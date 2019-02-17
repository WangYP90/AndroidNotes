## 编译FFmpeg
由于我使用的Centos7 ,所以下面的命令,全部换成ubuntu 搜一遍也是ok的
1. 阿里云主机 Centos 7
2. 安装XSHell Xftp
安装Xftp
https://www.cnblogs.com/flandre/p/6051532.html
db_load命令: yum -y install db4
开启端口,如果防火墙使用的firewalld :
https://www.cnblogs.com/moxiaoan/p/5683743.html
Centos ftp启动方式变化:
systemctl start vsftpd.service
3. 上传NDK,FFmpeg
NDK下载 https://www.androiddevtools.cn/ 
FFmpeg http://ffmpeg.org/download.html#releases (我这里使用的4.1)  
- linux文件和文件夹权限
chmod 777 -R ndk目录   给目录给所有文件,执行 写入,读取等权限
- 安装 zip解压工具
yum install -y  unzip zip;
unzip 名称.zip -d 路径
- 解压 tar
https://www.jianshu.com/p/3d9effeef575
- 删除文件或者文件夹
https://blog.csdn.net/nicolas12/article/details/80890811
4. VI命令大全

添加权限: chmod u+x path


保存命令

按ESC键 跳到命令模式，然后：

:w 保存文件但不退出vi
:w file 将修改另外保存到file中，不退出vi
:w! 强制保存，不推出vi
:wq 保存文件并退出vi
:wq! 强制保存文件，并退出vi
q: 不保存文件，退出vi
:q! 不保存文件，强制退出vi
:e! 放弃所有修改，从上次保存文件开始再编辑

5. VIM 命令
https://blog.csdn.net/capecape/article/details/78503497
Vi 和VIM 在 进入文本文件的时候 ,一进去就是命令模式, 
进入命令模式: ESC
i 进入编辑

- 创建文件 
touch wtest.txt
命令模式退出: 1.  shift +: ,输入q!强制退出  2.  shift+ZZ
进入编辑模式: i:

- VIM 配置

6. 配置ndk环境变量
1. vim ~/.bashrc  ~代表用户,到当前用户的环境变量里面
2. 加入下面这两行:  
export NDKROOT=/usr/wang/android_all/ndk_build/android-ndk-r16-beta1/
export PATH=$NDKROOT:$PATH
3. 保存 ,source ~/.bashrc (更新环境变量,不然要重启)
4. ndk-build 
Android NDK: Could not find application project directory !    
Android NDK: Please define the NDK_PROJECT_PATH variable to point to it.    
/usr/wang/android_all/ndk_build/android-ndk-r16-beta1/build/core/build-local.mk:151: *** Android NDK: Aborting    .  Stop.  

5. 成功

7. build_android.sh
build_android.sh
-bash: build_android.sh: command not found
因为是在windows 下创建的,不是linux 可执行的
- yum install -y dos2unix   安装dos2unix
将windows .sh 转成linux 可执行文件



- ./configure --enable-libsrt --enable-libx264 --enable-libx265 --enable-gpl
- nasm/yasm not found or too old,  这是一个警告,  linux 下更新这个两个

- https://blog.csdn.net/biezhihua/article/details/86186420
* shell文件报错syntax error near unexpected token '$'\r''
```
https://www.jianshu.com/p/55597646fa84

本来跑的好好得一个文件，在windows下修改了，然后移植到linux就报错了。
找了一圈以下是解决方案：
这种情况发生的原因是因为你所处理的文件换行符是dos格式的"\r\n"
可以使用cat -v 文件名 来查看换行符是否是，如果是上述的，则行结尾会是^m
需要转换成linux/unix格式的"\n"
具体转换办法就是转换换行符
可以用sed命令处理一下文件，命令如下：
sed 's/\r//' 原文件 >转换后文件
再运行就Ok拉




.h.in what ?这个什么文件?
Files ending with .in are typically template files used by a program called configure that generates a new file without the extension after substituting for variable expansions. I.e., if you're looking at a source tree that has files called, e.g. Makefile.in in the tree, then ./configure will generate a usable Makefile that can be used to "make" from source.


java.awt.image.BufferedImage 
awt包下的 android 都不支持,所以必须用android 自带的Bitmap 来处理图片
```





