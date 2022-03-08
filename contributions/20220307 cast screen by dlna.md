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

然后用 `pactl` 查找"监视器信源" 或 "Monitor Source"：

```
pactl list sinks
```
如
```Sink #0
	State: RUNNING
	Name: alsa_output.pci-0000_05_00.6.HiFi__hw_Generic_1__sink
	Description: Family 17h (Models 10h-1fh) HD Audio Controller Speaker + Headphones
	Driver: module-alsa-card.c
	Sample Specification: s16le 2ch 44100Hz
	Channel Map: front-left,front-right
	Owner Module: 9
	Mute: no
	Volume: front-left: 53814 /  82% / -5.14 dB,   front-right: 53814 /  82% / -5.14 dB
	        balance 0.00
	Base Volume: 65536 / 100% / 0.00 dB
	Monitor Source: alsa_output.pci-0000_05_00.6.HiFi__hw_Generic_1__sink.monitor
	Latency: 16676 usec, configured 16000 us...

```


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
python3 -m http.server --cgi 9999&
```

然后，找到你的 DLNA 设备，然后把 `location` 后面的 URL 复制下来：

```
dlna device
```
如
```
=> Device 1:
{
    "location": "http://192.168.3.118:1528/",
    "host": "192.168.3.118",
    "friendly_name": "Kodi",
...

```

找到你的 Linux 电脑的局域网 IP 地址：

```
ip addr
```
如
```
3: wlp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 74:4c:a1:82:2e:3f brd ff:ff:ff:ff:ff:ff
    inet 192.168.3.117/24 brd 192.168.3.255 scope global dynamic noprefixroute wlp2s0
       valid_lft 58283sec preferred_lft 58283sec
    inet6 240e:3b3:2ee3:9530:d005:e492:6243:9/128 scope global dynamic noprefixroute 
       valid_lft 6738sec preferred_lft 3138sec
    inet6 240e:3b3:2ee3:9539:f289:6043:c56a:4e7b/64 scope global dynamic noprefixroute 
       valid_lft 7189sec preferred_lft 3589sec
    inet6 240e:3b3:2ee3:9539:3714:eaf0:c549:b8c9/64 scope global dynamic mngtmpaddr noprefixroute 
       valid_lft 7188sec preferred_lft 3588sec
    inet6 fe80::c746:2540:ab7b:20aa/64 scope link 
       valid_lft forever preferred_lft forever
    inet6 fe80::3543:2637:e0fc:3630/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
`
```
启动投屏方式如下：

```
dlna play -d <URL> http://<局域网 IP>:9999/cgi-bin/screen.flv
```

 
请相应替换其中的 `<URL>` 和 `<局域网 IP>` 参数，此处我替换后的命令是：
```
dlna play -d http://192.168.3.118:1528/ http://192.168.3.117:9999/cgi-bin/screen.flv
```

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
