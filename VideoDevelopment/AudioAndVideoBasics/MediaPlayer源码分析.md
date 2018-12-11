## 音视频基础
### 音频基础
声音是由物体振动而产生的
声波的三要素 频率、振幅和波形。

* 频率代表音阶的高低
频率（过零率）越高，波长就越短，低频声响的波长则较长。所以其可以更容易地绕过障碍物，
* 振幅代表响度
* 波形代表音色

### MediaPlayer
#### 创建过程
```java
这里只介绍create方法来创建MediaPlayer
 public static MediaPlayer create(Context context, Uri uri, SurfaceHolder holder,
            AudioAttributes audioAttributes, int audioSessionId) {

        try {
            MediaPlayer mp = new MediaPlayer();
            //声音相关处理,若为空,就创建一个.
            final AudioAttributes aa = audioAttributes != null ? audioAttributes :
                new AudioAttributes.Builder().build();
            //设置音频属性
            mp.setAudioAttributes(aa);
            //设置声音的会话ID,视频和音频是分开渲染的.
            mp.setAudioSessionId(audioSessionId);
            //把url地址传入到底层
            mp.setDataSource(context, uri);
            //判断SurfaceHolder是否为空,这是一个控制器,Surface的控制器,用来操作Surface,处理它在Canvas上作画的效果和动画,控制表面、大小、像素等。
            if (holder != null) {
                mp.setDisplay(holder);
            }
            mp.prepare();//开始准备
            return mp;//返回
        } catch (IOException ex) {
            Log.d(TAG, "create failed:", ex);
            // fall through
        } catch (IllegalArgumentException ex) {
            Log.d(TAG, "create failed:", ex);
            // fall through
        } catch (SecurityException ex) {
            Log.d(TAG, "create failed:", ex);
            // fall through
        }

        return null;
    }
```
#### MediaPlayer.cpp
```c++
   static void android_media_MediaPlayer_native_init(JNIEnv *env) //可以理解为一个万能的指针表,通过操作符(->)访问JNI中的函数
   {
       jclass clazz;//类的句柄
       clazz = env->FindClass("android/media/MediaPlayer");
       //这里通过Native层调用Java层,获取MediaPlayer对象
       if(clazz == NULL){
       	return;
       }
       //获取成员变量mNativeConext,它在MediaPlayer.java中是一个long型整数实际对应的是一个内存地址.
       if(fields.context = NULL){
       	return;
       }
       fields.post_event = env->GetstaticMethodID(clazz,"postEventFromNative","(Ljava/lang/Object;IIILjava/lang/Object;)V");
       if(fields.post_event == NULL){
           return;
       }
}
       //省略部分代码
       /*
       上面的这种方式是通过JNI调用Java层的MediaPlayer类,然后拿到mNativeContext 的指针,接着调用MediaPlayer.java中的静态方法postEventFromNative,把Native的事件回调到Java层,使用EventHandler post事件回到主线程中,用软引用指向原生的MediaPlayer,以保证Native代码是安全的.
       */
priveate static void postEventFromNative(Object mediaplayer_ref,int what, int arg1, int arg2,Object obj){
    MediaPlayer mp = (Mediaplayer)((WeakReference)mediaplayer_ref).get();//得到软引用对象
    if(mp == null){
    	return;
    }
    //省略部分代码
    if(mp.mEventHandler != null){
        Message m = mp.mEventHandler.obtainMessage(what,arg1,arg2,obj);
        mp.mEventHandler.sendMessage(m);
    }
}
/*之前我们再Java层的MediaPlayer.java文件的构造函数中,分析到最后有一个native_setuo,在android_media_MediaPlayer.cpp中找到对应的函数,
*/
static void android_media_MediaPlayer_native_setup(JNIEnv *env,jobject thiz,jobject weak_this){
    sp<MediaPlayer> mp = MediaPlayer();
    //省略部分代码
    //给MediaPlayer 创建一个listener,以便我们再java层设置的setPrepareListener、setOnCompleteListener能产生回调
    sp<JNIMediaPlayerListener> listener = new JNIMediaPlayerListener(env,thiz,weak_this);
    mp->setListener(listener);
    //对于Java层来说,C++中的MediaPlayer是不透明的,也无须关心其对应的逻辑,各司其职.
    setMediaPlayer(env,thiz,mp);
}
//可以看到会设置一些会调用的listener及创建C++中的MediaPlayer对象.
```
#### setDataSource 过程
上面是对MediaPlayer的构造过程,接下来就是setDataSource过程:
```java
public void setDataSource(String path){
	  setDataSource(path, null, null);
}
//setDatasource 包含文件或HTTP/RTSP地址
private void setDataSource(String path, Map<String, String> headers, List<HttpCookie> cookies)
            throws IOException, IllegalArgumentException, SecurityException, IllegalStateException
    {
        String[] keys = null;
        String[] values = null;

        if (headers != null) {
            keys = new String[headers.size()];//请求头的key
            values = new String[headers.size()];//请求头的value

            int i = 0;
            //把HTTP/RTSP 消息头分别封到两个数组中
            for (Map.Entry<String, String> entry: headers.entrySet()) {
                keys[i] = entry.getKey();
                values[i] = entry.getValue();
                ++i;
            }
        }
        setDataSource(path, keys, values, cookies);
    }
    
    
private void setDataSource(String path, String[] keys, String[] values,List<HttpCookie> cookies)
            throws IOException, IllegalArgumentException, SecurityException, IllegalStateException {
        final Uri uri = Uri.parse(path);//解析path
        final String scheme = uri.getScheme();
        if ("file".equals(scheme)) {
            path = uri.getPath();
        } else if (scheme != null) {
        	//1.处理非文件资源
            // handle non-file sources
            nativeSetDataSource(
                MediaHTTPService.createHttpServiceBinderIfNecessary(path, cookies),
                path,
                keys,
                values);
            return;
        }
		//2.处理文件类型
        final File file = new File(path);
        if (file.exists()) {
            FileInputStream is = new FileInputStream(file);
            FileDescriptor fd = is.getFD();//得到文件标识符
            setDataSource(fd);
            is.close();
        } else {
            throw new IOException("setDataSource failed.");
        }
    }
//调到这里
public void setDataSource(FileDescriptor fd)
            throws IOException, IllegalArgumentException, IllegalStateException {
        // intentionally less than LONG_MAX
        setDataSource(fd, 0, 0x7ffffffffffffffL);
    }
public void setDataSource(FileDescriptor fd, long offset, long length)
            throws IOException, IllegalArgumentException, IllegalStateException {
        _setDataSource(fd, offset, length);
    }

private native void _setDataSource(FileDescriptor fd, long offset, long length)
            throws IOException, IllegalArgumentException, IllegalStateException;
/*开始进入JNI层,发现找不到android_media_MediaPlayer_setDataSource函数,但发现有一个函数名映射函数声明,这是JNI中的常用动态注册方法.
*/
static JNINativeMethod gMethods[] = {
    {
        "nativeSetDataSource",
        "(Landroid/os/IBinder;Ljava/lang/String;[Ljava/lang/String;[Ljava/lang/String;)V",
        (void *)android_media_MediaPlayer_setDataSourceAndHeaders
    },
    {"_setDataSource",	"(Ljava/io/FileDescriptor;JJ)V",
     (void *)android_media_MediaPlayer_setDataSourceFD
    },
    {"_setDataSource",	"(Landroid/media/MediaDataSource;)V",
	 (void *)android_media_MediaPlayer_setDataSourceCallback
    }
    {"_setDataSourface", "(Landroid/view/Surface;)V",
	 (void *)android_media_MediaPlayer_setVideoSurface
    },
}
//省略相关函数名映射native 函数声明
/*以上这些都是函数名映射,看过JNIEnv * 源码的话,应该不会陌生,无非还是映射,不影响我们分析.
    下面对android_media_MediaPlayer_setDataSourceFD函数进行分析:
*/
 static void android_media_MediaPlayer_setDataSourceFD(JNIEnv *env,jobject thiz,jobject fileDescriptor,jlong offset,jlong length)
 {	//得到MediaPlayer对象
     sp<MediaPlayer> mp = getMediaPlayer(env,thiz);
     //省略抛出异常代码
     //在JNI中获取java.io.FileDescriptor
     int fd = jniGetFDFromFileDescriptor(env,fileDescriptor);
     ALOGV("setDataSourceFD：fd %d",fd);//这是一句输出log 打印
     process_media_player_call( env, thiz, mp->setDataSource( fd, offset, length), "java/io/IOException", "setDataSourceFD failed.");
 }
//这里开始调用 JNIEnv* 中的 GetIntField 函数获取对应的变量
int jniGetFDFromFileDescriptor(JNIEnv *env,jobject fileDescriptor){
    return (*env)->GetIntField( env, FileDescriptor, gCachedFields.DescriptorField);
}
//接着分析 process_media_player_call 函数:
static void process_media_player_call(JNIEnv *env, jobject thiz,status_t opStatus, const char* exception, const char *message)
{
    if(exception == NULL){ //不抛出异常,发送一个onError事件
        if(excption == NULL){ //如果setDataSource过程中opStatus不ok
           sp<MediaPlayer> mp = getMediaPlayer(env, thiz);
            if(mp != 0){
                mp->notify(MEDIA_ERROR, opStatus, 0);
                //通知MEDIA_ERROR
            }
        }
    }
    //省略抛出异常部分代码
}
/*
总结以上代码: 当 mp->setDataSource(fd, offset, length)函数得到状态后,对各种状态进行通知.有异常的直接抛出,这样也就不会影响MediaPlayer后面的执行过程了.
下面是HTTP/RTSP传入JNI.在java层中对应的nativeSetDataSource函数:
*/
private native void nativeSetDataSource(IBinder httpServiceBinder, String path, String[] keys, String[] values)throws IOException, IllegalArgumentException, SecurityException,IllegalStateException;
//在JNI中通过映射表可对应到adroid_media_MediaPlayer_setDataSourceAndHeaders函数:
static void adroid_media_MediaPlayer_setDataSourceAndHeaders(JNIEnv *env,jobject thiz, jobject httpServiceBinderObj, jstring path, jobjectArray keys, jobjectArray values)
{
    //获取MediaPlayer对象
    sp<MediaPlayer> mp = getMediaPlayer(env, thiz);
    //省略判空,抛出异常的代码
    const char *tmp = env->GetStringUTFChars(path, NULL);
    if(tmp == NULL){ //内存溢出
        return;
    }
    ALOGV("setDataSource: path %s", tmp);
    String8 pathStr(tmp);
    env->ReleaseStringUTFChars(path, tmp);
    tmp = NULL;
    //省略部分代码
    sp<IMediaHTTPService> httpService;
    if(httpServiceBinderObj != NULL){
        sp<IBinder> binder = ibinderForJavaObject(env, httpServiceBinderObj);
        //通过Binder机制将httpServiceBinderObj 传给IPC并返回给binder
        //然后强制转换为IMediaHTTPService
        httpService = interface_cast<IMediaHTTPService>(binder);
    }
    //开始判断状态,和上面的文件操作是一样的
    status_t opStatus = mp->setDataSource(httpService, pathStr, headerVector.size() > 0?headersVector : NULL);
    //见上面的文件操作
    process_media_player_call(env, thiz, opStatus, "java/io/IOException", "setDataSource failed.");
}
/*至此,setDataSource过程就完成了,这里需要注意两点,一点是从Java->JNI->C++ 的正向调用过程(前面从java层到Native层都是正向过程),一点是C++ -> JNI->java的过程(如mp->setDataSource(httpService, pathStr, headersVector.size()>0 ? headersVector: NULL)),这样回调的好处是什么? 有几点:
	安全性,封装在Native层的代码是so形式的,破坏性风险小.
	效率高,在运行速度上C++执行时间短,且底层也是C++语言编写的.对于复杂的渲染及时间要求高的渲染,放在Native层是最好不过的选择.
	连通性,正向调用将值传入,反向调用把处理过的值通知回去.相当于一根管道.
*/


```
#### setDisplay过程
```java
public void setDisplay(SurfaceHolder sh)
{
	nSurfaceHolder = sh;// 1. 给Surface设置一个控制器,用于展示视频图像
	Surface surface;
	if (sh != null) {
		surface = sh.getSurface();
	} else {
		surface = null;
	}
	_setVideoSurface(surface);//2.给视频设置Surface,带_的函数是native函数
	updateSurfaceScreenOn();//3.更新Surface到屏幕上
}
//对于上面代码中的第2点,同样在android_media_MediaPlayer.cpp中找到其对应的函数
static void android_media_MediaPlayer_setVideoSurface(JNIEnv *env, jobject thiz, jobject jsurface)
{
	setVideoSurface(env, thiz, jsurface, true);
}
static void setVideoSurface(JNIEnv *env, jobject thiz, jobject jsurface, jboolean mediaPlayerMustBealive)
{
	sp<MediaPlayer> mp = getMediaPlayer(env, thiz);
	//省略抛出异常的代码
	decVideoSurfaceRef(env, thiz);
	//IGraphicBufferProducer
	sp<IGraphicBufferProducer> new_st;
	if(jsurface){
		//得到Java层的Surface
		sp<Surface> surface(android_view_Surface_getSurface(env,jsurface));
		if(surface != NULL){
			new_st = surface->getIGraphicBufferProducer();
			//省略抛出异常的代码
			//调用incStrong
			new_st->incStrong((void*)decVideoSurfaceRef);
		} else {
            //省略抛出异常的代码
            return;
		}
	}
	env->SetLongField(thiz, fields.surface_texture, (jlong)new_st.get());
	/*如果MediaPlayer还未被初始化,setDataSource将失败,但setDataSurce之前就setDiaplay了,在prepare/prepareAsync中调用setVideoSurfaceTexture可以覆盖该case
	*/
	mp->setVideoSurfaceTexture(new_st);
}

static void decVideoSurfaceRef(JNIEnv *env, jobject thiz)
{
	sp<MediaPlayer> mp = getMediaPlayer(env, thiz);
	//省略部分代码
	//得到旧的SurfaceTexture
	sp<IGraphicBufferProducer> old_st = getVideoSurfaceTexture(env, thiz);
	if(old_st != NULL){
        old_st->decStrong((void*)decVideoSurfaceRef);
	}
}
/*这里有几个概念:
SurfaceTexture: SurfaceTexture 是Android 3.0加入的一个类,这个类跟Surface很像,可从视频解码里面获取图像流(image stream). 但是,和SurfaceView 不同的是,SurfaceTexture在接收图像流之后,不需要显示出来.SurfaceTexture不需要显示到屏幕上,因此我们可以用SurfaceTexture 接收解码出来的图像流,然后从SurfaceTexture中取得图像帧的富而不能进行处理,处理完毕后再送给另一个SurfaceView用于显示.

Surface: 处理被屏幕排序的原生Buffer, Android中的Surface就是一个用来画图形(graphic)或图像(image)的地方,对于View及其子类,都画在SUrface上的,各Surface对象通过SurfaceFlinger合成到frameBuffer.每个Surface都是双缓冲的(实际上就是两个线程,一个渲染线程,一个UI更新线程),它有一个backBuffer 和一个frontBuffer.在Surface中创建的Canvas对象,可以来管理Surface绘图操作,Canvas对应Bitmap,存储Surface中的内容.

SurfaceView: 在Camera、MediaRecorder、MediaPlayer中SurfaceView经常被用来显示图像.SurfaceView是View的子类,实现了Parcelable接口,其中内嵌了一个专门用于绘制的Surface,SUrfaceView可以控制这个Surface的格式和尺寸,以及Surface的绘制位置.可以理解Surface就是管理数据的地方,SurfaceView就是展示数据的地方.

SurfaceHolder: 顾名思义,是一个管理SurfaceHolder的容器.SUrfaceHolder是一个接口,其可被理解为一个Surface的监听器.通过回调函数addCallback(SurfaceHolder,Callback callback)监听Surface的创建,通过获取Surface中的Canvas对象,锁定之.所得到的Canvas对象在完成修改Surface中的数据后,释放同步锁,并提交改变Surface的状态及图像,展示新的图像数据.

总结: SurfaceView中调用getHolder函数,可以获得当前SUrfaceView中的Surface对应的SurfaceHolder,SurfaceHolder开始对Surface进行管理操作.这里按MVC模式可以更好的理 解 M:Surface(图像数据)、V：SurfaceView（图像展示）、C：SurfaceHolder（图像数据管理）.MediaPlayer.java中的setDisplay操作就是对将要显示的视频进行预设置.
*/

```
#### 开始 prepare 后的流程
![image](https://github.com/WangYP90/AndroidNotes/blob/master/VideoDevelopment/img/MediaPlayer%E7%B1%BB%E4%B9%8B%E9%97%B4%E7%9A%84%E4%BE%9D%E8%B5%96%E5%9B%BE.png?raw=true)
MediaPlayer部分的头文件在frameworks/base/include/media/目录中,这个目录和libmedia.so库源文件的目录frameworks/base/media/libmedia/相对应,主要的头文件有以下几个:  

* IMediaPlayerClient.h
* mediaplayer.h
* IMediaPlayer.h
* IMediaPlayerService.h
* MediaPlayerInterface.h  
mediaplayer.h 提供了对上层的接口,其他几个头文件提供的是一些接口类(即包含了纯虚函数的类),这些接口类必须被实现类继承才能够使用.
在运行的时候,整个MediaPlayer可以大致上分成Client 和Server两个部分,他们分别在两个进程中运行,他们之间使用Bunder机制实现IPC通讯.从框架结构上看,IMediaPlayerService.h、IMediaPlayerClient.h和mediaplayer.h这个3个头文件定义了MediaPlayer的接口和架构，在目录中有专门的MediaPlayerService.cpp 和mediaplayer.cpp文件对应上面3个头文件，用于MediaPlayer架构的实现。  
在给播放器设置数据源且展现了Surface后，你应当开始调用prepare或prepareAsync函数。对应文件类型，调用prepare函数将暂时阻塞，因为prepare是一个同步函数，直到MediaPlayer已经准备好数据即将播放，也就是播放器回调了onPrepared函数，进入Prepared状态。
- preapre函数的执行过程
```c++
public void prepare() throws IOException, IllegalStateException{
	_prepare();//调用native函数
	scanInternalSubtitleTracks();
}
//Native层的android_media_MediaPlayer_prepare函数:
static void android_media_mediaplayer_prepare(JNIEnv *env, jobject thiz){
    sp<MediaPlayer> mp = getMediaPlayer(env, thiz);//获取MediaPlayer对象
    if(mp == NULL){
    	jniThrowException(env, "java/lang/IllegalStateException", NULL);
    	return;
    }
    sp<IGraphicBufferProducer> st = getVideoSurfaceTexture(env, thiz);//1
    mp->setVideoSurfaceTexture(st);//2
    process_media_player_call(env, thiz , mp->prepare(), "java/io/IOException", "Prepare failed");//3
}

static sp<IGraphicBufferProducer> getVideoSurfaceTexture(JNIEnv* env, jobject thiz){
    IGraphicBufferProducer * const p = (IGraphicBufferProducer*)env->GetLongField(thiz, fields.surface_texture);
    return sp<IGraphicBufferProducer>(p);
}
/*
1. getVideoSurfaceTexture获取一个IGraphicBufferProducer类型指针
2. 是setVideoSurfaceTexture,主要给MediaPlayer传入IGraphicBufferProducer.这里IGraphicBufferProducer就是App 和 BufferQueue 的重要桥梁, GraphicBufferProducer承担着单个应用进程中的UI显示需求,与BufferQueue打交道的就是它.	BpGraphicBufferProducer 是 GraphicBufferProducer在客户端这边的代理对象,负责和SurfaceFlinger交互, GraphicBufferProducer 通过 gbp(IGraphicBufferProducer类对象) 向BufferQueue 获取Buffer,然后填充UI信息,填充完毕会通知SurfaceFlinger.
3. 是一个判定并且进行通知的函数,这个process_media_player_call 是对MediaPlayer调用prepare 函数后是否有异常的检测,如果出现参数不合法,或是I/O异常,就会抛出异常.
*/

// 一个网络URL发送过来, 用到prepareAsunc 函数的流程:
public void startPlayUrl(Uri uri){
    MediaPlayer mMediaPlayer = new MediaPlayer();
    try{
    	mMediaPlayer.setDataSource(this, uri);
    } catch(Exception e){
    //这里有4个异常,举例使用,实际开发尽量各自处理
    	e.printStackTrace();
    }
    mMediaPlayer.setOnPreparedListener(mPreparedListener);
    mMediaPlayer.setOnVideoSizeChangedListener(mVideoSizeChangedListener);
    mMediaPlayer.setErrorListener(mOnErrorListener);
    mMediaPlayer.prepareAsync();
}

public native void prepareAsync() throws IllegalStateException;
static void android_media_MediaPlayer_prepareAsync(JNIEnv *env, jobject thiz)
{
    sp<MediaPlayer> mp = getMediaPlayer(env, thiz);
    if(mp == NULL){
        jniThrowException(env, "java/lang/IllegalStateException", NULL);
        return;
    }
    sp<IGraphicBufferProducer> st = getVideoSurfaceTexture(env, thiz);
    mp->setVideoSurfaceTexture(st);
    process_media_player_call(env, thiz, mp->prepareAsync(), "java/io/IOException", "Prepare Asunc failed.");
}
//除了最后process_media_player_call 中的mp->preapreAsync 在判断状态时不一样,其他都和prepare函数是一样的,它的操作结果经过回调通知Java层.
//下面是 media/mediaplayer.h中的prepareAsync函数
status_t MediaPlayer::prepareAsync() // 之前所说的status_t就是从这里返回的
{
    ALOGV("prepateAsync");
    Mutex::Autolock_l(mLock);//这是一个互斥锁
    return prepareAsync_l();
}
status_t MediaPlayer::prepareAsync_l()
{
    if((mPlayer != 0) && (mCurrentState & (MEDIA_PLAYER_INITIALIZED |MEDIA_PLAYER_STOPPED ))){
    //设置音频流类型,在IMediaPlayer.cpp中对应的transact 操作SET_AUDIO_STREAM_TYPE
    mPlayer->setAudioStreamType(mStreamType);
    //将当前状态设置为MEDIA_PLAYER_PREPARING
    mCurrentState = MEDIA_PLAYER_PREPARING;
    return mPlayer->prepareAsync();
    }
    ALOGE("prepareAsync called in state %d", mCurrentState);
    return INVALID_OPERATION;
}
// 继续分析prepareAsync函数,mp->prepareAsync()对应的BnMediaPlayer操作如下:
MediaPlayerService::Client::prepareAsync case PREPARE_ASUNC: 
{
    CHECK_INTERFACE(IMediaPlayer, data ,reply);
    reply->writeInt32(prepareAsync());
    return NO_ERROR;
}break;
// 分析MediaPlayerService::Client::prepareAsync函数:
status_t MediaPlayerService::Client::prepareAsync()
{
	ALOGV("[%d] prepareAsync", mConnId);
	sp<MediaPlayerBase> p = getPlayer();
	if(p == 0) return UNKNOWN_ERROR;
	status_t ret = p->prepareAsync();
	//#if CALLBACK_ANTAGONIZER
	ALOGD("start Antagonizer");
	if(ret == NO_ERROR) mAntagonizer->start();
	//#endif
	return ret;
}
//这里调用了 AwesomePlayer 的prepareAsync 函数:
status_t AwesomePlayer::prepareAsync()
{
	ATRACE_CALL();
	Mutex::Autolock autoLock(mLock);//互斥锁
	if(mFlags & PREPARING){
	return UNKNOWN_ERROR; //说明prepareAsync 已经在等待prepare了
	}
	mIsAsyncPrepare = true;
	return prepareAsync_l();
}
//接着看 AwesomePlayer::prepareAsync_l函数：
status_t AwesomePlayer::prepareAsync_l()
{
	if(mFlags & PREPARING){
		return UNKNOWN_ERROR;//说明prepareAsync 已经在等待prepare了
	}
	if(!mQueueStarted){//队列不是开始状态
		mQueue.start();//设置成开始状态
		mQueueStarted = true;
	}
	modifyFlags(PREPARING, SET); //修改状态为 PERPARING
	//这里 AwesomeEvent 接收到事件,进行回调
	mAsyncPrepareEvent = new AwesomeEvent(this, &AwesomePlayer::onPrepareAsyncEvent);
	//将回调事件通过队列通知出去
	mQueue.postEvent(mAsyncPrepareEvent);
	return OK;
}
/* 总结: 首先判断mFlags, 此时不是PREPARING.接着启动mQueue(TimedEventQueue).之后修改mFlags 的状态为PREPARING,表示现在正在准备处理文件的音视频流.然后实例化一个AwesomeEvent,放到之前启动的mQueue中进行通知.
队列中处理的结果就是调用AwesomePlayer::onPrepareAsyncEvnet函数.后面的过程就是初始化解码器,将流解码出来,也能知道视频流的宽高等属性,然后处于Prepared状态,不再向下追送.prepare的流程就完成了.
*/

//下面回到java层之前的prepare函数中的 scanInternalSubtitleTracks函数:
private void scanInternalSubtitleTracks(){
	if(mSubtitleController == null){//字幕控制器为null
		setSubtitleAnchor();//设置一个字幕控制锚点
	}
	populateInbandTracks();
	if(mSubtitleController != null){
		mSubtitleController.selectDefaultTrack();
	}
}
//这个函数用于扫描内嵌字幕并进行跟踪,接下来看看MediaPlayer中的start函数:
public void start() throws IllegalStateException{
	if(isRestricted()){// 1.判断是否是受限制的
		_setVolume(0, 0); // 2.如果受限制,设置声音为0
	}
	stayAwake(true); // 3. 设置唤醒为1
	_start();
}
private Powermanager.WakeLock mWakeLock = null;
private void stayAwake(boolean awake){
    if(mWakeLock != null){
        if(awake && !mWakeLock.isHeld()){
            mWakeLock.acquire(); //获取
        } else if (!awake && mWakeLock.isHeld()){
        	mWakeLock.release();//释放
        }
    }
    mStayAwake = awake;
    updateSurfaceScreenOn(); // 4.更新Surface
}
/* 从Paused状态变为Started 状态,如果playback已经处于Stopped状态,或之前从来没有处于过Started状态,playback将会开始start.
	总结:start函数用于start 或者重新恢复播放,如果playback先前已暂停,playback将从Paused状态变为Started状态,如果playback已经处于Stopped状态,或之前从来没有处于过Started状态,playback将会开始start.
	stayAwake用于对屏幕进行操作.
	首先执行ProwerManager pm = (PowerManager)getSystemService(Context.POWER_SERVICE);通过Context.getSystemService函数获取PowerManager实例.然后通过PowerManager的newWakeLock(int flags, String tag)来生成WakeLock实例.int flags 指示要获取那种WakeLock,不同的锁对CPU、屏幕、键盘灯有不同的影响.获取WakeLock实例后通过acquire获取相应的锁,然后进行其他业务逻辑的操作,最后使用release释放(释放是必须的);
	关于int flags,各种锁的类型对CPU、屏幕、键盘的影响如下：
	- PARTIAL_WAKE_LOCK: 保持CPU运转,屏幕和键盘灯有可能是关闭的
	- SCREEN_DIM_WAKE_LOCK:保持CPU运转,允许保持屏幕显示但有可能是灰的,允许关闭键盘灯.
	- SCREEN_BRIGHT_WAKE_LOCK: 保持CPU运转,允许保持屏幕高亮显示,允许关闭键盘灯.
	- FULL_WAKE_LOCK: 保持CPU运转,保持屏幕高亮显示,键盘灯也保持亮度.
	- ACQUIRE_CAUSES_WKEUP: 正常唤醒锁实际上并不打开照明.相反,一旦打开,他们会一直保持.当获得WakeLock时,这个标志会使屏幕或/和键盘立即打开.一个典型的应用就是,可以立即看到对用户来说重要的通知.
*/

//最后,通过updateSUrfaceScreenOn 函数更新屏幕上的SUrface. 下面还是回到最上面的start函数中. 在JNI中,对应的是android_media_MediaPlayer_start函数.

static void android_media_MediaPlayer_start(JNIEnv *env,jobject thiz)
{
    ALOGV("start");
    sp<MediaPlayer> mp = getMediaPlayer(env, thiz);
    //省略判空抛异常
    process_media_player_call(env, thiz ,mp->start, NULL, NULL);
}

//从MediaPlayer 调用start 函数开始,就进入了视频播放环节,最终会到C++的mediaplayer.cpp中实现,我们先分析一下mediaplayer.h(路径为\frameworks\av\include\media\mediaplayer.h):
class MediaPlayer : public BnMediaPlayerClient
{
public:
	MediaPlayer();
	~MediaPlayer();
	void onFirstRef();
	void disconnect();
	status_t setDataSource(const char *url);
	status_t setDataSource(int fd, int64_t offset, int64_t length);
	status_t setVideoSurface(const sp<Surface>& surface);
	status_t setListener(const sp<MediaPlayerListener>& listener);
	status_t prepare();
	status_t prepareAsync();
	status_t start();
	status_t stop();
	status_t pause();
	bool isPlaying();
	status_t getVideoWidth(int *w);
	status_t getVideoHeight(int *h);
	status_t seekTo(int msec);
	status_t getCurrentPosition(int *msec);
	status_t getDuration(int *msec);
	status_t reset();
	status_t setAudioStreamType(int type);
	status_t setLooping(int loop);
	status_t setVolume(float leftVolume, float rightVolume);
	void notify(int msg, int ext1, int ext2);
	static sp<IMemory> decode(const char* url, uint32_t *pSampleRate, int* pNumChannels);
	static sp<IMemory> decode(int fd, int64_t offset, int64_t length, uint32_t *pSampleRate, int* pNumChannels);
	//省略部分代码
}
// 从接口中可以看出MediaPlayer 类实现了一个MediaPlayer的基本播放控制操作,如播放(start)、停止（stop）、暂停（pause）、重置（reset）等。
// 另外的一个类DeathNotifier 是在MediaPlayer类中定义的,它集成了IBinder 类中的DeathRecipient类,这些类都是为进程间通信做准备的:
class DethNotifier: public IBinder:: DeathRecipient
{
public:
	DeathNotifier(){}
	virtual ~DeathNotifier();//析构
	virtual void binderDied(cost wp<Ibinder>& who);
}
//对于MediaPlayerClient 和 MediaPlayerService 通过IPC进行通信,可以发现调用start函数后,底层返回了一个状态,以便我们知道已经处于Started状态还是没有处于Started状态,这时需要用process_media_player_call判定这个返回状态,然后通知java层中的回调事件.

//下面是pause 函数:
public void pause() throws IllegalStateException 
{
	stayAwake(false);//把唤醒状态置为false
	_pause();//调用native代码
}
//在对应的JNI中找到android_media_mediaplayer_pause函数:
static void android_media_MediaPlayer_pause(JNIEnv *env,jobject thiz)
{
	ALOGV("pause");
	sp<MediaPlayer> mp = getMediaPlayer(env, thiz);
    //省略判空抛异常
    process_media_player_call(env,thiz, mp->pause(), NULL, NULL);
}
//查看pause函数,可以看到和start函数的流程类似,也是通过mp->pause返回对应的状态,然后通知上层来暂停的.

```
#### C++中的MediaPlayer的C/S架构
上面介绍的是java层调用JNI层中, 现在来分析JNI层向下到C++层,下面是Java层一个函数在C++层MediaPlayer中的过程(C++ 路径为 frameworks/av/media/libmedia/MediaPlayer.cpp)
下面是setDataSource 函数来看看 C(Client) /S(Server) 模式的过程：
```c++
status_t MediaPlayer::setDataSource(int fd, int64_t offset, int64_t length)
{
    ALOGV("setDataSource(%d, %"PRId64", %"PRId64")", fd, offset, length);
    status_t err = UNKNOWN_ERROR;
    //首先赋值为一个未知错误的状态,就像boolean值事先声明为false一样
    const sp<IMediaPlayerService>& service(getMediaPlayerService());
    //通过IMediaPlayerService 获取Service 端的MediaPlayerService
    if(service != 0){//如果service不为空
    	sp<IMediaPlayer> player(service->create(this, mAudioSessionId));
    	//调用service的create函数
    	if(NO_ERROR != doSetRetransmitEndpoint(player) || (NO_ERROR != player->setDataSource(fd, offset, length))){
    		player.clear();
    	}
    	err = attachNewPlayer(player);
    }
    return err;
}
//对应C++ 6.0 源码 MediaPlayerService.cpp 处于frameworks/media/libmediaplayerservice/MediaPlayerService.cpp中
sp<IMediaPlayer> MediaPlayer::create(const sp<IMediaPlayerClient>& client, int audioSessionId)
{
    pid_t pid = IPCThreadState::self()->getCallingPid();
    int32_t connId = android_atomic_inc(&mNextConnId);
    sp<Client> c = new Client(this, pid, connId, client, audioSessionId, IPCThreadState::self()->getCallingUid());
    ALOGV("Create new client(%d) from pid %d, uid %d,",connId, pid, IPCThreadState::self()->getCallingUid());
    //表示效验调用方法的uid,用来进行身份验证
    wp<Client> w = c;//把构造的client 强引用对象赋值成弱引用对象
    {
    	Mutex::Autolock lock(mLock);//互斥锁
    	//mClients 声明为SortedVector < wp<Client> >
    	mClients.add(w);
    }
    return c;
}
/* 在new Client中, 有一个IPCThreadState.在Android中ProcessState是客户端和服务器端公共的部分,作为Binder通信的基础.ProcessState是一个singleton类,每个进程只有一个对象,这个对象负责打开Binder驱动,简历线程池,让其进程里面的所有线程都能通过Binder通信.
	与之相关的是IPCThreadState,每个线程都有一个IPCThreadState,实例登记在Linux线程的上下文附属数据中,主要负责Binder的读取、写入和请求处理.IPCThreadState在构造的时候获取进程的ProcessState并记录在自己的成员变量mProcess中,通过mProcess可以获得Binder的句柄.IPCThread通过 IPCThreadState::transact把data及handle等填充进binder_transaction_data,在两个进程间通信.
	这里这个Client到底是什么?我们又得追踪一下,在frameworks/av/media/libmediaplayer-service/MediaPlayerService.h
*/
class CLient : public BnMediaPlayer
{
	//IMediaPlayer 接口
	virtual void disconnect();
	virtual status_t 	setVideoSurfaceTexture(const sp<IGraphicBufferProducer>& bufferProducer);
	virtual status_t 	prepareAsync();
	virtual status_t 	start();
	virtual status_t	stop();
	virtual status_t	pause();
	virtual status_t	siPlaying(bool* state);
	virtual status_t	setPlaybackSettings(const AudioPlaybackRate& rate);
	virtual status_t	getPlaybackSettings(AudioPlaybackRate* rate/* nonnull */);
	virtual status_t 	setSyncSettings(const AVSyncSettings& rate, float videoFpsHint);
	virtual status_t 	getSyncSettings(AVSyncSettings* rate/* nonnull */, float* videoFps /* nonnull */);
	virtual status_t	seekTo(int msec);
	virtual status_t	getCurrentPosition(int* msec);
	virtual status_t	getDuration(int* msec);
	virtual status_t 	reset();
	virtual status_t	setAudioStreamType(audio_stream_type_t type);
	virtual status_t	setLooping(int loop);
	virtual status_t	setVolume(float leftVolume, float rightVolume);
	//省略部分代码
}//Client

```















