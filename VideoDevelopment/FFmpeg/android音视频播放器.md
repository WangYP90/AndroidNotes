###  android视频播放
#### android音视频同步
- 视频和音频都会有pts,它要什么时候播放要什么时候渲染,都有一个这样的时间值
- 比如说:选视频为基准,以他的这个时间为基准,音频慢,就让他快一点, 视频慢一点,就让视频快一点,基本上就是你追我赶.

#### android OpenSL ES(Open Sound Library embeded system)声音播放

#### JNI和NDK 的区别
JNI:只是java访问C的一些api
NDK:必须使用jni,但是NDK内置了很多模块,比如说OpenGL OpenSL,NativeWindow的原生绘制.这都是android里面自带的.

### 帧
- IPB帧
- I帧:关键帧, 帧内亚朵, 包含完整的画面.解码时只需要本帧数据就可以完成.  压缩率(20%)
- P帧: 差别帧: 表示这一帧跟之前的一个关键帧(或P帧)的差别,解码时需要之前缓存的画面叠加上本帧定义的差别.生成追中画面.  压缩率:(50%)
- B帧:双向差别帧,与前后两帧的差别,解码B帧,不仅要之前的缓存画面,还要解码之后的画面,通过前后画面与本帧的叠加生成画面, 压缩率高,对CPU的也比较累.压缩率(80%)  
#### FFmpeg 判断帧
```
AVFrame->pict_type
AVPacket->flages & AV_PKT_FLAG_KEY
```
- FFmpeg 中的时间单位
```
time_base 时间单位(时间基)
不同的结构体,有不同的时间单位.
AVStream *stream;
time = stream->duration * stream->time_base;
```

### DTS 和 PTS
DTS: Decoding Time stamp 解码时间搓
PTS:Presentation Time Stamp 显示时间搓

```
流	I	P	B
DTS	1	2	3
PTS	1	3	2
```
### 音视频同步的三种方案
- 音频同步视频  
`让视频进行正常的时间去播放,视频有帧率,每一秒钟要绘制多少画面,这个就是帧率,控制绘制画面的个数,音频有采样率,就是每一秒中有多个个采样点,就能计算出没秒播放多少,如果说音频慢了,那就让音频快一点,音频快了,就让他慢一点.`
- 视频同步音频  
`音频采样率为标准时间,视频快了,就放慢, 视频慢了就放快.`
- 标准时间(视频已经播放了多长时间)  
`播放了多长时间`


### FFmpeg 推流到nginx服务器
#### 采集(视频)
#### 编码(H264) : H264-x264开源库  AAC-FAAC开源库
#### 推流(rtmp):rtmp-dumprtmp