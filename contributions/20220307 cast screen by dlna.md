Linux 下使用 DLNA 投屏
======



![](https://s3.bmp.ovh/imgs/2022/03/74888f6aed1cc398.png)


一般来说 安卓设备/windows设备投屏用的是miracast协议，但是miracast要求网卡支持p2pwifi 但是在linux下大多数网卡驱动不支持p2pwifi。

于是用python+ffmpeg+dlna制作了这个延迟有点大的投屏方案

先装这个dlna库
` pip3 install dlna`
然后
```
mkdir screencast
mkdir screencast/cgi-bin
cat <<eof>screencast/cgi-bin/screen.flv
#!/bin/bash
echo "Content-Type:video/x-flv"
echo

ffmpeg -f pulse -i  Speeker name here   -f x11grab -i :0  -vcodec h264_nvenc  pipe:.flv
eof
chmod +x screencast/cgi-bin/screen.flv 
```

写完cgi后 要对这个脚本进行一点修改

将 Speeker name here 替换成在pactl list sinks 中找到的”监视器信源”

如果没有nvidia显卡 或者是要使用其他的硬件加速 把H264_nvenc 替换为其他的 不建议软解 延迟非常高 还卡

需要投屏时 
```
cd screencast
python3 -m http.server --cgi 9999

dlna device （找到你的dlna设备，然后把location后面的url复制下来）
dlna play -d 上一步的url http://你的lan ip(不是localhost):9999/cgi-bin/screen.flv
```
![](https://s3.bmp.ovh/imgs/2022/03/74888f6aed1cc398.png)
稍等片刻 视频就会出现在电视上了


-
By 一个普通的深圳初中生

------

via: https://www.bilibili.com/read/cv15488839

作者：[calvinlin](https://space.bilibili.com/525982547)
审核：[wxy](https://github.com/wxy)

本文由贡献者投稿，采用 [CC-BY-SA 协议](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 发布，[Linux中国](https://linux.cn/) 荣誉推出

[1]: images/img001.png
[2]: 文内链接
[3]: images/img001.png
