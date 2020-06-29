### Android_NDK开发
#### AndroidStudio 开发NDK



#### Eclipse 开发NDK流程
为什么要写Eclipse,我想说,我一个朋友去年入职某为外包合作公司的项目,里面的android项目依然是eclipse开发,而且项目总监还不让换.  
另外还有一个原因那就是,只有用了Eclipse你才知道为什么要用NDK,Java调用C,NDK,理解的更加深入.
* 编写Java层Native方法
* javah 命令生成头文件
* 配置Android-NDK
* 创建jni目录
* add native support 添加本地支持,将android工程转为C/C++工程
* Preferences->C/C++ General->Paths and Symbols ->右侧add 配置jni等信息.和VS开发JNI 是非常类似的,只不过依赖的是NDK-build里面的JNI.h, 和java-sdk的jni.h 是一样的.只是ndk做了封装.

#### android.mk 相关的移植
如果碰到有某个项目没有cmake 而是用的android.mk的方式,那就得在该文件夹下有jni目录,在jni目录下有android.mk文件(修改里面的配置为shell),然后运行ndk_build