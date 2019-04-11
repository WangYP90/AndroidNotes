## Makefile
* GCC
GCC全称GNU Compiler Collection,是一个编译套件.现在很多C/C++的项目,最终的编译还是交给了GCC,之所以使用makefile,CMake等工具,是因为当项目达到一定规模,使用GCC的构建成本和时间就很大. 将.c的源文件编译生成.o的目标文件:
gcc .c文件 生成.o
gcc .o文件(可以多个) -o 打包生成的可执行程序路径

任何C/C++开源项目,都有一个makefile这样的文件.


makefile起到的作用, ANT构建工具 就是把class文件等资源构建成apk文件,makefile的作用也类似,就是让gcc把.c编译成.o的目标文件,链接得到可执行程序.

在C/C++的开源项目中,configure文件用来检查系统配置,生成配置文件(生成的这些配置问价你会在接下来的makefile文件用来生成我们需要的动态库文件),

虽然现在AS直接用CMake来构建,但是也有个问题,很多开源库依然只有Makefile.
- shell脚本 configure 主要是一些配置

Makefile 就是一个构建工具, 在构建的时候只需要一行命令make,就可以把整个工程构成.
make工具是一个批处理工具,它本身并没有编译和链接功能,makefile命令中包含了调用gcc(也可以是别的编译器)去编译某个源文件的命令
makefile 的缺点,如果换了平台(系统,CPU架构)又需要重新修改.

CMake是一个跨平台的安装(编译)工具,语法更加简单,编译过程也非常简单,他能够输出各种各样的makefile或者project文件,Cmake的出现,就可以更加简单的声场makefile文件给make命令.
Cmake根据CMakeLists.txt文件生成makefile文件.Cmake并不直接构建出最终软件,而是产生标准的构建档(如Unix的Makefile 或者 Windows Visual C++的projects/workspaces)

从上面的分析来看,make,makefile,CMake,CMakeLists, 在Shell脚本中的作用和意义:
总结:在Linux下编译,make命令需要makefile读取配置信息来执行,而如果使用CMake,那么需要写CMakelists来执行,生成makefile文件,然后再make执行该makefile.


为什么要写Makefile文件?
1.当项目非常庞大时,让构建过程,自动化,简单.
2.依赖文件比目标更新,会重新构建.

三要素:目标,依赖,命令
```
#三要素
#目标,依赖,命令
#myapp目标(最终目标,第一行是最终目标)
#:后的文件,这些都是依赖
#命令TAB键开头,如何由依赖文件得到目标
myapp:main.o plus.o minus.o multi.o divi.o
        gcc main.o plus.o minus.o multi.o divi.o -o myapp

#目标:main.o
#依赖:mian.c
#命令: gcc -c mian.c
main.o:main.c
        gcc -c main.c
plus.o:plus.c
        gcc -c plus.c
minus.o:minus.c
        gcc -c minus.c
multi.o:multi.c
        gcc -c multi.c
divi.o:divi.c
        gcc -c divi.c



在最后执行的时候,如果你更改了哪个.c文件,最终编译的时候只会编译你更改的文件.

make在执行的时候可以是下面几种形式
make 构建终极目标 上面的终极目标是myapp
make clean 执行clean命令
make main.c 单独执行

```



```
android.mk,旧的编译方式.
LOCAL_PATH := $(call my-dir)
#放在第一行,告诉NDK编译工具,我的mk文件在哪个目录下面编译
```