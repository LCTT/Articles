[#]: subject: "在启用安全启动的 Fedora 中安装 Nvidia 驱动"
[#]: via: "https://www.insidentally.com/articles/000034/"
[#]: author: "insidentally https://www.insidentally.com"
[#]: keywords: "Nvidia驱动 安全启动 内核模块签名"
[#]: url: "发布后链接，由发布人填写"

在启用安全启动的 Fedora 中安装英伟达驱动
======

> 本文介绍如何在 Fedora 中自动签署英伟达内核模块。

[//]: # (文章内图片请自行放置于图床，并引用)

![在启用安全启动的 Fedora 中安装英伟达驱动][a]

[//]: # (文章内章节以 `###` 标题为一级标题，子标题以此类推)

### 本文缘起

现在新出厂的电脑 UEFI 会默认开启<ruby>安全启动<rt>**Secure Boot**</rt></ruby>，安全启动的作用是防止恶意软件侵入。当电脑引导器被病毒修改之后，它会给出提醒并拒绝启动，避免可能带来的进一步损失。不过它同样会阻止一些未经微软签名的 Linux 内核启动运行。虽然可以直接选择在主板设置中关闭安全启动来解决一系列麻烦，但就在近期微软公布的 Windows 11 最低硬件标准中可以看到，安全启动被微软看的越来越重。如果你的电脑是 Windows + Linux 双系统，最好还是让 Linux 本身支持安全启动。

而最好用的发行版之一 Fedora 更热衷于开源驱动。Fedora 其本身是支持安全启动的，但是当你通过 rpmfusion 安装官方的英伟达驱动，会造成这些驱动的内核模块未签名。在 Linux 启动过程中因为安全启动校验签名，会阻止加载这些模块，进而无法正常驱动显卡。用过 Ubuntu 的伙伴们应该知道，在安全启动开启的情况下 ，Ubuntu 安装程序会自动用自签密钥签名英伟达驱动内核模块，并在开机过程中自动将该自签密钥导入 MOK List（安全启动机器主人信任密钥列表）。而 Fedora 只会保证自身内核签名有效，对 rpmfusion 中的第三方内核模块签名问题不予理会，导致无法正常加载英伟达驱动。

本文介绍如何在 Fedora 中自动签署英伟达内核模块

### 准备工作

在 Fedora 36 之前，要像 Ubuntu 那样自动签署内核模块有点困难。但从这个版本开始，您只需几个简单的步骤就能做到。

在开始之前，让我们先确认一些前提条件已经满足：

1. 已启用安全启动；
2. 尚未安装英伟达驱动程序（**非常关键**，如果你已经安装了专有的英伟达驱动，可能需要重装系统才行）；
3. 以及安装了 Fedora 36 及以上版本。

本指南主要参考了以下资料：

1. [rpmfusion 的官方英伟达文档][1]
2. [rpmfusion 的官方安全启动文档][2]
3. [Andrei Nevedomskii 的博客教程][3]

不满足于本文的朋友可以阅读上述资料进一步深入研究。

### 具体步骤

#### 1. 安装自动签名所需的工具

```
sudo dnf install kmodtool akmods mokutil openssl
```

#### 2. 生成签名密钥

```
sudo kmodgenca -a
```

#### 3. 启动密钥注册

这将使 Linux 内核信任使用你的密钥签名的驱动程序 

```
sudo mokutil --import /etc/pki/akmods/certs/public_key.der
``` 

你会被要求输入一个密码。请记住这个密码，在第五步中还需要再次使用。

#### 4. 重启以注册密钥 

```
sudo reboot
```

#### 5. 注册密钥

重启后，你将看到蓝色的 MOK 管理器界面，不要惊惶，按照以下步骤注册密钥。

>如果你曾在启用安全启动的 Ubuntu 中安装过英伟达驱动程序，你可能见过这个界面。

1. 首先选择“Enroll MOK”注册 MOK。
2. 然后选择“Continue”。
3. 点击“Yes”并输入步骤 3 中的密码并回车（**密码不会在输入框中显示，输入密码直接回车就好了**）。
4. 然后选择“reboot”，设备将再次重启。

#### 6. 安装英伟达驱动程序

现在只需正常安装英伟达驱动程序。

```
sudo dnf install gcc kernel-headers kernel-devel akmod-nvidia xorg-x11-drv-nvidia xorg-x11-drv-nvidia-libs
```

#### 7. 确保内核模块已编译

```
sudo akmods --force
```

#### 8. 确保启动镜像也已更新

```
sudo dracut --force
```

#### 9. 重启设备

```
sudo reboot
```

### 确认是否成功

重启完成后，输入以下命令确认驱动是否加载：

```
lsmod | grep -i nvidia
```

如果有类似以下的输出，恭喜你，一切顺利，一切就绪！



``` shell
$ lsmod | grep -i nvidia

nvidia_drm             94208  2
nvidia_modeset       1560576  2 nvidia_drm
nvidia_uvm           3493888  0
nvidia              62517248  118 nvidia_uvm,nvidia_modeset
video                  73728  3 asus_wmi,i915,nvidia_modeset
```

现在，你可以愉快的在开启安全启动的情况下使用 Nvidia 显卡了。

希望本文能够帮助到你。

---

作者简介：一个喜欢瞎鼓捣的外科医生

------

via: https://www.insidentally.com/articles/000034/

作者：[insidentally](https://www.insidentally.com)
编辑：[wxy](https://github.com/wxy)

本文由贡献者投稿至 [Linux 中国公开投稿计划](https://github.com/LCTT/Articles/)，采用 [CC-BY-SA 协议](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 发布，[Linux中国](https://linux.cn/) 荣誉推出

[a]: https://www.insidentally.com/images/000034/00.png

[1]: https://rpmfusion.org/Howto/NVIDIA
[2]: https://rpmfusion.org/Howto/Secure%20Boot
[3]: https://blog.monosoul.dev/2022/05/17/automatically-sign-nvidia-kernel-module-in-fedora-36/
