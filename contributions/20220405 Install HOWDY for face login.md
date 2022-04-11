[#]: subject: "使用 Howdy 为 Linux 增加人脸识别登录"
[#]: via: " "
[#]: author: "calvinlin https://space.bilibili.com/525982547"
[#]: keywords: "Howdy 人脸识别 登录"
[#]: url: " "

使用 Howdy 为 Linux 增加人脸识别登录
======

最近，深度操作系统刚刚发布了 20.05，它添加的 [人脸识别功能](https://linux.cn/article-14425-1.html) 引来了社区的关注。

抛开人脸识别的准确度、可靠性，以及是否实用等问题，我们是否可以在其它的 Linux 系统中也获得人脸识别/解锁的的功能呢？

答案是可以的。这就是本文要介绍的 Howdy 提供的功能。

### Howdy 是什么？

![](https://camo.githubusercontent.com/fbefb0aacc2aa9fd473506dde1bc2dac77684633fd6db775de509a5b80f0c879/68747470733a2f2f626f6c74676f6c742e6e6c2f686f7764792f62616e6e65722e706e67)

[据该项目的说明](https://github.com/boltgolt/howdy/)：

> Howdy 为 Linux 提供了 Windows Hello™ 式的认证方式。使用内置红外发射器和摄像头，结合面部识别功能来证明你是谁。
> 
> 它使用中央身份验证系统（PAM），适用于任何需要密码的地方，如登录、锁屏、`sudo`、`su` 等等。

### 安装

对于 Ubuntu/Linux Mint，可以添加第三方仓库安装：

```
sudo add-apt-repository ppa:boltgolt/howdy
sudo apt update
sudo apt install howdy
```

对于 Debian Linux，请在 [发布页](https://github.com/boltgolt/howdy/releases) 下载 deb 安装包：

```
wget https://github.com/boltgolt/howdy/releases/download/v2.6.1/howdy_2.6.1.deb
```

使用以下命令安装：

```
sudo dpkg -i howdy_2.6.1.deb  # 请将文件名代替为你下载的文件名
sudo apt install --fix-broken # 使用 --fix-broken 安装缺失的依赖
```

对于 Fedora Linux，通过 COPR 仓库安装：

```
sudo dnf copr enable principis/howdy
sudo dnf --refresh install howdy
```

安装时，会自动下载依赖包和 dlib 的模型。请保证网络通畅。

对于 Arch Linux 和 openSUSE 请参照其 [仓库的说明](https://github.com/boltgolt/howdy/)。

### 配置

安装后，运行如下命令来编辑配置文件：

```
sudo howdy config
```

请将配置文件中的 `device_path = /dev/xxxx` 改成你的摄像头路径，它通常是 `/dev/video0`。

如果 `/dev` 下没有 `videoX`设备，请检查摄像头驱动是否已经安装。

Howdy 需要了解你的长相，以便以后能识别你。运行如下命令来添加一个面部模型：

```
sudo howdy add
```

如果没有出错，我们应该可以通过识别你的脸来运行 `sudo`。打开一个新的终端，运行 `sudo -i` 来看看它的运行情况。

### 排错

#### 解决 Howdy 在 GNOME 锁屏界面不工作的问题

复制如下文件：

`https://github.com/boltgolt/howdy/blob/caf244ce297d27d40168c40571b0fad6f7ee2596/src/compare.py`

代替 `/lib/security/howdy/compare.py` 即可。

------

作者简介：

calvinlin：一个普通的深圳初中生。

------

via: https://www.bilibili.com/read/

作者：[calvinlin](https://space.bilibili.com/525982547)
编辑：[wxy](https://github.com/wxy)

本文由贡献者投稿至 [Linux 中国公开投稿计划](https://github.com/LCTT/Articles/)，采用 [CC-BY-SA 协议](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 发布，[Linux中国](https://linux.cn/) 荣誉推出
