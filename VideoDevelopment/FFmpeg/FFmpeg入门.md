## FFmpeg

### FFmpeg基础
##### FFmpeg命令行
* windows 下安装和运行
[下载](https://ffmpeg.zeranoe.com/builds/)
解压完成后，ffmpeg-static\bin 文件夹下有ffmpeg.exe、ffplay.exe、ffprobe.exe
* 直接运行时不行的，打开cmd ，cd到ffmpeg-static\bin 目录下，然后直接ffmpeg -h 就可以命令行操作了
##### FFmpeg 的定义
FFmpeg既是一款音视频编解码工具，同时也是一组音视频编解码开发套件，作为编码开发套件，它为开发者提供了丰富的音视频处理调用接口。  

FFmpeg 提供了多种媒体格式的封装和解封装，包括多种音视频编码 、多种协议的流
媒体、多种色彩格式转换、多种采样率转换、多种码率转换等； FFmpeg 框架提供了多种
丰富的插件模块，包含封装与解封装的插件、编码与解码的插件等。

FFmpeg 中的“FF ”指的是“Fast Forward “

##### FFmpeg 的基本组成
FFmpeg 框架的基本组成包含AVFormat、AVCodec、AVFilter、AVDevice、AVUtil等模块库。
* **AVFormat**：
AVformat 中实现了目前多媒体领域中的绝大多数媒体封装格式，包括封装和解封装，如MP4、FLV、KV、TS等文件封装格式，RTMP、RTSP、MMS、HLS等网络协议封装格式。FFmpeg 是否支持某种媒体封装格式，取决于编译时是否包含了该格式的封装库。也可以根据需求增加自己的定制封装格式的扩展。
* **AVCodec**：
AVCodec中实现了目前多媒体领域绝大多数常用的编解码格式，既支持编码，也支持解码。AVCodec除了支持MPEG4、AAC、MJPEG等自带的媒体编解码格式之外，还支持第三方的编解码，如H.264（AVC）编码，需要使用x264编码器；H.265（HEVC）编码，需要使用x265编码器；MP3（mp3lame）编码，需要使用libMP3lame编码器。如果需要增加自己的编码格式。
* **AVFilter**：
AvFilter库提供了一个通用的音频、视频、字幕等滤镜处理框架。

一个小例子： 滤镜处理将输入的视频切割成两部分流，一部分流抛给crop 滤镜与vflip 滤镜处理模块进行操作，另一部分保持原样，当crop滤镜和vflip滤镜处理操作完成之后，将流合并到原有的overlay图层中。并显示在最上面一层，输出新视频。
`ffmpeg -i 500.mp4(INPUT) -vf "split [main][tmp];[tmp] crop=iw:ih/2:0:0, vflip [flip];[main][flip] overlay=0:H/2" 600.mp4(OUTPUT)`
总结：相同的Filter 线性链之间用逗号分隔，不同的Filter线性链之间用分号分隔。
上面的例子：crop与vflip 使用的是同一个滤镜处理的线性链，split滤镜和overlay滤镜使用的是另一种线性链。一个线性链与一个线性链汇合时是通过方括号“[]”括起来的标签进行标示的。
split滤镜分隔视频后视频流的第二部分打上标签[tmp]，通过crop滤镜对该部分进行处理，然后进行纵坐标调换操作，打上标签[flip],然后把[main]与[flip]进行合并。
* **swsccale模块**
swscale 模块提供了高级别的图像转换API，例如允许进行图像缩放和像素格式转换，常见于将图像从1080p转换成720p或者480p等的缩放，或者将图像数据从YUV420P 转换成YUYV，或者YUV转RGB等图像格式转换。
* **swresample模块**
swresample模块提供了高级别的音频重采样API。例如允许操作音频采样、音频通道布局与布局调整。

#### FFmpeg 官方运行程序
* ffmpeg.exe: `ffmpeg 是FFmpeg 源代码编译后生成的一个可执行的程序，其可以作为命令行工具使用。`
```
ffmpeg -i input.mp4 output.avi (确实进行了转码操作)

也可以写成：
ffmpeg -i input.mp4 -f avi output.dat(这里是故意写成output.dat，依然转码成功，但是这个文件的格式依然是avi，所以转码其实是和后缀名无关的。但是不指定的情况下会根据文件后缀。)

```
ffmpeg的主要工作流程相对比较简单： 解封装（Demuxing）、解码（Decoding）、编码（Encoding）、封装（Muxing）。

* ffplay.exe
FFmpeg 不但可以提供转码、转封装等功能，同事还提供了播放器相关功能，使用FFmpeg 的avformat与avcodec，可以播放各种媒体文件或者流。如果想要使用ffplay，那么系统首先需要有SDL来进行ffplay的基础支撑。  

* ffprobe.exe
ffprobe也是FFmpeg 源码编译后生成的一个可执行程序。 ffprobe 是一个非常强大的多媒体分析工具，可以从媒体文件或者媒体流中获取想要了解的媒体信息，比如音频的参数、视频的参数、媒体容器的参数信息等。  
`ffprobe -show_streams xxx.mp4`
#### FFmpeg 编码支持与定制
FFmpeg 本身支持一些音视频编码格式、文件封装格式与流媒体传输协议，但是支持的数量依然有限，FFmpeg所做的只是提供一套基础的框架，所有的编码格式、文件封装格式与流媒体协议均可以作为FFmpeg的一个模块挂载在FFmpeg 框架中。这些模块以第三方的外部库的方式提供支持，可以通过FFmpeg源码的configure 命令查看FFmpeg所支持的音视频编码格式、文件封装格式与流媒体传输协议，对于FFmpeg 不支持的格式，可以通过configure --help 查看所需要的第三方外部库，然后通过增加对应的编译参数选项进行支持。  

* 下面是FFmpeg 所支持的外部库主要包含如下列表：
```
bzip2 1.0.6 <http://bzi p.org/> 
Fontconfig 2.11.94 <http://freedesktop.org/wiki/Software/fontconfig> 
FreiOr 20130909-git-10d8360 <http://freiOr.dyne.org/> 
GnuTLS 3.3.15 <http://gnutls.org/> 
libiconv 1.14 <http://gnu.org/software/libiconv/> 
libass 0.12.2 <http://code.google.com/p/libass/> 
libbluray 0.8.1 <http://videolan.org/developers/libbluray.html> 
libbs2b 3.1.0 <http://bs2b.sourceforge.net/> 
libcaca 0. 99 .betal8 <http: //caca. zoy .org/wiki/libcaca> 
dcadec 20150506-git-98fb3b6 <https://github.com/foo86/dcadec> 
FreeType 2.5.5 <http://freetype.sourceforge.net/> 
Game Music Emu 0.6.0 <http://code.google.com/p/game-music-emu/> 
GSM 1.0.13-4 <http://packages.debian.org/source/squeeze/libgsm> 
iLBC 20141214 白 git-ef04ebe <https://github.com/dekkers/libilbc/> 
Modplug-XMMS 0.8.8.5 <http://modplug －泪runs.sourceforge.net/>
LAME 3.99.5 <http://1 缸ne. source forge. net/> 
OpenCORE AMR 0.1.3 <http://sourceforge.net/projects/opencore-amr/> 
OpenJPEG 1. 5. 2 <http://www.openjpeg.org/> 
Opus 1.1 <http://opus-codec.org/> 
RTMPDump 20140707-git-al900c3 <http://rtmpdump.mplayerhq.hu/> 
Schroedinger 1.0.11 <http://diracvideo.org/> 
libsoxr 0. 1. 1 <http://sourceforge.net/projects/soxr/> 
Speex 1. 2rc2 <http: I I speex. org /> 
Theora 1.1.1 <http://theora.org/> 
TwoLAME 0.3.13 <http://twolame.org/> 
vid.stab 0 . 98 <http://public.hronopik.de/vid.stab/> 
VisualOn AAC 0.1.3 <https://github.com/mstorsjo/vo-aacenc> 
VisualOn AMR-WB 0.1.2 <https://github.com/mstorsjo/vo-amrwbenc> 
Vorbis 1.3.5 <http://vorbis.com/> 
vpx 1.4.0 <http://webmproject.org/> 
WavPack 4.75.0 <http://wavpack.com/> 
WebP 0.4.3 <https://developers.google.com/speed/webp/> 
x264 20150223-git-121396c <http://videolan.org/developers/x264.html> 
x265 1.7 <http://x265.org/> 
XAVS svn-r55 <http://xavs.sourceforge.net/> 
Xvid 1.3.3 <http://xvid.org/> 
XZ Utils 5.2.1 <http://tukaani.org/xz> 
zlib 1.2.8 <http://zlib.net/> 
```
#### FFmpeg 的编码器支持
FFmpeg 源代码中可以包含的编码非常多，常见的和不常见的都可以在编译配置列表中见到，可以通过使用编译配置命令./configure --list-encoders
常见的有：AAC、AC3、H.264、H.265、MPEG4、MPEG2VIDEO、PCM、FLV1的编码器支持。
#### FFmpeg的解码器支持
FFmpeg 源代码本身包含了很多的解码支持，解码主要是在输入的时候进行解码，也可以理解为将压缩过的编码进行结压缩，关于解码的支持，可以通过./configure --list-decoders 命令进行查看：
常见的有：MPEG4、H.264、H.265（HEVC）、MP3等格式。
#### FFmpeg 的封装支持
FFmpeg 的封装（ Muxing ）是指将压缩后的编码封装到一个容器格式中，如果要查
看 FFmpeg 源代码中都可以支持哪些容器格式，可以通过命令.／configure 一 list-muxers 来
查看 ：  
从封装（又称复用）格式所支持的信息中可以看到， FFmpeg 支持生成裸流文件，如
日. 264 、 AAC 、 PCM ，也支持一些常见的格式，如 MP3 、 MP4 、 FLV 、 M3U8, WEBM 等。
#### FFmpeg 的解封装支持
FFmpeg 的解封装（ Demuxing ）是指将读入的容器格式拆解开，将里面压缩的音频流 、
视频流 、 字幕流、数据流等提取出来 ， 如果要查看 FFmpeg 的源代码中都可以支持哪些输
入的容器格式，可以通过命令.／configure 一list-demuxers 来查看 ：  
从解封装（ Demuxer ，又称解复用）格式支持信息中可以看到， FFmpeg 源代码中已 经
支持的 demuxer 非常多，包含图片（ image ） 、 MP3 、 FLV 、 MP4 、 MOV 、 AVI 等。
#### FFmpeg 的通信协议支持
FFmpeg 不仅仅支持本地的多媒体处理，而且还支持网络流媒体的处理，支持的网络
流媒体协议相对来说也很全面，可以通过命令.／configure 一list-protocols 查看：
async gopher librtmpte rtmps tls_gnutls 
bluray hls libsmbclient rtmpt tls_openssl 
cache http libs sh rtmpte tls schannel 
concat httpproxy md5 rtmpts tls securetransport 
crypto https mm sh rtp udp 
data icecast mmst sctp udplite 
ffrtmpcrypt librtmp pipe srtp unix 
ffrtmphttp librtmpe prompeg subfile 
file librtmps rtmp tcp 
ftp librtmpt rtmpe tee 
从协议的相关信息列表中可以看到， FFmpeg 支持的流媒体协议比较多，包括 MMS,
HTTP 、 HTTPS 、 HLS ( M3U8 ） 、 RTMP , RTP ，甚至支持 TCP 、 UDP ，其也支持使用 file
协议的本地文件操作和使用 concat 协议支持的多个文件串流操作，后面的章节中会有详细
的介绍。
本章重点介绍了 FFmpeg 的获取、安装、容器封装与解封装的格式支持、音视频编码
与解码 的格式支持，以及流媒体传输协议的支持。 综合来说， FFmpeg 所支持的容器
解码、协议相对来说比较全面，是一款功能强大的多媒体处理工具和开发套件。


### FFmpeg API
#### libavformat
libavformat 是FFmpeg中处理音频、视频以及字幕封装和解封装的通用框架，内置了很多处理多媒体文件的 Muxer（封装器） 和 Demuxer（解封装器），它支持如AVInputFormat的输入容器和AVOutputFormat的输出容器，同时也支持基于网络的一些流媒体协议，如HTTP、RTSP、RTMP等。



