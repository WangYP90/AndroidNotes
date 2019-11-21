## Flutter
### Dart语法描述
Flutter和Dart的关系,早起的Flutter谈对评估了十多种语言才选择了Dart,因为它符合构建用户界面的方式,一下是Flutter团队看重Dart语言的部分特性
- Dart是AOT(Ahead Of Time)编译的,可编译成快速、可预测的本地代码，使Flutter几乎都可以使用Dart编写。这不仅使Flutter变得更快，而且几乎所有的组件（包括小部件）都可以定制。
- Dart也可以JIT（Just In Time）编译,开发周期异常快,工作流颠覆常规(包括Flutter流行的亚秒级有状态热重载).
- Dart可以更轻松地创建以60fps 运行的流畅动画和转场.Dart可以在没有锁的情况下进行对象分配和垃圾回收.就像JavaScript一样,Dart避免了抢占式调度和共享内存(因而也不需要锁).由于Flutter应用程序被编译为本地代码，因此不需要在领域之间建立缓慢的桥梁（例如，JavaScript到本地代码）。它的启动速度也快得多。
- Dart使Flutter不需要单独的声明式布局语言（如JSX或XML），或单独的可视化界面构建器，因为Dart的声明式编程布局易于阅读和可视化。所有的布局使用一种语言，聚集一处，Flutter很容易提供高级工具，使布局更简单。
- 开发人员发现Dart特别容易学习，因为它具有静态和动态语言用户都熟悉的特性。并非所有这些功能都是Dart独有的，但Dart将这些功能组合得恰到好处，使Dart在实现Flutter方面独一无二。因此，没有Dart，很难想象Flutter像现在这样强大。  
当你想创建移动App、Web App、Command-line应用时，都可以使用Dart语言。
Dart重要的概念如下：
- 所有的东西都是对象，无论是变量、数字、函数等都是对象。所有的对象都是类的实例。所有的对象都继承自内置的Object类。这点类似于Java语言“一切皆为对象”。
- 程序中指定数据类型使得程序合理地分配内存空间，并帮助编译器进行语法检查。但是，指定类型不是必须的。Dart语言是弱数据类型。
- Dart代码在运行前解析。指定数据类型和编译时的常量，可以提高运行速度。
- Dart程序有统一的程序入口：main（）。这一点与Java、C/C++语言相像。
- Dart没有public、protected和private的概念。私有特性通过变量或函数加上下划线来表示。
- Dart的工具可以检查出警告信息（warning）和错误信息（errors）。警告信息只是表明代码可能不工作，但是不会妨碍程序运行。错误信息可以是编译时的错误，也可能是运行时的错误。编译时的错误将阻止程序运行，运行时的错误将会以异常（exception）的方式呈现。
- Dart支持anync/await 异步处理。
#### 关键字（56个）如下：  
···
import
interface
abstract
super
implements
extends
class


new
do
as
in
dynamic
enum

switch
case
break
default
if
else
while
for
return
continue

assert
sync*


is
this
async*
await
export
library

try
carch
finally
throw



static
var
final
const
void
null
true
false
typedef

external
mixin
factory
operator
part
rethrow
with
covariant
get
set
yield*
deferred



···  
包名 | 描述  
---|---  
dart：async | 异步编程支持，提供Future和Stream类
dart: collection | 对dart: core 提供更多的集合支持
dart:convert | 不同类型(Json,UTF-8) 间的字符编码,解码支持
dart:core | Dart语言内建的类型,对象以及dart语言核心的功能
dart:html | 网页开发用到的库
dart:io | 文件读写I/O相关操作的库
dart:math | 数字常量及函数,提供随机数算法
dart:svg | 事件和动画的矢量图像支持  
其中三个开发库的使用频率很高:

- dart:core: 核心库,包括strings,numbers,collections,errors,dates,URIs等
- dart:html: 网页开发里的DOM相关的一些库
- dart.io: I/O 命令行使用的I/O库  


#### Mixins with关键字 的用法 

```dart
class A {
  String getMessage() => 'A';
}

class B {
  String getMessage() => 'B';
}

class P {
  String getMessage() => 'P';
}

class AB extends P with A, B {}

class BA extends P with B, A {}

void main() {
  String result = '';

  AB ab = AB();
  result += ab.getMessage();

  BA ba = BA();
  result += ba.getMessage();

  print(result);
}

/*
上面这个代码的输出 结果是: BA
Dart中的Mixins通过创建一个新类来实现，该类将mixin的实现层叠在一个超类之上以创建一个新类 ，它不是“在超类中”，而是在超类的“顶部”，因此如何解决查找问题不会产生歧义。

*/

class AB extends P with A, B {}

class BA extends P with B, A {}
//实际上上面这段代码在语意上等同于下面的代码段,PAB最终with B,所以第一打印是B,第二个打印是A
class PA = P with A;
class PAB = PA with B;

class AB extends PAB {}

class PB = P with B;
class PBA = PB with A;

class BA extends PBA {}

/*

- 线性化
在AB和P之间创建新类，这些新类是超类P与A类和B类之间的混合类。

正如你所看到的那样，我们并没有使用多重继承！

Mixins不是一种在经典意义上获得多重继承的方法。
Mixins是一种抽象和重用一系列操作和状态的方法。
它类似于扩展类所获得的重用，但它与单继承兼容，因为它是线性的。

- 什么时候应该使用mixins?
当我们想要在不共享相同类层次结构的多个类之间共享行为时，或者在超类中实现此类行为没有意义时，Mixins非常有用。

通常情况下是序列化（例如，查看jaguar_serializer）或持久化。
但是你也可以使用mixins来提供一些实用功能（比如Flutter中的RenderSliverHelpers）。

花点时间玩这个功能，我相信你会找到新的用例😉。
不要局限于无状态mixins，你绝对可以存储变量并使用它们！
*/

```

#### 库的引用
引用dart的库
import 'dart:xx'
import 'package:xxx'

引用库的一部分内容
show 关键字: 只引用一点
hide 关键字:除此之外都引用
import 'package:lib1/lib1.dart show foo'; //导入foo
import 'package:lib1/lib1.dart hide foo';// 一处foo导入其他所有内容

#### 异步支持
Dart语言是目前少数几个支持异步操作的语言,一般使用async函数和await 表达式实现异步操作.
Dart库提供asynchronous功能,该功能提供接口进行耗费时间的操作,比如文件读写,网络请求.该功能返回Futuree或者Stream对象.
* 使用async函数和await表达式
* 使用Future功能提供的API. 
代码示例:
```dart
//方法上 加async   然后异步等待的时候加await
fileOperate () async {
	//读取文件
	var file = await readFile();
	//其他处理
}

```
#### 元数据
使用元数据给代码添加更多的信息,元数据是可以@开始的修饰符,在@后面接着编译时的常量或调用一个常量构造函数.目前Dart语言提供三个@修饰符:
@ deprecated 被弃用
@ override 重写
@proxy 代理

使用@override 修饰符可以重写父类方法. 
元数据可以修饰 library(库), class(类),typedef(类型定义),type parameter(类型参数),constructor(构造函数),factory(工厂函数),function(函数),field(作用域),parameter(参数),variaable declaration(变量声明).

#### 注释
// 单行注释
/* 多行注释*/
/**或者 ///开头的是文档注释
### Flutter 常用组件
#### 容器组件 Widget
- Container 组件的属 性及描述
属性名 | 类型 | 说明  
---|---  
key | Key  | Container 唯一标识符，用于查找更新
alignment | AlignmentGeometry  | 控制 child 的对齐方式，如果 Container 或者 Container 父节点尺寸大于 child 的尺寸，这个属性设置会起作用，有很多种对齐方式
padding |  EdgelnsetsGeometry | Decoration 内 部 的 空 白区域，如果有 child 的话,child 位于padding 内部
color | Color | 用来设置 Container 背景色，如果 foregroundDecoration 设置的话，可能会遮盖 color 效果
decoration | Decoration | 绘制在 child 后丽的装饰，设置了 Decoration 的话，就不能设置 color 属性，否则会报错 ，此时应该在 Dec oration 中进行颜色的设置
foregroundDecoration | Decoration |  绘制在 child 前面的装饰
width | double |  Container 的宽度，设置为 double .infinity 可以强制在宽度上撑满，不设置， 则根据 child 和父节点两者一起布局
height | double |   Container 的高度，设置为 doub l e.infin 即可以强制在高度上撑满
constraints | BoxConstraints |   添加到 child 上额外的约束条件
margin | EdgelnsetsGeometry |   围绕在 Decoration 和 child 之外的空白区域，不属于内容区域
transform | Matrix4 |   设置 Container 的变换矩阵，类型为 Matrix4
child | Widget |   Container 中的内容 Widget  

```dart
void main() {
//  runApp(new MyMainApp());

  runApp(new MyApp03());
}

class MyApp03 extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        title: '容器组件示例',
        home: Scaffold(
          appBar: AppBar(
            title: Text('容器组件示例'),
          ),
          body: Center(
            child: Container(
              width: 200.0,
              height: 200.0,
              //使用了装饰就不能再容器内直接使用color属性
              decoration: BoxDecoration(
                  color: Colors.white,
                  //边框
                  border: new Border.all(color: Colors.grey, width: 8.0),
                  //圆角
                  borderRadius:
                      const BorderRadius.all(const Radius.circular(8.0))),
              child: Text(
                'Flutter',
                textAlign: TextAlign.center,
                style: TextStyle(fontSize: 28.0),
              ),
            ),
          ),
        ));
  }
}

```
#### 图片组件
图片组件(Image) 是显示图像的组件,Image组件有多重构造函数
- new Image: 从ImageProvider获取图像
- new Image.asset:加载资源图片
- new Image.file: 加载本地图片文件
- new Image.newwork:加载网络图片
- new Image.memory: 加载Uint8List 资源图片
![image组件属性图](D:\公司内部资料\新建文件夹\AndroidNotes\VideoDevelopment\img\flutter\Image组件属性图.png)
![image组件属性图](D:\公司内部资料\新建文件夹\AndroidNotes\VideoDevelopment\img\flutter\BoxFit取值及描述.png)
```dart

void main() {
  runApp(new MyApp04());
}
class MyApp04 extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Image demo',
      home: new ImageDemo(),
    );
  }
}

class ImageDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return new Center(
      child: new Image.network(
        //图片url
        'https://flutter.dev/assets/homepage/carousel/slide_1-bg-4e2fcef9a7343692a5f7784d68241a786c57c79d55f0fe37e6b82a653d146b93.jpg',
        //填充模式
        fit: BoxFit.fitWidth,
      ),
    );
  }


}

```
#### 文本组件
![文本组件](D:\公司内部资料\新建文件夹\AndroidNotes\VideoDevelopment\img\flutter\文本组件属性及描述.png)
```dart

class TextContainerDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text('文本组件'),
      ),
      body: new Column(
        children: <Widget>[
          new Text(
            '红色+ 黑色 删除线+ 25号',
            style: new TextStyle(
              color: const Color(0xffff0000),
              decoration: TextDecoration.lineThrough,
              decorationColor: const Color(0xff000000),
              fontSize: 25.0,
            ),
          ),
          new Text(
            '橙色 + 下划线+ 24号',
            style: new TextStyle(
              color: const Color(0xffff9900),
              decoration: TextDecoration.underline,
              fontSize: 24.0,
            ),
          ),
          new Text(
            '虚线上划线+ 23号+ 斜体',
            style: new TextStyle(
              decoration: TextDecoration.overline,
              decorationStyle: TextDecorationStyle.dashed,
              fontSize: 23.0,
              fontStyle: FontStyle.italic,
            ),
          ),
          new Text(
            '24号+ 加粗',
            style: new TextStyle(
              fontSize: 23.0,
              fontStyle: FontStyle.italic,
              fontWeight: FontWeight.bold,
              letterSpacing: 6.0,
            ),
          ),
        ],
      ),
    );
  }
}

```
#### 图标及按钮组件
图标组件(Icon) 为展示图标的组件,该组件不可交互,要实现可交互的图标,可以考虑使用IconButton组件.图标组件相关的组件有以下几个:
- IconButton: 可交互的Icon
- Icons: 框架自带的Icon集合
- IconTheme: Icon 主题
- ImageIcon: 通过AssetImages 或者其他图片显示Icon
![文本组件](D:\公司内部资料\新建文件夹\AndroidNotes\VideoDevelopment\img\flutter\Icon组件的属性图.png)
```dart
class IconContainerDemo extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return new Scaffold(
      appBar: new AppBar(
        title: new Text('图标及按钮组件'),
      ),
      body: new Column(
        children: <Widget>[
          //图标组件
          new  Icon(
            Icons.phone,
            color: Colors.green[500],
            size: 28.0,
          ),
          //图标按钮
          new IconButton(icon: Icon(Icons.volume_up,size: 48.0,),
              tooltip: '按下操作',
              onPressed: (){
                print('按下操作');
              }),
          //凸起按钮
          new RaisedButton(onPressed: (){
            //按下事件处理
          },
            child: new Text('RaisedButton 组件'),
          ),
          
        ],
      ),
    );
  }

}
```
#### 列表组件
列表组件在Flutter中, 用ListView来显示列表项,支持垂直和水平方向展示,通过一个属性来控制其方向:
- 水平的列表
- 垂直的列表
- 数据量非常大的列表
- 矩阵式列表
#### 表单组件
表单是一个包含表单元素的区域,表单元素允许用户输入内容,比如:文本域、下拉列表、单选框、复选框等。常见的应用场景有：登录、注册、输入信息等。表单里有两个重要组件，一个是From组件用来做整个表单提交使用的，另一个是TextFormField组件用来做用户输入。
![表单组件](D:\公司内部资料\新建文件夹\AndroidNotes\VideoDevelopment\img\flutter\表单组件属性.png)  
上面我们可以看到 key ：组件在整个Widget树种的key值，而获取表单实例，只需要设置一个全局类型的key，通过这个key的属性，来获取表单对象，需要使用GlobalKey来获取。
- `GlobalKey<FormState> loginKey = new GlobalKey<FormState>()`  
```dart

```
### MeterialDesign风格
![MeterialDesign风格](D:\公司内部资料\新建文件夹\AndroidNotes\VideoDevelopment\img\flutter\MeterialDesign风格组件属性.png) 