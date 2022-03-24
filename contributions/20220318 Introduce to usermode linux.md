[#]: subject: "如何构建用户态 Linux"
[#]: via: "https://www.bilibili.com/read/cv15626523"
[#]: author: "calvinlin https://space.bilibili.com/525982547"
[#]: keywords: "内核 用户态"
[#]: url: " "

# 如何构建用户态 Linux

![](https://s3.bmp.ovh/imgs/2022/03/164eccd6da50e10d.png)

“用户态 Linux” 是什么？它是一种可以在用户态运行的 Linux 内核。（用户态是什么，这里就不解释了）

它有什么用？它用于内核隔离，替代 qemu/bochs 来调试 Linux 内核，可以在低性能设备上代替 kvm 进行虚拟化。

它也存在一些缺陷，比如不支持 ARM 架构以及多核系统。

### 如何构建一个用户态的 Linux 内核

#### 编译 Linux 内核

首先通过 `git` 下载 Linux 内核源代码：

```
git clone --depth 1 https://mirrors.tuna.tsinghua.edu.cn/git/linux.git
```

（这里使用了清华大学的镜像站，kernel.org 也是可以的。）

然后采用如下步骤编译它：

```
$ cd linux
$ export ARCH=um # 非常重要 设置架构为用户态
$ make defcongig
$ make -j8

 LD      .tmp_vmlinux.kallsyms1
 KSYMS   .tmp_vmlinux.kallsyms1.S
 AS      .tmp_vmlinux.kallsyms1.S
 LD      .tmp_vmlinux.kallsyms2
 KSYMS   .tmp_vmlinux.kallsyms2.S
 AS      .tmp_vmlinux.kallsyms2.S
 LD      vmlinux
 SYSMAP  System.map
 LINK linux
 MODPOST modules-only.symvers
 GEN     Module.symvers
```

经过漫长的编译之后，你获得了一个 `vmlinux` 文件。它和正常内核的区别就是这个 `vmlinux` 可以在用户态运行。

### 准备 rootfs

先别着急启动，先来准备 rootfs。

以下内容以 Debian Linux 为例。

首先安装 `debootstrap` 软件包：

```
sudo apt install  debootstrap
```

然后构建 rootfs：

```
$ sudo su # 以下命令皆需要 root 权限

# dd if=/dev/zero of=rootfs seek=2G # 创建一个 2GB 大小的空 rootfs 文件
 2000000000字节（2 GB，2 GB）已复制，0.137825 s，570 MB/s`

# mkfs.ext4 rootfs # 将其格式化为 ext4 格式
 mke2fs 1.46.5 (30-Dec-2021)
 Discarding device blocks: done                            
 Creating filesystem with 76748 1k blocks and 19200 inodes
 Filesystem UUID: 9dc7f1f6-8b16-4c64-9e22-94ede327c532
 Superblock backups stored on blocks: 
  	8193, 24577, 40961, 57345, 73729

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done 

# mount rootfs /mnt # 挂载
# cd /mnt
# debootstrap sid ./ https://mirrors.tuna.tsinghua.edu.cn/debian # 创建 Debian 的 rootfs
 I: Configuring python-central... 
 I: Configuring ubuntu-minimal... 
 I: Configuring libc-bin... 
 I: Configuring initramfs-tools... 
 I: Base system installed successfully.

# chroot ./ # 进入新的 rootfs

# passwd # 设置 root 密码 
 New password: 
 Retype new password: 
# umount /mnt
# exit
# sudo chown `whomi` rootfs
```

然后就可以启动了，只需要一行命令：

```
screen ./vmlinux mem=1G root=/dev/root rootfstype=hostfs hostfs=./rootfs  con=null con0=null,fd:2 con1=fd:0,fd:1
```

![][1]
启动后，使用你前面设置的root用户登录
便可以进入到用户态linux容器中了
有别于`docker` 这个容器的内核和宿主的内核是隔离的
可以使用这个容器作为一个调试内核的工具 如
```bash
echo 1 > /proc/sys/kernel/sysrq
echo c > /proc/sysrq-trigger
```
手动触发一个kernel panic
延伸阅读：

- https://www.kernel.org/doc/html/latest/virt/uml/user_mode_linux_howto_v2.html
- https://wiki.archlinux.org/title/User-mode_Linux

作者简介：

calvinlin：一个普通的深圳初中生。

------

via: https://www.bilibili.com/read/cv15626523

作者：[calvinlin](https://space.bilibili.com/525982547)
编辑：[wxy](https://github.com/wxy)

本文由贡献者投稿至 [Linux 中国公开投稿计划](https://github.com/LCTT/Articles/)，采用 [CC-BY-SA 协议](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 发布，[Linux中国](https://linux.cn/) 荣誉推出

[1]: https://s3.bmp.ovh/imgs/2022/03/0e586473dc1acdf1.png
