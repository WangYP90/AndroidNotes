### Shell脚本编程
在ubuntu里面 默认使用bShell(bash shell),cShell,dShelll,我们现在写的这些命令实际上都是shell命令,之所以写到.sh脚本文件里面,是因为命令过于复杂.

#### linux命令:
清空命令行:	reset
文件操作:
ls -l
ls -la 所有
ls -l ja* 通配符查找

创建文件
touch today.c

复制文件
cp src.txt dest.txt
cp -i src.txt dest.txt	 询问
cp src.txt .	复制到当前目录
cp -R 目录 dest 复制整个目录到指定路径
cp c_?1 ../ 	通配符复制到上级目录

删除文件
rm file

删除目录
rm -rf 目录

查看文件类型:
file 文件

查看文件内容:
cat file
cat -n file 显示行号

查看进程
ps -ef|grep java
ps -ef|grep python
查看日志
tail -f file

查看文件最后10行
tail -n 10 file
查看文件最开始的10行
head -n 10file

创建用户
useradd --helper 	查看命令的帮助
useradd -m jack 创建用户的同时 创建了home 目录
userdel -r jack 删除用户
/home/wang/bashrc  	是一个启动文件,在这个文件中添加了环境变量(该用户的全局环境变量)

创建组:
groupad androidgroup
分配用户到组
usermod -G androidgroup jack

创建组和创建用户的,便于管理团队和分配个人必要权限.



文件权限

drwxr -xr -x
d rwx r-x r-x
d目录
rwx r读 w写 x执行,root用户对该目录拥有这些权限.
(第一个):文件所属用户具备的权限(root对该文件具备读写执行权限)
(第二个):文件所属用户的所属组具备的权限(读.执行)
(第三个):系统的其他用户对该文件具备的权限


rwx必须是固定顺序

权限		二进制		八进制
---			000			0
--x			001			1
-w-			010			2
-wx			011			3
r--			100			4
r-x			101			5
rw-			110			6
rwx			111			7

文件权限修改: chmod 644 文件
给用户加上执行权限chmod u+x 文件
给用户取消执行权限chmod u-x 文件

umask 022 改变创建文件时默认的权限, 创建后的权限就是 777-022 = 755.

chown 用户 文件 更改文件的用户权限

su 用户 ,切换用户.
#### shell脚本和命令
```shell
if 	命令
then
	命令
fi

test数值比较
-gt 大于
-eq 等于
-le 小于
-ne 不等于
test字符串比较
str1 == str2
str1 != str2
str1 < str2 (比较ASIC2 码)
-n str1 长度是否非0
-z str1 长度是否为0

文件比较
-d 检查是否存在,并且是一个目录
-e 检查file是否存在
-f 检查是否存在,并且是一个文件
-r 检查是否存在,并且可读
-w 同理(检查是否存在,并且可写)
-x 同理(检查是否存在,并且可执行)
file1 -nt file2 file1比file2新
file1 -ot file2 file1比file2旧

case命令
case 变量 in
pattern1) 命令;;
pattern2) 命令;;
*) 默认命令;;
esac

for命令
for var in list
do
	命令
done

while命令
while test command
do
	命令
done


#以ffmepg为例,enable命令 其实就是进了configure 里面的 enable命令的case里面了,然后就走了相应的逻辑.
for opt do
    optval="${opt#*=}"
    case "$opt" in
上面这段代码就是把我们传的参数,拿去遍历,然后case判断是哪个种类.



输入输出重定向
#0 STDIN
#1 STDOUT 标准输出
#2 STDERR
./xxx.sh 1> 文件路径     1> 将标准输出重定向到文件中,
#永久重定向
exec 1>test7

echo "cc xx "
echo "ll yy"

ls -a ./ff_error


#输入重定向



function myfun {
   echo "myfun"
}

myfun

myfun() {
  echo "myfun2"
}

myfun



```

#### shell脚本命令
```shell
$# 是启动脚本时携带的参数个数
-ne 是不等于
# 例如:if [ $# -ne 1 ];then  这句的意思是如果启动时参数个数不等于1个
$1 表示第一个参数

#整数比较
-eq     等于,如:if ["$a" -eq "$b" ]
-ne     不等于,如:if ["$a" -ne "$b" ]
-gt     大于,如:if ["$a" -gt "$b" ]
-ge    大于等于,如:if ["$a" -ge "$b" ]
-lt      小于,如:if ["$a" -lt "$b" ]
-le      小于等于,如:if ["$a" -le "$b" ]
<  小于(需要双括号),如:(("$a" < "$b"))
<=  小于等于(需要双括号),如:(("$a" <= "$b"))
>  大于(需要双括号),如:(("$a" > "$b"))
>=  大于等于(需要双括号),如:(("$a" >= "$b"))

另外：$?是shell变量,表示"最后一次执行命令"的退出状态.0为成功,非0为失败.



# tr命令:
一般格式为:
tr -c ["string1_to"] ["string2_to"] ,用字符串1中字符集的补集替换次字符,要求字符集为ASCII
tr -d ["string1_to"] ["string2_to"]	,删除字符串1中所有输入字符
tr -s ["string1_to"] ["string2_to"]	,删除所有重复出现字符序列,只保留一个,即将重复出现字符串压缩为一个字符串

## 字符范围
[a-z]:a-z内的字符组成的字符串
[A-Z]:A-Z内的字符组成的字符串
[0-9]:数字串
/octal:一个三维的八进制数,对应有效的ASCII字符
[0*n]:表示字符0重复出现指定次数n,例[0*2]表示匹配[00]字符串

## 保存输出
要保存输出结果,需将之重定向到一个文件,例:重定向输出到文件results.txt,输入文件cops.txt:
$tr -s "[a-z]" results.txt

## 去除重复出现的字符
$more cops.txt
And the cowwwwws went homeeeeeee
Or did theyyyy
如果要去除重复字符或将其压缩在一起,可以使用-s选项,因为都是字母,故使用[a-z]:
$tr -s "[a-z]"And the cows went home
Or did they
## 删除空行
可使用-s来作这项工作,换行的八进制表示为\012,例:
$more plane.txt
and 0500 399999 2773888
or 093999 3766666
data 39

tr -s "[\012]"
and 0500 399999 2773888
or 093999 3766666
data 39

## 大小写转换
除了删除控制字符,转换大小写是tr最常用的功能,为此需指定即将转换的小写字符[a-z]和转换结果[A-Z]
$ echo "May Day,May Day,Going Down..."
$ tr "[a-z]" "[A-Z]" "May Day,May Day,Going Down..."
MAY DAY,MAY DAY,GOING DOWN...
同样也可以使用字符类[:lower:]和[:upper:] ,使用是一样的.

```



#### shell脚本练习
```shell
#!/bin/bash
# num 类型赋值
NDK=10
# 属性 字符串赋值
text="I love sheell"
# 波浪线下面的`` 引用一个命令
text1=`date`
# $(who) 也是同样的效果,引用一个命令
text2=$(who)


echo $NDK
echo $text
echo "$text very match"
# 对特殊字符需要打印,得用\转译
echo "\$NDK"

echo $text1
echo $text2


# if 嵌套语句 只能写命令,不写命令得用test 命令
#!/bin/bash
testuser=wang01
# 判断用户是否存在, 找到返回数据,没找到返回0; 0是true,取非,就是找到了.
if !(grep $testuser /ect/passwd)
then
    echo "ok"
    ls -a /home/$testuser/
#
elif ls -d /home/$testuser
then
    echo "用户不存在,主目录仍然存在"
#
else
    ##嵌套if 
    #if   
    #then
    echo "$testuser not exist"
fi       
#  


#!/bin/bash
#test命令.如果条件成立,test命令状态为0退出,if条件成立. 提供了快捷方式 [] 括起来
var="lala"
if test $var
then
    echo "test条件成立"
else
    echo "test条件不成立"
fi
#快捷方式 if [$var]
#!bin/bash
a=10
b=5
#test命令简单形式 [] 前后必须加空格,不加空格报错
if [ $a -gt $b ]
then
	echo "$a greater than $b"
else
	echo "$a smaller than $b"
fi



#!/bin/bash
#test命令.如果条件成立,test命令状态为0退出,if条件成立.
var="lala"
if test $var
then
    echo "test条件成立"
else
    echo "test条件不成立"
fi
#类似于这样
strl=""
if [ strl = "" ]
then
    echo "有内容"
else
    echo "没内容"
fi


a=10
b=5
#test命令简单形式
if [ $a -gt $b ]
then
    echo "$a greater than $b"
else
    echo "$a smaller than $b"
fi


mydir=/usr/wang

#-d检查目录是否存在
if [ -d $mydir ]
then
    echo "$mydir exist"
    cd $mydir
    ls -la
else
    echo "mydir not exist"
fi

#多个条件
mydir2=/usr/wang/android_all
#任意数学赋值或者比较表达式
if [ -d $HOME ] && [ -w $mydir2 ]
then
    cd $mydir2
    touch test1.txt
else
    echo "no"
fi

#switch 语句
testuser2=wang

case $testuser2 in
wang)
     echo "hi,$testuser2";;
ricky)
     echo "hello,ricky";;
*)
     echo "defaults";;
esac

#IFS字段分隔符
list="Mother\'s Father Brother"
list2="windows--linux--macos"
IFS=$--
for item in $list2
do
    echo $item
done

#赋值
a=10
while [ $a -gt 0 ]
do
    echo "num:$a"
    #赋值不用使用$符号
    a=$[ $a - 1 ]
    if [ $a -eq 5 ]
    then
        echo "break"
        break
    fi
done

```


#### shell脚本练习-编译ffmpeg
```shell
#早期版本编译ffmpeg,编写shell之前,执行 ./configure --disable-yasm,检查系统版本,生成config.mak配置文件(比如说检查gcc的版本,生成一个config.mak,老版本在ffmpeg根目录下,新版本在ffbuild文件夹里面), 禁用软件yasm
#nasm和yasm fasm 

#编译过程中, gcc会生成 .o 临时文件,最后再链接生成动态库

#--enable-cross-compile gcc支持交叉编译,但是得配置使用,在linux上进行编译最终都是使用的gcc进行编译的.  交叉编译(编译不同操作系统的动态库,编译不同CPU平台的动态库,比如在linux下编译armCPU架构的动态库)


```














