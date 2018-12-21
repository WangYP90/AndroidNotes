# C语言入门

## C语言须知和开始
### 初识C语言
1987年布莱恩柯林汉和丹尼斯里奇合作的C语言程序设计第一版是公认的C标准:
* 第一个ANSI/ISO C标准   
通常叫做C89(因为ANSI与1989年批准该标准)或C90（因为ISO与1990奶奶批准该标准），由于ANSI先公布C标准，因此业界人士通常使用ANSI C。  
该委员会指定的指导原则中，最有趣的可能是:保持C的精神。委员会在表述这一精神时列出了以下几点：
    * 信任程序员
    * 不要妨碍程序员做需要做的事
    * 保持语言精练简单
    * 只提供一种方法执行一项操作
    * 让程序运行更快，即使不能保证其可移植性  
* C99标准
1994年，ANSI/ISO联合委员会开始修订C标准，最终发布了C99标准。  
    * 支持国际化编程            例如：提供多种方法处理国际字符集
    * 调整现有实现致力于解决明显缺陷 例如：需要将C移至64位处理器
    * 为适应和工程项目中的关键数值计算，提高C的适应性。
* C11标准
标准委员会在2007年承诺C的标准下一个版本是C1X，2011年终于发布了C11标准。此次，委员会提出了一些新的指导原则。处于对当前编程安全的担忧，不那么强调“信任程序员”目标了。而且，供应商并未像对C90nayang很好地接受和支持C99。这使得C99的一些特性称为C11的可选项。因为委员会认为，不应要求服务小型机市场的供应商支持其目标环境中用不到的特性。另外需要强调的是，修订标准的原因不是因为原标准不能用，而是需要跟进新的技术，比如，新标准添加了可选项支持当前使用多处理器的计算机。对于C11标准，我们浅尝辄止。
### 开发工具
在Windows软件开发中，Microsoft Visual Studio及其免费版本Microsoft Visual Studio Express 都久负盛名，它们与C标准的关系也很重要。然而，微软鼓励程序员从C转向C++和 C#。虽然Visual Studio 支持C89/C90，但是到目前为止，它只选择性的支持那些在C++新特性中能找到的C标准（如，long long 类型）。而且，自2012版本起，Visual Studio 不再把C作为项目类型的选项。尽管如此，本书中的绝大多数程序仍可用Visual Studio来编译。
* Visual Sutdio 32位以下面的方式    
在新建项目时，选择C++选项，然后选择【Win32 控制台应用程序】，在应用设置中选择【空项目】。几乎所有的C程序都能与C++程序兼容。所以，本书中的绝大多数C程序都可作为C++程序运行。或者，在选择C++选项后，将默认的源文件扩展名.cpp替换为.C，编译器便会使用C语言的规则代替C++。
* Visual Studio 64位 以下面的方式：  
1. 在新建项目时，选择C++选项，然后选择Windows桌面向导，点击确认-项目新建成功，其他选项里面选择空项目-点击确认，在`源文件`新建一个项，选择C++文件，把.cpp更改为.c。 然后就可以运行了   
这里需要注意的是 一个拥有main()方法的.c文件就是一个独立的项目，所以需要运行更多的main()方法，那么只有一种办法就是点击解决方案第一个右键继续新建一个项目（循环上一步），然后编码，编码完成后，点击该项目右键，设置为启动项目。
2. 在新建项目时，选择C++选项，然后选择Windows控制台应用程序，点击确认-项目新建成功，在`源文件`里面有个 xxx(项目名).cpp 更改为.c，报错的代码删除，就可以直接运行了。


### C 语言语法
#### 基本要求和变更
```c
int main(void)  void main()	void main(String[] args) 也可以像java一样这样. 
/* 双斜线 单行注释 是C99标准以后加的
C90，要求吧变量声明在块的顶部，其他语句不能任何声明的前面。 C99和C11 改变了这个的限制，可以随意声明。
C99 和C11 允许使用更长的标识符名（变量名），但是编译器只识别前63个字符。C90只允许6个字符，旧式编译器通常最多只允许8个字符。
变量名可以用 大、小写字母和下划线来命名。第一个字符必须是字符或下划线，不能是数字。
*/
```
#### 数据类型和使用
##### 基本数据类型和使用
* 平常基本使用就使用 int long short unsigned char float double 就完了, 所以只需要有哪些东西存在就行了,真的在使用比较大的数做运算处理的时候, 再去使用它们.  

数据类型 | printf  
---|---  
int | %d  
short | %d 或者 %hd  
long | %d  
float | %d 或者 %hd  
double | %d  
char | %d 或者 %hd  
16进制 | %x  
8进制 | %o  
字符串 | %s  
16进制long 类型 | %lx  
8进制打印long 类型 | %lo  
字符串 | %s  
指数基数法的浮点数 | %e (如果系统支持16进制格式的浮点数),可用a和A分别代替e和E  
short int | %d  
long int |	%ld (在32位环境中其实是一样的long int  和long)  
long long |	%lld  
long long int | %lld  
unsigned long | %lu  
int32_t(是int类型的别名，是精确宽度整数类型，int32_t表示的宽度正好为是32位的时候，但是计算机底层系统不支持。因此这是个可选项。如果系统不支持精确宽度整数类型怎么办？C99和C11提供了第2类别名集合：) | %" PRId32 "  
int_least8_t (至少是8位有符号的整数值的一个别名)| %" PRId32 "  
int_fast8_t (至少8位有符号值而言运算最快的整数类型别名)| %" PRId32 "  
intmax_t (C99定义了 intmax_t （最大的有符号整数） 类似的还有 unitmax_t 表示最大无符号整数类型。 这些类型可能比 long long 和 unsigned long 类型更大。) | %" PRId32 "  
int32_t | %" PRId32 "(上面的这些可移植类型，在printf 打印的时候 有些使用 %d，有些使用 %ld ，C标准针对这一情况提供了 RPId32 \ 64 字符串宏来显示可移植类型。  还有 PR (Id li lo lu ) 很多种 宏)  

```c++
//stdudio.h  相当于java 的lang包,在C中studio.h 是C语言的一个标准库, .h代表只有函数的声明,没有函数的实现,因为C语言是系统底层的语言,所以他的这些函数,都在window,linux 有不同的实现,在编译器把.c 或者C++的.cpp文件编译完成后,执行的时候我们调用的这些声明函数就会去系统底层找自己的实现. 
#includeo <studio.h> //include 预处理指令，类似java 的import
void main(){
	printf("n = %d, n squared = %ld, n cubed = %f\n", int, long,float);
}



/*

char 类型：
char 类型存储的一个字符，但是从技术层面看，char是整数类型。因为char类型实际上存储的是整数而不是字符，计算机使用数字编码来处理字符，即用特定整数表示特定的字符，参考ASCII编码表。

转义序列:
//		\a		警报 
//		\b		退格
//		\f		换页
//		\n		换行
//		\r		回车
//		\t		水平制表符
//		\v		垂直制表符
//		\\		反斜杠(\)
//		\'		单引号
//		\"		双引号
//		\?		问号
//		\0oo	八进制(oo必须是有效的八进制数，即每个o可表示0~7中的一个数)
//		\xhh	十六进制(hh必须是有效的十六进制数，即每个h可表示0~f中的一个数)


*/

```
##### 指数记数法(插曲) 
因为上面有个指数记数,学习了就记个笔记.
数字 | 科学计数法 | 指数记数法
---|---|---
100000000 | 1 X 10（9次方） | 1.0e9
322.56 | 3.2256 X 10(2次方) | 3.2256e2
0.000056 | 5.6 X 10(-5次方) | 5.6e-5


```c
C标准规定，float类型必须至少能表示 10(-37次方) ~ 10 (37次方)， float 至少精确到小数点后6位。
//	double类型和float类型的最小取值范围相同，但是至少必须能表示10位有效数字。 （经验： 不管系统差异给小数部分分多少，一般情况下，double至少有13位小数精度）
//	C99 标准添加了一种新的浮点型常量格式--用十六进制表示浮点型常量。十六进制前加（0x或0X），用p 或P 分别代表e或E， 例如： 0xa.1fp10 ,打印16进制浮点型 使用 %e或%le
//	使用C语言,很多公司都有系统化的命名规范,比如 int i_smart; unsigned short us_versmart,一看命名就知道是什么类型
//	复数和虚数：
```
##### 定义常量
```c
#define DENSITY 62.4  // 定义一个常量

#include <limits.h>
#include <float.h>
void main()
{
	char name[40];
	/*
	定义常量:
	#define DENSITY 62.4 这样像include 一样定义的常量,程序编译时,程序中的所有DENSITY 都被替换成了62.4,这个过程称为编译时替换,通常这种常量也叫做明示常量
	C90 标准新增 const 关键字, const int MONTHS = 12; 用于限定一个变量为只读.
	C头文件 limits.h 和 float.h ,


	C 语言 char类型数组必须以\0(ASCII对应0) 结束,那么40个就只能存储39个字符.  'x' 和"x" 就不同 一个是char基本类型,一个是char数组类型(后面就是\0)
	sizeof(name) = 40;    strlen(name) = 实际存储的字符长度;

	*/
}
```
##### 指针
* 指针是什么?   
指针是一个变量,来存储int char ..... 用来存储各种类型定义的变量的内存地址. 而且指针还需要有类型.  
* 指针还需要有类型?  
那么在C中指针为什么要有类型,因为指针存储的是一个变量的内存地址,那么如果有人要使用指针去拿取内存地址的数据的时候,该怎么去拿呢,即使拿出来又该怎么判定它是什么类型的呢.  
指针有类型,地址没有类型.,地址只是开始的位置,类型读取到上面位置结束,(例如:如果你是int类型 加入int占4字节,那么指针的类型就告诉你读到哪里结束.  内存地址告诉你从内存哪个位置开始读.)  
* 使用
& 去地址符 (int i = 89;       int *p = &i);  定义一个变量 i = 89,  &i  拿到 i 变量的内存地址,赋值给 int *p 一个int 类型的指针变量.
```c
int main(void)
{
  int i = 89;
  int *p = &i; //
  double j = 78.9;
  //赋值给double 类型变量的地址
  p = &j;
  printf("double size %d\n",sizeof(double));
  printf("%#x,%lf\n",p,*p);
}
```
* 空指针
空指针的默认地址为0,访问内存地址0,操作系统不允许.
```c
void main() {
	int i = 9;
	int *p = NULL;
	//p = &i;
	printf("%d\n", *p); //空指针的默认地址为0,访问内存地址0,操作系统不允许.
	getchar();
}
```
* 二级指针 和多级指针
JNIEnv 是 jni.h 中 _JNIEnv 就是一个JNINativeXXX* 就是一个指针,
多级指针(二级指针),指针保存的是变量的地址,保存的变量还可以是一个指针变量
动态内存分配 二维数组,都需要用的到多级指针
```
void main() {
	int a = 50;

	int* p1 = &a;

	int** p2 = &p1;

	int*** p3 = &p2;
	printf("p1 : %#x, p2 : %#x", p1, p2);


	int* qP1 = *p3;
	int* qP2 = *p2;
	int qa = *qP2;


	//等同于
	int qa1 = ***p3;
	printf("qa %d",qa);
	//printf("p2 %#x",);
	getchar();
}
```
* 指针的运算
```c
void main() {
	//int ids[] = {78, 90, 23, 65, 19};
	////数组变量名ids就是数组的首地址,下面这三个打印出来的地址是一样的
	//printf("%#x \n", ids);	
	//printf("%#x \n", &ids);
	//printf("%#x \n", &ids[0]);

	////指针变量
	//int *p = ids;
	//printf("%d \n", *p);
	////指针的加法, +1 向前移动sizeof(数据类型)个字节, 相当于直接移动到数组的下一个index 的地址.
	///*数组在内存中是连续存储的,
	//*/
	//p++;

	//printf("%d \n", *p);



	//高级写法
	int uids[5];
	//int i = 0;
	//for (int i = 0; i < 5; i++)
	//{
	//	uids[i] = i;
	//}
	//早些版本的写法,p<uids+5 uids是0 index 的内存地址,+5 index-5 后面的那个内存地址,p初始值就是uids,那么p++ 直到循环完毕.
	int* p = uids;
	printf("%#x\n",p);
	int i= 0;
	for (;p< uids+5; p++)
	{
		*p = i;
		i++;
	}
	
	getchar();
}
```
* 函数指针
```c
void msg(char* msg, char* title) {
	MessageBox(0, msg,title,0);
}

//函数指针
void main() {
	//msg();
	printf("%#x msg\n",msg);
	printf("%#x msg\n", &msg);
	//函数指针 void函数返回值类型 (*fun_p)函数指针的名称,() 函数的参数列表
	void(*fun_p)() = msg;
	fun_p("xiaoxi内容","biaoti");

	getchar();
}
```
* 练习 (用随机数生成一个数组,写一个函数查找最小值,并返回最小数的地址,在主函数总打印出来)
```c
int* getMinPointer(int ids[], int len) {
	int i = 0;
	int* p = &ids[0];
	for (;  i< len; i++)
	{
		if (ids[i] < *p) {
			p = &ids[i];
		}
	}
	return p;
}


void main() {
	int ids[10];
	int i = 0;
	//初始化随机数发生器,设置种子,种子不一样,随机数才不一样
	//当前时间作为种子
	srand((unsigned)time(NULL));
	for (int i = 0; i < 10; i++)
	{	
	
		ids[i] = rand() % 100;//生成随机数  rand不能生成 必须指定种子  模100就是100以内的数.
		printf("%d  ",ids[i]);

	}
	int* p_1 = getMinPointer(ids, sizeof(ids) / sizeof(int));
	printf("%#x, %d \n",p_1,*p_1);
	getchar();
}
```
##### 动态内存
为什么要用动态内存:  
静态内存分配,分配内存大小是固定的,问题: 1. 很容易超出栈内存最大值 2.为了防止内存不够用会开辟更多的内存
动态内存分配,在程序运行过程中,动态指定需要使用的内存大小,手动释放,释放之后这些内存还可以供其他程序使用或重新使用.

* 动态内存分配
```c
void main() {
	//40M
	//stack overflow 错误,栈溢出
	//int a[1024 * 1024 * 10]; //在栈区直接分配40M内存,正常来说window下面,一个应用程序分配的栈内存大小在2M,所以这样申请会报stack overflow
	/*C语言的内存分配
	1.栈区(stack)		自动分配,自动释放 ,一般是局部变量,用完立马就释放了.
	2.堆区(heap)		程序员手动分配和释放,可以分配大概操作系统80%左右的内存.	
	3.全局区或静态区	
	4.字符常量区
	5.程序代码区
	*/


	//在堆内存上分配一个400M的内存 
	// malloc 返回的是void* 代表,它可以返回任意类型的指针 , 所以这里
	int* p = malloc(1024 * 1024 * 100 * sizeof(int));

	
	getchar();
	//释放该内存
	free(p);
}
```
* 动态内存分配 +扩充
```c
void main() {
	//静态内存分配,数组的大小是固定的
	int len;
	printf("第一次输入数组的长度:");
	scanf("%d", &len);

	
	int* p = malloc(len * sizeof(int));
	//int* p = calloc(len, sizeof(int));  //还有一种写法 calloc 不用自己算,传进去它自动帮你算
	//赋值
	int i = 0;
	//p 是内存地址第一个int 4字节的地址, 所以i++ 就直接给内存地址赋值就可以了
	for (; i < len; i++)
	{
		p[i] = rand() % 100;
		printf("%d, %#x \n",p[i],&p[i]);
	}

	int addLen;
	printf("第二次增加数组的长度:");
	scanf("%d", &addLen);
	//realloc 重新分配内存, 之前开辟的内存指针, 需要扩容或缩小的大小,扩大会
	//扩大内存: 
	//1. 若果当前内存段后面有需要的内存空间,直接扩展,返回原指针
	//2. 当前内存段后面的空闲字节不够, 那就使用堆中第一个能满足这一要求的内存区域,把原内存数据copy,然后释放原内存,返回新的指针.
	//3. 如果申请失败,返回NULL,原来的指针依然存在


	int* p2 = realloc(p,sizeof(int) * (len +addLen));
	if (p2 == NULL) {
		printf("重新分配内存失败!!!!!!");
	}

	i = 0;
	for (; i < (len + addLen); i++)
	{
		p2[i] = rand() % 100;
		printf("%d, %#x \n", p2[i], &p2[i]);
	}

	if (p2 != NULL) {
		free(p2);
		p2 = NULL;
	}
	getchar();
}
```
* 动态内存的释放 和内存泄漏
不能多次释放,释放完成后置NULL,标志释放完成
内存泄漏    指针重新赋值不等于 释放前内存,  如果不做释放 会发生内存泄漏.
```c
void main() {
	//40M
	int *p1 = malloc(1024* 1024 * 10 * sizeof(int));
	//if (p1 != NULL) {
	//	free(p1);
	//	p1 = NULL;
	//}


	p1 = malloc(1024 * 1024 * 20 * sizeof(int));

	if (p1 != NULL) {
		free(p1);
		p1 = NULL;
	}


	getchar();
}

```
##### C 中的字符串
C语言中没有字符串,字符串可以用两种方式表示一种是 字符数组,一种是字符指针
字符数组: 这里的定长字符数组是固定的,可以修改某个index 字符的值,但是一经创建,长度无法改变.
字符指针: 字符指针是不能被修改的.
在C中也有像Java中一样的一个处理String 的类,这个 #include <string.h>,它里面有一些字符串的处理函数.  
在线文档:[string在线文档](http://www.kuqin.com/clib/string/strchr.html)
```c
//C中字符串两种创建方式
void main() {
	//1. 通过字符数组来 表示一个字符串
	//char str[] = {'c','h','i','n', 'a','\0'};
	//char str[6] = { 'c','h','i','n', 'a'};
	//char str[10] = "china";
	////可以修改
	//str[0] = 's';
	//printf("%s \n", str);


	//getchar();



	//2. 通过字符指针  
	// 利用字符数组表示的可以修改, 利用指针表示的数组不能被修改.  和java 中类似,string不可变,stringBuffer可变
	//内存连续排列
	char* str = "how are you?";

	//str[0] = 's'; //写入位置时发生访问冲突


	printf("%#x \n", str);
	printf("%s \n", str);
	str += 3;
	while (*str) {//取不到就为0, 为0就跳出了
		printf("%c", *str);
		str++;
	}
	getchar();
}
```
##### 字符串的处理函数 
在线API文档 [string在线文档](http://www.kuqin.com/clib/string/strchr.html)
* 字符串中匹配字符
```
void main() {
	char *str = "I want go to USA!";
	printf("%#x\n", str);

	char* p = strchr(str, 'w');
	if (p)
	{
		printf("索引位置:%d\n", p - str);
	}
	else
	{
		printf("没有找到");
	}
	system("pause");
}
```
* 字符串的拼接
```
void main() {
	char dest[50];
	char *a = "china";
	char *b = " is powerful!";
	
	strcpy(dest, a);
	strcat(dest, b);
	printf("%s\n", dest);
	system("pause");
}
```
##### 结构体
结构体相当于Java 中的Object,但是它又有C中的部分独有特性,很多时候我们看jni.h相关的代码或者WebRTC,会看到Google程序员会利用C结构体来模拟实现Java OOP编程的风格,使代码更加清晰,易读,易维护.
把不同的数据类型整合起来成为一个自定义的数据类型
* 定义结构体
```
struct Man {
	//成员
	char name1[20];
	char* name;
	int age;
	int(*func)();
	void*(*funj)();
};

int test() {
	return 1;
}

void main() {
	//初始化结构体的变量
	//1. 第一种形式
	//struct Man m1 = {"Java",20,};
	//2. 声明完成后,需要什么自己赋值
	struct Man m1;
	m1.age = 23;
	m1.name = "java";

	m1.func = test;
	strcpy(m1.name1,"Android"); //name1 是char数组,所以不能直接赋值,只能使用strcpy的方式
	
	printf("%s,%s, %d, %d\n", m1.name,m1.name1, m1.age, m1.func());

	getchar();
}
```
* 结构体变量 和 使用
```
struct Man {
	char *name;
	int age;
}m1, m2 = {"Java",20};

void main() {
	m1.name = "Android";
	m1.age = 10;
	getchar();
}

//匿名结构体,主要用在控制结构体变量的个数,相当于Java中单例
struct {
	int	age;
}m3;
void main() {
	m3.age = 5;
}
```
* 基本写法
```
// 结构体的嵌套: 这种写法 和下面的写法 没有任何区别
//struct Student {
//	char name[20];
//	int age;
//	struct Tracher {
//		char name[20];
//	}t;
//};


struct Teacher {
	char name[20];
};

struct Student {
	char name[20];
	int age;
	struct Teacher t;
};
void main() {
	//struct Student s1 = { "java",20,{"json"} };

	struct Student s1;
	s1.age = 10;
	strcpy(s1.t.name,"json");
	system("pause");
}
```
* 结构体于指针
```
struct Man {
	char name[20];
	int age;
};
void main() {
	struct Man m1 = {"Java", 20};
	struct Man *p = &m1;
	printf("%s,%d \n",m1.name,m1.age);
	printf("%s,%d \n",(*p).name,(*p).age);
	//-> 是(*p).的简写
	printf("%s,%d \n", p->name,p->age);
	system("pause");
}
```
* 结构体数组
```
struct Man {
	char name[20];
	int age;
};

int main() {
	struct Man mans[] = { {"Java", 20},{ "Android", 25}};
	//遍历结构体数组
	//1.
	struct Man *p = mans;
	for (; p < mans+2; p++)
	{
		printf("%s, %d\n",p->name, p->age);

	}
	//2.
	int i = 0;
	for (; i < 2; i++)
	{
		printf("%s, %d\n", mans[i].name, mans[i].age);
	}
	//3.
	int i = 0;
	for (; i < sizeof(mans) / sizeof(struct Man); i++)
	{
		printf("%s, %d\n", mans[i].name, mans[i].age);
	}

	//结构体的大小(字节对齐)

}
```
* 结构体的大小 和字节对齐的概念
结构体的大小该怎么计算呢?,那得先说说什么是字节对齐:  
结构体必须满足 最大的数据类型的sizeof的值整除, 所以呢所有的属性呢都会变成8个字节,即使空出来一块.  
结构体变量的大小必须是最宽基本数据类型的整数倍. 如果不够,那么他就会在这些成员中间需要添加填充字节.
```
struct Man {
	int age;
	double weight;
};
void main() {
	struct Man m1 = {20, 170.5};
	printf(" %#x,%d,\n", &m1, sizeof(m1));
	getchar();
}
```
* 结构体与动态内存分配
```
struct Man {
	char  *name;
	int age;
};

void main() {
	struct Man *m_p = (struct Man*)malloc(sizeof(struct Man) *10);
	struct Man *p = m_p;
	赋值
	p->name = "Jack";
	p->age = 20;
	p++;
	p->name = "Android";
	p->age = 21;

	struct Man *loop_p = m_p;
	for (; loop_p < m_p +2; loop_p++)
	{
		printf("%s, %d\n",loop_p->name ,loop_p->age);
	}
	free(m_p);
	getchar();
}
```
* 结构体取别名   
typedef 取别名:  
1. 不同的名称代表在干不同的事情,比如:typedef int jint;  
2. 不同情况下,使用不同的别名
```
#if defined  (_cplusplus);
//typedef _JNIEnv JNIEnv;
//typedef _JavaVM JavaVM;
*/
//struct Man {
//	char name[20];
//	int age;
//};
//
//typedef struct Man JavaMan;
//typedef struct Man* JM;
//
//typedef int Age; //Age int类型的别名
//typedef int* Ap;//Age int 类型指针的别名;

//结构体取别名
typedef struct Woman {
	char name[20];
	int age;
} W,*WP;//W是Woman 结构体的别名, WP 是woman 结构体指针的别名

void main() {
	int i = 5;
	Ap a = &i;

	W w1 = {"Java",20};
	WP wp1 = &w1;
	printf("%s ,%d \n", w1.name, w1.age);
	printf("%s ,%d \n",wp1->name, wp1->age);

	getchar();
}
```
* 结构体函数指针成员
C语言到最后搞来搞去,写的都是这样的代码:
```
typedef struct Girl {
	char* name;
	int age;
	void(*sayHi)(char*);
}Girl;
typedef Girl* GirlP; //Girl结构体指针取别名GirlP
//Girl 结构体类似于Java中的类.name和age 类似于属性,sayHi类似于方法.
void sayHi(char* text) {
	MessageBox(0,text,"title",0);
}

void rename(GirlP gp) {
	gp->name = "Android";
	//如果传Girl 类型进来,会重新开辟一个内存空间,用完就销毁了, 所以需要传指针进来
}
void main() {
	struct Girl g1 = {"Lucy",18,sayHi};
	g1.sayHi("hello");

	GirlP gp = &g1;
	rename(gp);
	gp->sayHi("Byebye!");
	getchar();
}
```
##### 联合体 (共用体)
不同类型的变量共同占用一段内存(相互覆盖),联合变量任何情况只有一个内存存在,目的(节省内存). 
联合体变量大小= 最大的成员所占的字节数
```
union MyValue {
	int x;
	int y;
	double z;
};
void main() {
	union MyValue d1;
	d1.x = 90;
	d1.y = 100;
	d1.z = 23.8; //最后一次赋值有效
	printf("%d,%d,%lf \n",d1.x, d1.y,d1.z);
	getchar();
}
```
##### 枚举
枚举 :固定的数据 enumeration(主要用来列举所有的情况,限制值,保证取值的安全性)
```
enum Day {
	Mon,
	Tue,
	Wed,
	Thu,
	Fri,
	Sat,
	Sun
};
void main(void) {
	//枚举的值必须是括号中出现的值
	enum Day d = Mon;

	printf("%#x,%d\n",&d,d);
	getchar();

}
```
##### C语言中的文件操作
* 读取和写入文本
```
//写入
void main() {
	char path[] = "D:\\BaiduNetdiskDownload\\test.txt";
	//打开
	FILE *fp = fopen(path, "w");
	char *text = "测试;\n啦啦啦啦啦";
	fputs(text, fp);
	//关闭流
	fclose(fp);
	getchar();
}
//读取文本文件
void main()
{
	char path[] = "D:\\BaiduNetdiskDownload\\test.txt";
	//打开
	FILE *fp = fopen(path,"r");
	if (fp == NULL) {
		printf("文件打开失败...");
		return;
	}
	//读取
	char buff[50];
	while (fgets(buff,50,fp)) {
		printf("%s", buff);
	}
	//关闭
	fclose(fp);
	getchar();
}
```
* 文件复制
二进制读取写入文件,计算机的文件存储在物理上都是二进制,文本和二进制之分,其实是一种逻辑之分,c读写文本文件与二进制文件的差别仅仅体现在回车换行,写文本时,每遇到'\n',会将其转成'\r\n'(回车换行),读文本时,每遇到一个'\r\n',都会将其转为'\n'.
```
void main() {
	char path[] = "D:\\BaiduNetdiskDownload\\timg.jpg";
	//读的文件 b字符表示操二进制文件binary
	FILE *read_fp = fopen(path, "rb");
	char write_path[] = "D:\\BaiduNetdiskDownload\\timg1.jpg";
	FILE *write_fp = fopen(write_path,"wb");
	
	//复制
	int buff[50];
	int len = 0;
	//fread(buff, sizeof(int), 50, fp) buff,缓冲区的类型的大小,一次读多少,read_fp
	while ((len = fread(buff,sizeof(int),50, read_fp)) != 0)
	{	
		fwrite(buff,sizeof(int),len,write_fp);

	}
	//关闭流
	fclose(read_fp);
	getchar();
}
```
* 获取文件大小
```
void main() {
	char path[] = "D:\\BaiduNetdiskDownload\\timg.jpg";
	FILE *fp = fopen(path, "r");
	//该函数可以直接操作文件指针, 操作指针到文件的任何位置,可以用来做断点续传和下载.
	//0代表偏移量, SEEK_END 定位的位置根据偏移量
	fseek(fp, 0 ,SEEK_END); 
	//返回当前文件指针,相当于文件开头的偏移量.
	long filesize = ftell(fp);
	printf("%ld\n", filesize);

	getchar();
}
```
* 文件的加密和解密
```
//解密
void decrypt(char crypt_path[], char decrypt_path[], char password[]) {
	//打开文件
	FILE *crypt_fp = fopen(crypt_path, "rb");
	FILE *decrypt_fp = fopen(decrypt_path, "wb");

	//一次读取一个字符
	int ch;
	int i = 0;
	int pwd_len = strlen(password);
	while ((ch = fgetc(crypt_fp)) != EOF) {
		fputc(ch ^ password[i % pwd_len], decrypt_fp);
		if (i == pwd_len)
		{
			i = 0;
		}
		else {
			i++;
		}
	}

	fclose(crypt_fp);
	fclose(decrypt_fp);
}
//加密函数
//异或 : 1^1 = 0 ; 0^0 = 0; 1^0 = 0
void encrypt(char normal_path[], char crypt_path[], char password[]) {
	//打开文件
	FILE *normal_fp = fopen(normal_path, "rb");
	FILE *crypt_fp = fopen(crypt_path, "wb");

	//一次读取一个字符
	int ch;
	int i = 0;
	int pwd_len = strlen(password);
	while ((ch = fgetc(normal_fp)) != EOF) {
		fputc(ch ^ password[i % pwd_len], crypt_fp);
		if (i == pwd_len)
		{
			i = 0;
		}
		else {
			i++;
		}
	}

	fclose(normal_fp);
	fclose(crypt_fp);
}
void main() {
	char normal_path[] = "D:\\BaiduNetdiskDownload\\timg.jpg";
	char crypt_path[] = "D:\\BaiduNetdiskDownload\\timg_crypt.jpg";
	char decrypt_path[] = "D:\\BaiduNetdiskDownload\\timg_decrypt.jpg";
	//加密
	//encrypt(normal_path, crypt_path, "ceshimima");
	//解密
	decrypt(crypt_path, decrypt_path, "ceshimima");

	getchar();
}
```
##### C语言的预编译 define
这里得说一下为什么要用Visual Studio, VS 可以看到内存情况,可以看到编译日志,.c 编译完成后就是.obj的目标代码.  
- 预编译： 为编译做准备工作,完成代码文本的替换工作。
- 编译: 形成目标代码(.obj)---()
- 连接:将目标代码和C函数库连接合并,形成最终的可执行文件
- 执行
```c
/*
例如  这两个头文件互相依赖,需要执行某些事情
A.h  
#include "B.h"  void printfA();

B.h  
#include "A.h"	 void printfB();

在早期的编译器必须这样才可以解决这种 头文件的互相引用冲突.
#ifndef BH
#define BH
#include "A.h"
void printfB();
#endif // !BH  

上面这个这个早期的处理只需要了解一下,新的编译器这样处理就可以了
#pragma once
*/

//2. 定义常数
#define MAX 100

//3. 定义一个宏函数
void wy_com_jni_read() {
	printf("read\n");
}
void wy_com_jni_write() {
	printf("write\n");
}
#define jni(NAME) wy_com_jni_##NAME();

//4. 日志输出
//__VA_ARGS__ 代表可变参数.   FOEMAT前加 ##代表参数,不加也能运行.
#define LOG(LEVEL,FORMAT,...)printf(##LEVEL);printf(##FORMAT,__VA_ARGS__);
#define LOG_I(FORMAT,...) LOG("INFO:",##FORMAT,__VA_ARGS__);
#define LOG_E(FOEMAT,...) printf("ERRO:");printf(##FOEMAT,__VA_ARGS__);
#define LOG_W(FORMAT,...) LOG("WARN:",##FORMAT,__VA_ARGS__);
void main() {
	//#include "my.txt" //给my.text 里面就写一个函数myprintf(char *msg){printf("%s",msg);};
	//myprintf("java"); //预编译一样可以通过,就是个文本替换.函数一样可以得到执行.

	//printfA();
	
	//2. 测试常数
	int i = 90;
	if (i < MAX) {
		printf("比MAX小");
	}
	
	//3.调用宏函数,只需要传入name就可以了 自动拼接成需要的函数名并且执行.
	jni(read); //调用的时候就看起来比较清爽了.
	
	//4. 测试 自定义LOG
	LOG_E("%s,%d\n","lallala",50); //测试自己的

	getchar();
}
```
## 结束语: C语言真的是博大精深,上面的这些例子只是C语言的一角,而我们学习只是为了当时的需求,而我的需求就是能够基本看懂jni.h, 能够使用JNI开发需求App就够了,然而学到最后你会发现,会的越多忘的越快. 所以,如果你当下需要学习C语言,可以把上面的代码敲一遍,虽然笔记是CV大法+一目十行,但是,敲一遍运行一下,提升和找错真的很快.

 * [github](https://github.com/WangYP90/c_smmple)
#### C语言学习资料
##### 网站资料大全
* [C语言中文网-C入门教程](http://c.biancheng.net/cpp/u/jiaocheng/)
* [菜鸟教程](http://www.runoob.com/cprogramming/c-tutorial.html)
##### C语言相关的书籍
* C程序设计语言     《The C Programming Language》
* C语言参考手册     《C Reference Manual》
* C primer plus第6版
* C陷阱与缺陷       《C Traps and Pitfalls》
* 指针的艺术
* 你必须知道的495个c语言问题
* C专家编程         《Expert C Programming》
* 《C标准库》
* 《 C语言接口与实现 》
* 《C语言的科学和艺术》
* 《C程序设计语言》
* 《数据结构与算法分析》
##### 最终彩蛋
- `C++在C语言的基础上嫁接了面向对象编程工具，面向对象是一门哲学，它通过对语言建模来适应问题，而不是对问题建模以适应语言。C++几乎是C的超集，这意味着任何C程序差不多就是一个C++程序。学习C语言，也相当于学习了许多C++的知识。`

