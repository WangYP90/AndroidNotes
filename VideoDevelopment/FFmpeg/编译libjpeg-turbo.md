## 编译libjpeg-turbo
### 安装必须编译软件
- GNU-M4
`wget http://mirrors.kernel.org/gnu/m4/m4-1.4.18.tar.gz \ && tar -xzvf m4-1.4.18.tar.gz && cd m4-1.4.18 \ && ./configure --prefix=/usr/local && make && make install`   
上面的那段命令,如果版本有更新[GUN-M4](http://ftp.gnu.org/gnu/m4/)这里找自己需要的版本
- autoconf
`wget http://mirrors.kernel.org/gnu/autoconf/autoconf-2.69.tar.gz && tar -xzvf autoconf-2.69.tar.gz && cd autoconf-2.69 && ./configure --prefix=/usr/local && make && make install`  
同理上面[autoconf](ftp://ftp.gnu.org/gnu/autoconf/)  
- automake
`wget http://mirrors.kernel.org/gnu/automake/automake-1.16.1.tar.gz && tar xzvf automake-1.16.1.tar.gz && cd automake-1.16.1 && ./configure --prefix=/usr/local && make && make install`  
同理[automake](http://ftp.gnu.org/gnu/automake/)
- libtool
`wget http://mirrors.kernel.org/gnu/libtool/libtool-2.4.6.tar.gz && tar xzvf libtool-2.4.6.tar.gz && cd libtool-2.4.6 && ./configure --prefix=/usr/local && make && make install`
同理[libtool](http://www.gnu.org/software/libtool/)
- nasm (编译x86库需要nasm)
`wget http://www.nasm.us/pub/nasm/releasebuilds/2.13.03/nasm-2.13.03.tar.gz && tar xzvf nasm-2.13.03.tar.gz && cd nasm-2.13.03 && ./configure --prefix=/usr/local && make && make install`  
- cmake
`wget https://github.com/Kitware/CMake/releases/download/v3.14.3/cmake-3.14.3.tar.gz && tar xzvf cmake-3.14.3.tar.gz && cd cmake-3.14.3 && ./configure --prefix=/usr/local && make && make install`
