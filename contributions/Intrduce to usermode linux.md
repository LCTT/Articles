#用户态linux简明教程
![](https://s3.bmp.ovh/imgs/2022/03/164eccd6da50e10d.png)
用户态linux是什么？
一种可以在用户态运行的linux内核

有什么用？
进行内核隔离，替代qemu/bochs调试linux内核，在低性能设备上代替kvm进行虚拟化

缺陷:
不支持arm架构以及多核

怎么做？

Step1:
编译linux内核
git下载源代码
`git clone --depth 1 https://mirrors.tuna.tsinghua.edu.cn/git/linux.git` (这里使用了清华大学的镜像站，kernel.org也是ok的)
编译
```bash
cd linux
export ARCH=um#非常重要 设置架构为用户态
make defcongig
make -j8
```

```
...
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


现在 你获得了一个vmlinux文件 这个vmlinux和正常内核的区别就是这个vmlinux可以在用户态运行

先别着急启动，先来准备rootfs

一下内容以debian为例

先安装debootstrap
```bash
sudo apt install  debootstrap
```
然后构建rootfs
`sudo su`(以下命令皆需要root权限)
```
# dd if=/dev/zero of=rootfs seek=2G`#创建一个2GB的空rootfs文件
 2000000000字节（2 GB，2 GB）已复制，0.137825 s，570 MB/s`

# mkfs.ext4 rootfs#格式化为ext4格式
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


# mount rootfs /mnt
# cd /mnt
# debootstrap sid ./ https://mirrors.tuna.tsinghua.edu.cn/debian#创建debian rootfs
 I: Configuring python-central... 
 I: Configuring ubuntu-minimal... 
 I: Configuring libc-bin... 
 I: Configuring initramfs-tools... 
 I: Base system installed successfully.
# chroot ./
#passwd#设置root密码
# umount /mnt
# exit
# sudo chown `whomi` rootfs


然后就可以启动了
启动只需要一行命令:
`screen ./vmlinux mem=1G root=/dev/udba udb0=rootfs con=null con0=null,fd:2 con1=fd:0,fd:1 #启动一个拥有1gb内存的用户态linux`


---
作者简介：

calvinlin：一个普通的深圳初中生。

------

via: https://www.bilibili.com/read/cv15626523

作者：[calvinlin](https://space.bilibili.com/525982547)
