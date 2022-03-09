[#]: subject: "怎么开始你的第一次打包？"
[#]: author: "PokerFace128  GitHub https://github.com/pokerface128  Gitee  https://gitee.com/pokerface128  BiliBili  https://space.bilibili.com/36777210 Mail PokerFace128@qq.com"

### 怎么开始你的第一次打包？

 >太复杂的包咱们打不来，咱们先从最简单的壁纸包开始打起。

![](https://s3.bmp.ovh/imgs/2022/03/349fee489f974553.png)

### 背景介绍：

![](https://ftp.bmp.ovh/imgs/2019/09/3101e88de6e8b45a.jpg)

崩坏3，一个我很喜欢玩的游戏。但它们不支持Linux平台。我只好把这些壁纸进行打包，以此纪念和女武神们并肩战斗过的时光。

### 咱们开始准备工作吧

先准备如下工具：

wget  tar dh-make debmake lintian
```
$ sudo apt install wget tar dh-make debmake lintian
```
先建立文件夹
```
$ mkdir -p honkai-impact3-0.1/usr/share/background/honkai-impact3`
```
更换壁纸的时候你应该注意到了，通常壁纸的存放位置都是在 background 这。

你也可以用你手里的照片来打包。演示图片均来自于崩坏3官网。

由于我觉得一个个下载太累了，于是乎就写了个脚本来批量下壁纸。
```
#!/bin/sh
echo 一次牛逼的攻击
wget -c https://x/01.jpg
```
就这样壁纸就很快下好了。

![](https://s3.bmp.ovh/imgs/2022/03/5fd5926b55fa0862.png)

#### 就这样开始打包吧

然后 退回到上一个目录里进行打包

```
$ cd ..
$ tar -cvzf honkai-impact3-0.1.tar.gz honkai-impact3-0.1/usr/share/background/honkai-impact3
```

压缩包就这样打好了。但还没完。

我们还得设置两个变量，这样维护工具就可以正确识别维护者信息了。
```
$ cat >>~/.bashrc <<EOF
DEBEMAIL="bronya_zaychik@st_freya_academy.edu"
DEBFULLNAME="Bronya Zaychik"
export DEBEMAIL DEBFULLNAME
EOF`
$ . ~/.bashrc
```
DEBEMAIL 写你的邮箱地址
DEBFULLNAME 写你维护者的名字

#### 初始化软件包 

```
$ cd honkai-impact3-0.1 
$ dh_make -f ../honkai-impact3-0.1.tar.gz
Type of package: (single, indep, library, python)
[s/i/l/p]?
Maintainer Name     : Bronya Zaychik
Email-Address       : bronya_zaychik@st_freya_academy.edu
Date                : Wed, 02 Feb 2022 07:00:28 +0000
Package Name        : honkai-impact3
Version             : 0.1
License             : blank
Package Type        : library
Are the details correct? [Y/n/q]

```

在初始化完成之后，你会看到如下文件

```
$ ls -f ../
honkai-impact3-0.1/
honkai-impact3-0.1.tar.gz
honkai-impact3_0.1.orig.tar.gz
```

而debian文件夹里却有了很多模板文件，在一阵怒砍之后，只留下如下文件。
```
$ ls
source/
changelog
control
copyright
rules
```

changlog 是用来记录版本更新内容

例如这样：
```
honkai-impact3-background (0.1-1) unstable; urgency=medium

  * 2020.8.17 首次打包完成
  * 2022.2.2  重新打包

 -- Bronya Zaychik <bronya_zaychik@st_freya_academy.edu> Wed, 02 Feb 2022 07:20:00 +0000

honkai-impact3-background (0.1-1) unstable; urgency=medium

  * Initial release 

 -- Bronya Zaychik <bronya_zaychik@st_freya_academy.edu> Wed, 02 Feb 2022 07:00:28 +0000

```

control  壁纸包的版本信息

```
Package: honkai-impact3-background
Version: 0.1-1
Architecture: all
Maintainer: Bronya Zaychik <bronya_zaychik@st_freya_academy.edu>
Section: x11
Priority: optional
Homepage: https://gitee.com/PokerFace128/K423_Lab_Soft
Description: This is the game wallpaper of the HokaiImpact3.
 TECH OTAKUS SAVE THE WORLD
```

第1-2行是包名和版本号
第3行是可以编译本二进制包的体系结构，通常文本、图像、或解释型语言脚本 生成的二进制包都用 Architecture: all
第4行是维护者信息
第5行是分类，这里我们选择为x11  x11 为不属于其他分类的为 X11 程序
第6行是优先级，这个为常规优先级。
第7行是你的个人主页，GitHub、Gitee，甚至是你的BiliBili主页都可以。
第8行是对这个软件包的描述 
第9行建议写点什么上去，这样在用 lintian 检查的时候就不会空了。

copyright 版权信息

额，版权这事我还在想办法联系米哈游。所以就为什么就没填。

#### 开始打包

只需一个命令，就可轻松打包。

```
$ dpkg-buildpackage -us -uc
```

啪的一下，很快啊。一个壁纸包就这样打好了。

```
$ ls -F ../
honkai-impact3-0.1/                   
honkai-impact3_0.1-1_amd64.changes  
honkai-impact3_0.1-1.debian.tar.xz  
honkai-impact3_0.1.orig.tar.gz
honkai-impact3_0.1-1_amd64.buildinfo  
honkai-impact3_0.1-1_amd64.deb      
honkai-impact3_0.1-1.dsc            
honkai-impact3-0.1.tar.gz
```

接下来用 lintian 检查

```
$ lintian honkai-impact3_0.1-1_amd64.deb   

E: honkai-impact3-background: copyright-contains-dh_make-todo-boilerplate
E: honkai-impact3-background: helper-templates-in-copyright
W: honkai-impact3-background: copyright-has-url-from-dh_make-boilerplate

```
 
 这里显示我没填copyright文件
 
 打包好之后就像这样
 
 ![](https://s3.bmp.ovh/imgs/2022/03/349fee489f974553.png)
 
 ---
 作者简介：
 
 PokerFace，一个会空中劈叉的老舰长。
 ------

 
 
