# 如何在pcDuino8 Uno上安装FFMPEG
手头上有一个 JVS-H210 的 IP 摄像头，可以输出 rtsp 视频流。pcDuino8 Uno 上如何去读取rtsp视频流呢？尝试了下面的方法。

![JVS-H210](http://en.jovision.com/UpLoadFiles/image/2015040315294235.png)

## 尝试

**1.  vlc**
尝试了多次，一直说URL错误，放弃

**2. live555**
同样尝试，startRSTP时也是说URL错误，放弃

**3. ffmpeg**
最后选择用ffmpeg实现rtsp视频流的接收。

## 安装FFMPEG
### 1. 安装H264的支持包
```shell
cd /usr/src
git clone git://git.videolan.org/x264
cd x264
./configure --host=arm-unknown-linux-gnueabi --enable-static --disable-opencl
make
sudo make install
```
### 2. 安装其他函数库/包（可选）
比如安装支持mp3格式的函数包
```shell
wget http://downloads.sourceforge.net/project/lame/lame/3.99/lame-3.99.tar.gz
tar xzvf lame-3.99.tar.gz
cd lame-3.99
./configure
make
make install
```

### 3. 编译安装FFMPEG
```shell
git clone git://source.ffmpeg.org/ffmpeg.git ffmpeg
cd ffmpeg
sudo ./configure --arch=armel --target-os=linux --enable-gpl --enable-libx264 --enable-nonfree
make -j4
sudo make install
```
这一步会花很长时间。

### 4. 测试
ip camera的视频流地址是：
> `rtsp://admin:123456@192.168.1.5:554/live0.264`

运行如下命令，将获取的视频流保存为视频文件。

`ffmpeg -rtsp_transport tcp -i rtsp://admin:123456@192.168.1.5:554/live0.264 stream.mkv`

输入**"q"**，停止保存。用 VLC 或者 ffplayer 打开保存的视频文件：

`vlc stream.mkv`

由于是视频流是软解，可以用top命令查看处理器使用率超过90%。

![cpu](/images/top.png)

##  参考
1. [Installing FFMPEG for Raspberry Pi](http://www.jeffreythompson.org/blog/2014/11/13/installing-ffmpeg-for-raspberry-pi/)