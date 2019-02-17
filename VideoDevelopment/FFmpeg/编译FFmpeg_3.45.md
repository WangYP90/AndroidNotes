## Android编译FFmpeg 
### 编译FFmpeg准备工作
- **需要的资料:**  
* VPS主机 系统CentOs7 64位 (可以用Ubuntu16.04 或18.04代替)
* windows下安装(Xshell6 或者PuTTY)  + WinSCP
* 下载NDK [link](https://www.androiddevtools.cn/) ,强烈建议使用NDK14b版本  
  `这里多说两句哦,NDK16版本中删除了旧的标头了https://android.googlesource.com/platform/ndk/+/master/docs/BuildSystemMaintainers.md(具体的有兴趣的可以自己研究下),引起各种编译问题. 另外一点就是新的NDK是不支持armeabi(arm-v5),所以如果有这种要求的也不能用高版本,这也是为什么ijkplayer建议编译配置还是Ndk10的原因.`
* 解压大全[tar解压](https://www.jianshu.com/p/3d9effeef575)
* [文件操作(如删除)](https://blog.csdn.net/nicolas12/article/details/80890811)  
- **配置**
* **解压NDK:**  
一般情况下使用Linux会在usr目录下新建自己的文件夹来管理自己的资源.  
我的目录是/usr/android_all/,unzip 名称.zip -d 解压路径 
* **配置NDK环境变量:**  
1. vim ~/.bashrc  ~代表用户,到当前用户的环境变量里面
2. 加入下面这两行:  
    export NDKROOT=/usr/wang/android_all/ndk_build/android-ndk-r16-beta1/
    export PATH=$NDKROOT:$PATH
3. 保存 ,source ~/.bashrc (更新环境变量,不然要重启)
4. ndk-build 
    Android NDK: Could not find application project directory !    
    Android NDK: Please define the NDK_PROJECT_PATH variable to point to it.    
    /usr/wang/android_all/ndk_build/android-ndk-r16-beta1/build/core/build-local.mk:151: *** Android NDK: Aborting    .  Stop.   
* **下载解压FFmpeg**
* 这里我使用的是3.4.5版本,FFmpeg最新的是4.1.3版本,还有一个4.0.3版本,这里我还是建议使用3.4.5版本, 因为4+版本仍然在编译时会或多或少有一些问题没法解决,而且网上对于FFmpeg的资料大部分都是3.4的,4.+的出现的问题,仍没法解决. 
	* 死磕(4.+)党:  
	```
	问题1: ndk10可以解决,其他不知道
	CC	libavformat/udp.o
libavformat/udp.c: In function 'udp_set_multicast_sources':
libavformat/udp.c:290:28: error: request for member 's_addr' in something not a structure or union
         mreqs.imr_multiaddr.s_addr = ((struct sockaddr_in *)addr)->sin_addr.s_addr;
    问题2:
    
	```
	
- 新建build_android.sh  
windows下新建 build_android.sh  
```
#!/bin/bash
NDK=/usr/wang/android_all/ndk_build/android-ndk-r14b/

#armv7-a
ARCH=arm
CPU=armv7-a
TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64
SYSROOT=$NDK/platforms/android-17/arch-$ARCH/
CROSS_PREFIX=$TOOLCHAIN/bin/arm-linux-androideabi-
PREFIX=$(pwd)/android/$CPU
OPTIMIZE_CFLAGS="-mfloat-abi=softfp -mfpu=vfp -marm -march=$CPU"

#armv8-a
ARCH=arm64
CPU=armv8-a
TOOLCHAIN=$NDK/toolchains/aarch64-linux-android-4.9/prebuilt/linux-x86_64
SYSROOT=$NDK/platforms/android-17/arch-$ARCH/
CROSS_PREFIX=$TOOLCHAIN/bin/aarch64-linux-android-
PREFIX=$(pwd)/android/$CPU
OPTIMIZE_CFLAGS="-march=$CPU"

#x86
ARCH=x86
CPU=x86
TOOLCHAIN=$NDK/toolchains/x86-4.9/prebuilt/linux-x86_64
SYSROOT=$NDK/platforms/android-17/arch-$ARCH/
CROSS_PREFIX=$TOOLCHAIN/bin/i686-linux-android-
PREFIX=$(pwd)/android/$CPU
OPTIMIZE_CFLAGS="-march=i686 -mtune=intel -mssse3 -mfpmath=sse -m32"

#x86_64
ARCH=x86_64
CPU=x86-64
TOOLCHAIN=$NDK/toolchains/x86_64-4.9/prebuilt/linux-x86_64
SYSROOT=$NDK/platforms/android-17/arch-$ARCH/
CROSS_PREFIX=$TOOLCHAIN/bin/x86_64-linux-android-
PREFIX=$(pwd)/android/$CPU
OPTIMIZE_CFLAGS="-march=$CPU -msse4.2 -mpopcnt -m64 -mtune=intel"

# 这是shell脚本的一个方法,里面就是ffmpeg config的一些开关,来控制动态或者静态库的大小.
function build_android
{
./configure \
    --prefix=$PREFIX \
    --enable-neon \
    --enable-hwaccels \
    --enable-gpl \
    --enable-postproc \
    --enable-shared \
    --enable-jni \
    --enable-mediacodec \
    --enable-decoder=h264_mediacodec \
    --disable-static \
    --disable-doc \
    --enable-ffmpeg \
    --disable-ffplay \
    --disable-ffprobe \
    --enable-avdevice \
    --disable-doc \
    --disable-symver \
    --cross-prefix=$CROSS_PREFIX \
    --target-os=android \
    --arch=$ARCH \
    --cpu=$CPU \
    --enable-cross-compile \
    --sysroot=$SYSROOT \
    --extra-cflags="-Os -fpic $OPTIMIZE_CFLAGS" \
    --extra-ldflags="$ADDI_LDFLAGS" \
$ADDITIONAL_CONFIGURE_FLAG
make clean
make
make install
}
build_android
```
- 在编译的时候需要什么版本就把什么版本打开,shell注释就是一个#单行注释,所以要注释掉其他或者直接删除CV大法.
- 移动build_android.sh 到FFmepg的根目录
- 这是一个windows编辑的.sh,而windows(\r\n)和linux(\n) 在换行符表述不一致,所以在windows下编译的不能在linux下直接运行.  
解决方法:安装dos2unix,yum install dos2unix(Centos这样安装,ubuntu百度下),执行命令dos2unix path  
也可以使用vim改(百度下).
- .sh是个普通文件移动过去的,需要给与build_android.sh可执行权限,添加权限: chmod u+x path  
* **执行**
cd 到 ffmepg根目录下,执行build_android.sh.  
等下一会,在ffmpeg根目录下出现android目录,里面就有编译好的so



ubuntu 18.04类似与CentOs