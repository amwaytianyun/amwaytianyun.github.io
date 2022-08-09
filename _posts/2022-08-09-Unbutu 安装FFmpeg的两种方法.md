Unbutu 安装FFmpeg的两种方法
Geek.Fan
于 2022-05-31 21:27:10 发布 320
收藏
[原地址](https://blog.csdn.net/fanyun_01/article/details/125073651)

分类专栏： FFmpeg开发实战 文章标签： p2p 网络协议 网络
版权
FFmpeg开发实战 专栏收录该内容
91 篇文章 52 订阅
订阅专栏

        本文介绍两种FFmpeg的安装方法，均可以完美安装和使用FFmpeg，首先先介绍第一种方法：

方法一：

        下载源码手动安装，此方法好处是，我们可以选择自己需要的版本的或者比较高的稳定版本的FFmpeg来进行安装。

1、官网下载最新包安装，编译安装

 我这里下载的是一个5.0.1的一个比较新的版本：

        wget https://launchpad.net/ubuntu/+archive/primary/+sourcefiles/ffmpeg/7:5.1-2ubuntu1/ffmpeg_5.1.orig.tar.xz

2、解压

        tar -xvf ffmpeg_5.1.orig.tar.xz

3、安装yasm

sudo apt install -y yasm

4、设置编译项

基础选项：

        cd ffmpeg-5.1
        ./configure --enable-shared --prefix=/usr/local/ffmpeg

5、增加H264支持

        ./configure --prefix=/usr/local/ffmpeg --enable-shared --disable-static --disable-doc --enable-gpl --enable-libx264

        6、增加GPU（CUDA）加速支持，则需要在尾部增加

        --enable-cuda --enable-cuvid

        7、同时下载并编译安装 FFmpeg NVIDIA headers (“ffnvcodec”)

    git clone https://git.videolan.org/git/ffmpeg/nv-codec-headers.git
     
    cd nv-codec-headers

        (# 有多个分支，根据GPU驱动版本可能需要切换分支，如git checkout sdk/9.1)

    make
     
    sudo make install

        否则编译ffmpeg时会报错“ERROR: cuvid requested, but not all dependencies are satisfied: cuda/ffnvcodec”。

 

8、编译安装

    make #时间好久
     
    make install

9、建立软链接

    sudo ln -s /usr/local/ffmpeg/bin/ffmpeg /usr/bin/ffmpeg
     
    sudo ln -s /usr/local/ffmpeg/bin/ffprobe /usr/bin/ffprobe

10、配置ffmpeg依赖环境

sudo vi /etc/ld.so.conf

在其中添加路径：

/usr/local/ffmpeg/lib

11、更新环境变量

sudo ldconfig

OK，可以使用。

方法二：

        使用命令安装，此方法好处是安装快速，缺点是，命令安装的版本是默认FFmpeg版本比较低，所以如果要使用一些FFFmpeg的新属性和新功能，建议手动编译安装。

1、安装

sudo apt-get install ffmpeg

2、版本查看

fanyun@ubuntu:~$ ffmpeg -version

ffmpeg version 4.2.4-1ubuntu0.1 Copyright (c) 2000-2020 the FFmpeg developers

built with gcc 9 (Ubuntu 9.3.0-10ubuntu2)

 

3、查看当前安装的ffmpeg 支持的编码器和解码器的支持情况。

ffmpeg -encoders -decoders

4、卸载

        此卸载方法只适用于刚刚那条简易安装指令，如果通过其他方法安装，该指令不适用。

sudo apt-get purge ffmpeg

        就是这样。FFmpeg现在已安装在您的系统上，您可以开始使用它了。发行新版本时，您可以通过命令行或桌面软件更新工具来更新FFmpeg软件包。

FFmpeg示例：

在本节中，我们将看一些有关如何使用该ffmpeg实用程序的基本示例。

基本转换

        使用转换音频和视频文件时ffmpeg，不必指定输入和输出格式。自动检测到输入文件格式，并从文件扩展名中猜测输出格式。

        将视频文件从mp4转换为webm：

ffmpeg -i input.mp4 output.webm

        将音频文件从mp3转换为ogg：

ffmpeg -i input.mp3 output.ogg

        指定编解码器:

        转换文件时，请使用该-c选项指定编解码器。它可以是任何受支持的解码器/编码器的名称，也可以是copy仅复制输入流的特殊值。

        使用libvpx视频编解码器和libvorbis音频编解码器将视频文件从mp4转换为webm ：

ffmpeg -i input.mp4 -c:v libvpx -c:a libvorbis output.webm

        将音频文件从mp3转换为使用libopus编解码器编码的ogg :

ffmpeg -i input.mp3 -c:a libopus output.ogg

        以上在Ubuntu 20.04上安装FFmpeg安装、卸载和使用基本上讲完了，希望对大家有帮助。
————————————————
版权声明：本文为CSDN博主「Geek.Fan」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/fanyun_01/article/details/125073651
