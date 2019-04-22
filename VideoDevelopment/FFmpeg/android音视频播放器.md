###  android视频播放
#### android音视频同步
- 视频和音频都会有pts,它要什么时候播放要什么时候渲染,都有一个这样的时间值
- 比如说:选视频为基准,以他的这个时间为基准,音频慢,就让他快一点, 视频慢一点,就让视频快一点,基本上就是你追我赶.

#### android OpenSL ES(Open Sound Library embeded system)声音播放

#### JNI和NDK 的区别
JNI:只是java访问C的一些api
NDK:必须使用jni,但是NDK内置了很多模块,比如说OpenGL OpenSL,NativeWindow的原生绘制.这都是android里面自带的.
