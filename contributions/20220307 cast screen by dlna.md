如何在 Linux 下使用 DLNA 投屏
======

![](https://s3.bmp.ovh/imgs/2022/03/74888f6aed1cc398.png)

一般来说，安卓设备和 Windows 设备投屏使用的是 miracast 协议，但是该协议要求网卡支持 p2pwifi，而 Linux 下大多数网卡驱动不支持 p2pwifi。

于是我用 Python + FFmpeg  + DLNA 完成了一个在 Linux 下的投屏方案。这个方案的不足是延迟有点大。

### 设置

下面是如何实现：

先装这个 DLNA 库：

```
pip3 install dlna
```

然后用 `pactl` 查找“监视器信源”：

```
pactl list sinks
```

// 请在此处给出一个示例输出。

然后创建一个 CGI 脚本 `screen.flv`，首先是建立放置该脚本的目录：

```
mkdir screencast
mkdir screencast/cgi-bin
```

然后通过 `cat` 来直接创建该脚本：

```
cat <<eof>screencast/cgi-bin/screen.flv
#!/bin/bash
echo "Content-Type:video/x-flv"
echo

ffmpeg -f pulse -i <监视器信源>   -f x11grab -i :0  -vcodec h264_nvenc  pipe:.flv
eof
```

请用上面获得的监视器信源替换 `<监视器信源>`。

并为它设置可执行权限：

```
chmod +x screencast/cgi-bin/screen.flv 
```

注意：如果没有 Nvidia 显卡，或者是要使用其他的硬件加速，把编码方案 `h264_nvenc` 替换为相应的编码方案。不建议采用软解方式，延迟非常高。

### 投屏

需要投屏时，首先启动本地 Web 服务器：

```
cd screencast
python3 -m http.server --cgi 9999
```

然后，找到你的 DLNA 设备，然后把 `location` 后面的 URL 复制下来：

```
dlna device
```

// 请在此处给出一个示例输出

找到你的 Linux 电脑的局域网 IP 地址：

```
ip addr
```

// 请在此处给出一个示例输出

启动投屏方式如下：

```
dlna play -d <URL> http://<局域网 IP>:9999/cgi-bin/screen.flv
```

请相应替换其中的 `<URL>` 和 `<局域网 IP>` 参数，此处我替换后的命令是：

// 请在此处给出替换后的命令

然后在电视上设置接受投屏，各种电视设备设置投屏方式不同，请参照具体设备说明。

稍等片刻，视频就会出现在电视上了。投屏效果如下：

![](https://s3.bmp.ovh/imgs/2022/03/74888f6aed1cc398.png)

---
作者简介：

calvinlin：一个普通的深圳初中生

------

via: https://www.bilibili.com/read/cv15488839

作者：[calvinlin](https://space.bilibili.com/525982547)
审核：[wxy](https://github.com/wxy)

本文由贡献者投稿，采用 [CC-BY-SA 协议](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 发布，[Linux中国](https://linux.cn/) 荣誉推出

[1]: images/img001.png
[2]: 文内链接
[3]: images/img001.png
