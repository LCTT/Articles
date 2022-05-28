[#]: author: "dongdigua"
[#]: via: "dongdigua.github.io/p2p_chat"
[#]: keywords: "elixir erlang p2p rsa 通讯 开源 实验性"

使用 elixir 写一个简易点对点加密聊天软件
===
![题图](https://dongdigua.github.io/elixir_image.jpg)

> 尝试用 elixir 写一个 p2p 加密聊天软件

## 使用到的技术
- UDP socket
- 进程(这里指 beam 虚拟机的进程)
- GenServer
- ETS 键值存储(Erlang Term Storage)[ETS 教程](https://elixirschool.com/zh-hans/lessons/storage/ets)
- escript 编译成可执行文件
- rsa 非对称加密
- UDP 打洞<br>
整个思路来源都是从这两个视频来的:<br>
[with netcat](https://www.youtube.com/watch?v=s_-UCmuiYW8) & [with python](https://www.youtube.com/watch?v=IbzGL_tjmv4)<br>
我的理解就是通过发送 UDP 包打开一个端口来让远程电脑能知道你的端口映射到了公网 IP 的哪个端口，<br>
然后将两个需要发消息的客户端相互告诉对方各自的公网 IP 以及映射到的端口，就能实现 p2p 通信。<br>

## 大体架构
客户端使用 GenServer 来实现后端接口和网络通信，在 CLI 模块处理用户输入调用 GenServer.<br>

服务端可以很简单，就是收到两个 IP 然后相互发送对方的地址让客户端能够相互通信，<br>
但是为了能够接受多对客户端以及非阻塞等待客户端，就用 ETS 存储客户端的信息，<br>
为了让客户端不乱配对，就需要增加一个注册功能，也使用 ETS 实现。<br>

## 客户端实现
内容比较多，所以我不会讲的很全, 代码不会都放出来<br>
项目目录大概是这样
```sh
├── client
│   ├── lib
│   │   ├── client
│   │   │   ├── cli.ex          # 和用户交互, 调用 GenServer 后端, escript 入口点
│   │   │   ├── connect.ex      # 处理与服务器发送和接受的二进制字符串
│   │   │   ├── crypto.ex       # rsa加密解密
│   │   │   └── register.ex     # 仅生成注册时需要发送的二进制字符串
│   │   └── client.ex           # 客户端核心程序, 包含 GenServer 和 socket 通信
│   ├── mix.exs
│   └── mix.lock
```
注意这里只在核心程序处理 socket，cli 模块处理用户交互，使项目分层化

### escript
客户端要编译成可执行文件，要在 `mix.exs` 里加入 `escript`
```elixir
defmodule Client.MixProject do
  use Mix.Project

  def project do
    [
      app: :client,
      version: "0.1.0",
      elixir: "~> 1.13",
      start_permanent: Mix.env() == :prod,
      deps: deps(),
      escript: escript()
    ]
  end

  defp escript do
    [main_module: Client.CLI]
  end

  def application do
    [
      extra_applications: [:logger, :crypto]
    ]
  end

  defp deps do
    []
  end
end
```
`main_module` 指定了程序的入口点main函数，`extra_applications` 加入 erlang 库 `:crypto` 因为后续需要使用加密

### GenServer 和 socket
先是定义了两个结构体，一个用于存储 peer 的信息，一个存储客户端的信息(peer 键是 peer 结构体)<br>
然后是一堆常量，服务器可以改成你的 128 核心 1TB 内存 1EB 固态硬盘的小型服务器的地址，`key_integer` 是客户端的密钥生成器用的<br>
其实可以在 `config.exs` 或者用 json 来配置
```elixir
defmodule Client do
  defmodule Peer do
    defstruct [:name, :addr, :port, :pub_key]
  end
  @serveraddr {127, 0, 0, 1}
  @serverpt 1234
  @key_integer <<3>>   #should be a valid rsa key_integer
  use GenServer
  alias Client.Conn
  defstruct [:socket, :name, :priv_key, peer: nil]
```

GenServer 初始化，UDP 打开一个端口(从命令行参数传进来)
```elixir
  def start_link(port) do
    {:ok, socket} = :gen_udp.open(port, [:binary, active: false])
    GenServer.start_link(Client, %Client{socket: socket}, name: :client)
  end
```

然后是 GenServer 的回调函数，实现了必要的接口
```elixir
  def init(%Client{} = client), do: {:ok, client}

  def handle_call({:register, sesstoken, passwd}, _from, client) do
    :ok = :gen_udp.send(client.socket, @serveraddr, @serverpt, Client.Reg.register(sesstoken, passwd))
    {:reply, :gen_udp.recv(client.socket, 0), client}
  end

  def handle_call({:find, name, sesstoken, passwd}, _from, client) do
    :ok = :gen_udp.send(client.socket, @serveraddr, @serverpt, Conn.find_peer(name, sesstoken, passwd))
    case :gen_udp.recv(client.socket, 0) |> Conn.parse_peer() do
      {:ok, peer} ->
        {:reply, peer, %{client | name: name, peer: peer}}
      {:error, reason} ->
        {:reply, reason, client}
    end
  end

  def handle_call(:key, _from, client) do
    {pub, priv} = Client.Crypto.generate_key(@key_integer)
    :ok = :gen_udp.send(client.socket, client.peer.addr, client.peer.port, hd(tl(pub)))
    {:ok, {_addr, _port, peer_pub}} = :gen_udp.recv(client.socket, 0)
    full_peer_pub = [@key_integer, peer_pub]
    {:reply, full_peer_pub,
      %{client | priv_key: priv, peer: %{client.peer | pub_key: full_peer_pub}}
    }
  end

  def handle_call({:chat, text}, _from, client) do
    encrypted = Client.Crypto.encrypt(text, client.peer.pub_key)
    :ok = :gen_udp.send(client.socket, client.peer.addr, client.peer.port, encrypted)
    {:reply, encrypted, client}
  end

  def handle_cast(:recv, client) do
    spawn(fn -> recv_loop(client.socket, client.priv_key) end)
    {:noreply, client}
  end
```

最后是接收消息(和解密)
```elixir
  defp recv_loop(socket, priv_key) do
    {:ok, {_ip, _port, data}} = :gen_udp.recv(socket, 0)
    decrypted = Client.Crypto.decrypt(data, priv_key)
    IO.puts(IO.ANSI.clear_line() <> "\r" <> IO.ANSI.cyan() <> "received: #{inspect(decrypted)}" <> IO.ANSI.reset())
    recv_loop(socket, priv_key)
  end
end
```

### 用户交互 CLI
首先使用 `OptionParser` 解析命令行参数，如果解析成功就启动 GenServer
```elixir
def main(args \\ []) do
  {opts, args, invalid} = OptionParser.parse(args, strict: [
    port: :integer,
  ])
  if opts == [] or (args != [] && invalid != []) do
    IO.puts "usage: client --port <port>"
  else
    Client.start_link(opts[:port])
    main_cli()
  end
end
```
然后 `main_cli()` 就处理用户的输入,<br>
然后先向服务器发起 find peer 请求(需要身份验证)，找到 peer 之后交换密钥然后就可以发消息了<br>
这里主要说一下输入密码的部分:<br>
erlang 的 `:io.get_password()` 函数在 mix 中不管用，所以就需要自己写一个清空用户输入的小东西
```elixir
def gets_passwd(prompt) do
  pid = spawn(fn -> clear_input(prompt) end)
  value = IO.gets("")
  send(pid, :stop)
  value
end

def clear_input(prompt) do
  IO.write(IO.ANSI.clear_line() <> "\r" <> prompt)   #\r用于回到行首
  :timer.sleep(10)
  receive do
    :stop -> IO.write("\r")
  after
    0 -> clear_input(prompt)
 end
end
```


## 服务端实现
服务端仅作为暴露客户端连接和将两个客户端牵手的作用，当然为了区分还要有注册功能<br>
项目目录大概是这样
```sh
└── server
    ├── Dockerfile
    ├── lib
    │   ├── server
    │   │   ├── application.ex
    │   │   ├── connection.ex
    │   │   └── register.ex
    │   └── server.ex
    └── mix.exs
```
这里用 docker 方便部署
### 应用程序监视器
因为是服务端嘛，鬼知道用户或其它东西会整出什么么蛾子，所以使用应用程序监视器在程序挂掉时重启进程很有必要<br>
这也是 erlang/OTP 的 let it crash 思想的一种体现
```elixir
defmodule Server.Application do
  use Application
  @impl true
  def start(_type, _args) do
    children = [
      %{
        id: Server,
        start: {Server, :start, []}
      }]
    opts = [strategy: :one_for_one, name: Server.Supervisor]
    Supervisor.start_link(children, opts)
  end
end
```
### socket
首先还是定义一个结构体用于存储每个用户的数据<br>
启动两个数据库，打开 UDP 端口
```elixir
defmodule Server do
  defmodule UserData do
    defstruct [:addr, :port, :name, :sesstoken, :passwd]
  end
  import Server.Conn
  @serverpt 1234

  def start do
    Server.Reg.new()
    Server.Conn.table_new()
    {:ok, socket} = :gen_udp.open(@serverpt, [:binary, active: false])
    serve(socket)
  end
```

根据收到的消息头部的不同选择处理不同的内容(其实这里的区分判断应该写全，但能用就行呗)<br>
然后递归调用自己形成循环(elixir 有尾递归优化, 所以这样递归不会有性能问题)
```elixir
  def serve(socket) do
    case :gen_udp.recv(socket, 0) |> IO.inspect() do
      {:ok, {_ip, _port, <<?F, _rest::binary>>} = data} ->
        #FROM:foo;SESSTOKEN:test;PASSWD:hash
        spawn(fn -> handle_connection(socket, data) end)
      {:ok, {_ip, _port, <<?R, _rest::binary>>} = data} ->
        #REGISTER:token:passwd
        spawn(fn -> handle_register(socket, data) end)
      {:error, error} ->
        IO.inspect(error)
      _ ->
        nil
    end
    serve(socket)
  end

  def handle_connection(socket, {ip, port, bin}) do
    [_, name, sesstoken, passwd] = Regex.run(~r/FROM:(\w+);SESSTOKEN:(\w+);PASSWD:(\w+)/, bin)
    user_data = %UserData{
      addr: ip,
      port: port,
      name: name,
      sesstoken: sesstoken,
      passwd: passwd
    }

    case find_peer(user_data) do
      nil -> add_peer(user_data)
      { {peer0, msg0}, {peer1, msg1} } ->
        :gen_udp.send(socket, peer0, msg0)
        :gen_udp.send(socket, peer1, msg1)
    end
  end

  def handle_register(socket, {ip, port, bin}) do
    [_, token, passwd] = Regex.run(~r/REGISTER:(\w+):(\w+)/, bin)
    if Server.Reg.register_session(token, passwd) do
      :gen_udp.send(socket, ip, port, "successful!")
    else
      :gen_udp.send(socket, ip, port, "exists")
    end
  end
end
```

## 使用方式
### 客户端
```sh
mix escript.build
./client --port 2000
```
输入 register 注册一个聊天，输入 find 连接另一个使用相同用户名密码 find 的人
### 服务端
```sh
mix run --no-halt
```


---
作者简介: 一个高中生<br>
B站: 董地瓜(MineCraft红石生存，高压电，编程，GeoGebra 区up猪)

---

