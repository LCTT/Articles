[#]: subject: vcc | 给 UNIX 程序员的酷炫命令行聊天软件
[#]: via: (http://124.223.105.230/vcc-for-programmers.md)
[#]: author: intirain (intirain@163.com)
[#]: keywords: UNIX、聊天软件

vcc | 给 UNIX 程序员的酷炫命令行聊天软件

vcc （very cool chat）是一个 UNIX 上的命令行聊天软件。编写它一方面是为了在命令行上更好的聊天对话，一方面纯粹为了提升技术以及为了好玩。你可以在写代码的时候快速切换到 vcc 所在终端标签来聊天，这一直是我的一个梦想。

![使用 vcc 聊天](images/using-vcc.png)

### vcc 简介

vcc 是一个用 C 编写的运行在 UNIX 上的聊天软件。现在，（vcc 1.5.2，vccd 1.6.1）它支持用户登录、广播消息、创建 / 加入会话以及在会话中聊天。vcc 以及 vccd 采用 GPLv3 许可证，贡献者有我（intirain）以及 3swordman。

### 编译 vcc

vcc 分为客户端（即 vcc）以及服务端（即 vccd），托管在码云以及我自己的服务器上：

vcc: (https://gitee.com/intirain/vcc.git) 或者 (http://124.223.105.230:8080/intirain/vcc.git)

vccd: (https://gitee.com/intirain/vccd.git) 或者 (http://124.223.105.230:8080/intirain/vccd.git)

克隆到本地后，进入该目录并且运行 `make` 来编译。你会得到一个可执行文件 “vcc” 或者 “vccd”。对于客户端，如果你想连接到默认服务器（(124.223.105.230)），请运行：

```
vcc $ ./vcc 
```

它将会提示你输入用户名以及密码。如果你是游客，则可使用用户名和密码都是 guest 的游客账户。而如果你想要获得一个账号，请向我发邮件或者微信。



如果你想要连接到自己那有着 128 个核心、内存 1 TB、SSD 1 YB 的小型服务器，请运行：

```
vcc $ ./vcc 服务器 IP 地址 （当前还不支持域名）
```

登录成功之后即可开始聊天，只需要输入消息并且回车。过长的信息（大约 200 字节以上）会被截断。

对于服务端，如果你想要在 46 端口上启动 vccd 服务，请运行如下命令：

```
(需要 root 权限)
vccd $ sudo scripts/install	（如果你是第一次运行 vccd）
vccd $ sudo ./vccd
```

`scripts/install` 创建了文件 /etc/vccd-runtime 并且在其中保存了 vccd 运行时目录（ `$HOME/.vccd-runtime`）。运行时目录中保存着用户信息（当前还没有加入聊天记录功能），具体的位置是 users/用户名，每个文件是一个采用本机字节序的结构体。

上列命令只是启动了 vccd 守护进程（严格来说，它只能算一个运行于后台的程序，并不是 daemon），而没有创建任何用户。运行如下命令（不需要重启 vccd）：

```
vccd $ sudo scripts/adduser 用户名 密码
```

来创建一个新的用户。

### vcc 命令

vcc （客户端）支持命令，它的命令都以 “-” 开头。以下是当前 vcc （1.5.2）支持的所有命令，可以在一个连接到服务器的 vcc 客户端里输入 `-help` 查看：

```
#000 intirain$: -help
-help:	Show information about every message. 
-quit:	Disconnect to server and exit vcc. 
-ls:	List all users online now. 
-rate:	Change message refresh rate. 
-newse:	Create a new session. 
-currs:	Get current session id. 
-swtch:	Switch to another session. 
-lsse:	List all sessions created. 
#000 intirain$: 
```

以下是对每一条命令的详细描述：

| 命令名称 | 描述                             |
| -------- | -------------------------------- |
| -help    | 显示所有命令的帮助信息           |
| -quit    | 退出 vcc（相当于杀掉它）         |
| -ls      | 查看当前在线的所有用户           |
| -rate    | 设置刷新率 [^1]                  |
| -newse   | 新建一个会话 [^2]                |
| -currs   | 查看当前会话 id (sid)            |
| -swtch   | 加入另一个会话[^3]               |
| -lsse    | 查看服务器上所有已经创建了的会话 |

[^1]:刷新率：vcc 需要时序性的请求服务器来获取新消息。刷新率以赫兹为单位，默认为 1 HZ （即每秒刷新一次）。刷新率不得超过 1000 HZ （即每一毫秒刷新一次）。
[^2]:会话：vcc 中的群聊叫做会话。会话是由一或多个用户组成的聊天室，0号会话为大厅，所有人都在其中。会话无法退出。现在还不支持销毁一个会话，只能通过杀掉 vccd 来手动完成。
[^3]:会话不会退出，因此在加入另一会话后你仍在原先的会话中，仍会收到原先会话中的消息。但是你在加入另一会话后发送的消息，并不会被原先会话中的人收到。会话的创建者在会话创建后就移植在会话中。

vcc 的命令还没有加入命令行的支持，也就是说所有的命令的参数都需要你在键入该命令后手动输入。

### vcc 的输出信息

vcc 使用了 ANSI 的 CSI 转义，但没有检查当前终端对该标准的支持。如果你在类似串口的终端上运行 vcc，将会直接得到没有渲染的转义字符。vcc 的配色是可更改的，默认的配色主题在 `pretty.c `中定义，是一个 C 语言结构体，该结构体在 `include/vcc/pretty.h`中定义。

如果您觉得当前默认的配色不甚好看，完全可以在 `pretty.c` 中新建自己的配色，也可以复制并更改默认配色。我们也鼓励大家将自己认为好看的配色 PR 到码云仓库（而不要提交到我的服务器），我们在看到后会第一时间合并。

### 参与 vcc 的开发

vcc 以及 vccd 是以 GPL 发布的，并且是我们 inti 计划 [^6] 的其中之一，我们鼓励你参与开发改进它（据我所知，vccd 就包含这一个巨大的内存泄露问题，即使是登录失败或者是已经退出的用户也会占用 100 多字节的内存）。当然，开发是无偿的。

[^6]:inti：inti 是我们（其实也就俩人）创建的一个计划，目的是为了将优秀的自由软件（Linux 以及 GNU 计划）推向社会，并且也是为了方便 C 语言程序员的开发。编写的内容包括 GNU 计划不包含的软件或者库，语言为 C/C++。如果您也想加入，请联系邮箱。

vcc 刚刚开始开发，但它应该已经具备了一个基本聊天软件的功能。我们当然希望您能够使用它，并且将它推广给身边使用 UNIX 的人们。目前，我们正在稳定当前的版本。将来，它将会成为一个不错的社交软件，动态之类的高级功能也会实现。

> vcc 的 Windows 移植版本正在开发中，预计还需要几周的时间（毕竟我们只有周末才能编程 :-(）。移植之后的 vcc 应该不会保留配色（因为 Windows 并不支持 CSI 转义）。

---
作者简介: intirain，喜欢 UNIX、x86、RISC-V 上的 C。
-----
