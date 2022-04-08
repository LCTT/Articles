

使用howdy在debian/ubuntu下使用人脸登录
======

众所周知 deepin 在最近的更新中添加来人脸识别功能
那么有没有办法，在 其他 linux 系统中获得同样的功能？
答案是可以的


Howdy是什么？
"
Howdy提供Windows Hello™ Linux的风格认证。使用内置红外发射器和摄像头，结合面部识别来证明你是谁。
使用中央身份验证系统（ PAM ），这可以在任何需要密码的地方工作：登录、锁屏、sudo、su等等。
"(转自项目 README )


- debian/ubuntu 下安装方法：
打开https://github.com/boltgolt/howdy/releases
下载最新的deb文件
使用以下命令安装
```
sudo dpkg -i howdy_2.6.1.deb #（请将文件名代替为你下载的文件名）
sudo apt install --fix-broken#使用 --fix-broken安装缺失的依赖
```
- fedora 下安装方法：
```
sudo dnf copr enable principis/howdy
sudo dnf --refresh install howdy
```

(安装时 会自动下载依赖包和dlib的模型 请保证网络通畅)
安装后，运行`sudo howdy config`来编辑配置文件
将配置文件中的`device_path = /dev/xxxx`改成你的摄像头路径，它通常是`/dev/video0`
> 如果/dev下没有videoX设备 请检查摄像头驱动是否已经安装

现在 你可以尝试使用`sudo`或者重新登录来体验howdy
------

# 解决howdy在gnome锁屏界面不工作的问题
1. 复制 https://github.com/boltgolt/howdy/blob/caf244ce297d27d40168c40571b0fad6f7ee2596/src/compare.py
2. 使用文件内容代替 /lib/security/howdy/compare.py 
------
作者简介：

calvinlin：一个普通的深圳初中生。

------

via: https://www.bilibili.com/read/

作者：[calvinlin](https://space.bilibili.com/525982547)
编辑：[wxy](https://github.com/wxy)

本文由贡献者投稿至 [Linux 中国公开投稿计划](https://github.com/LCTT/Articles/)，采用 [CC-BY-SA 协议](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 发布，[Linux中国](https://linux.cn/) 荣誉推出
