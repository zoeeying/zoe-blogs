# Flutter 学习

Flutter 是谷歌的移动 UI 框架，可以快速在 iOS 和 Android 上构建高质量的原生用户界面。Flutter 可以与现有的代码一起工作。Flutter 具有 120fps（每秒传输帧数，可以简单理解为每秒画面数）的超高性能。

Flutter 具有如下特点：

* 跨平台：Linux、Android、iOS、Fuchsia
* 原生用户界面：原生的，体验和性能更好
* 开源免费：完全开源，可以商用

目前市场上的主流框架：

* Cordova：混合式开发框架（Hybrid App）
* React Native：生成原生 APP，但以 View 为基准嵌入
* Flutter：在渲染技术上，选择了自己实现 GDI

## 1 Flutter 开发环境搭建

##### 1. 系统基本要求

* 操作系统：windows 7 以上 64 位操作系统
* 磁盘空间：大于 3G，最好可以达到 5G

##### 2. 安装 Java 环境

* 下载安装 JDK
* 安装完成后在**终端**使用 java 命令检查

##### 3. 安装 Flutter SDK

* 下载：下载完成后，直接解压缩在 C 盘（随便哪个盘）下
* 配置环境变量：在**系统变量**的 Path 后面追加 C:\flutter\bin，在终端使用 flutter 命令检查
* `flutter doctor` 命令的使用

##### 4. 安装 Android Studio

只是为了用它的虚拟设备，正式开发不用这个 IDE

##### 5. 使用 VSCode 创建项目

* 安装 Flutter 和 Dart 插件：在 extentions 中找到 Flutter 然后安装，会自动安装 Dart 插件
* ctrl + ~ ：切换打开终端
* 在某个文件夹下，打开终端，使用 `flutter create demo01` 命令，创建 Flutter 项目
* 使用命令 `cd demo01`，进入项目
* 使用命令 `flutter run`，启动项目

## 2 Hello World

##### 1. 升级 Flutter SDK

* 在终端使用命令 `flutter upgrade`
* 删除 SDK 包，重新下载

##### 2. VSCode 常用快捷键（控制虚拟机）

* R：点击后热加载，直接查看预览效果
* P：在虚拟机中显示网格
* O：切换 Android 和 iOS 的预览模式
* Q：退出调试预览模式

```dart
import 'package:flutter/material.dart';

// 入口组件，void表示没有返回值
void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of your application
  @override // 重写build方法，返回一个widget
  Widget build(BuildContext context) { // BuildContext context是上下文参数
    return MaterialApp(
      title: 'Welcome to Flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Hello World'),
        ),
        body: Center(
          child: Text('Hello World'),
        )
      ),
    );
  }
}
```

## 3 Flutter Widget

Flutter 中一切皆 Widget，通过 build 方法返回 Widget，这也是和 React Native 中通过 render 函数返回需要渲染的 component 一样的模式。Widget 是一切的基础，作为响应式的渲染，属于 MVVM 的实现机制。可以通过修改数据，再用 setState 设置数据，Flutter 会自动通过绑定的数据更新 Widget。

Widget 分为**有状态**和**无状态**两种，在 Flutter 中每个页面都是一帧。无状态就是保持在那一帧。而有状态的 Widget 当数据更新时，其实是绘制了新的 Widget，只是 State 实现了跨帧的数据同步保存。

**技巧：**当代码框里输入 stl 的时候，可以自动弹出创建无状态控件的模板选项，而输入 stf 的时，就会弹出创建有状态 Widget 的模板选项。

### 3.1 无状态 StatelessWidget

继承 StatelessWidget，通过 build 方法返回一个布局好的控件。Widget 和 Widget 之间通过 child 进行嵌套。其中有的 Widget 只能有一个 child，有的 Widget 可以有多个 child ，也就是children。

```dart
import 'package:flutter/material.dart';

class DEMOWidget extends StatelessWidget {
  // 实例变量
  final String text;

  // 数据可以通过构造方法传递进来
  DEMOWidget(this.text);

  @override
  Widget build(BuildContext context) {
    // 这里返回你需要的控件
    // 这里末尾有没有的逗号，对于格式化代码而已是不一样的
    return Container(
      // 白色背景
      color: Colors.white,
      // Dart语法中，?? 表示如果text为空，就返回尾号后的内容
      child: Text(text ?? "这就是无状态DMEO"),
    );
  }
}
```

### 3.2 有状态 StatefulWidget

需要创建管理的主要是 State ，通过 State 的 build 方法去构建控件。在 State 中，可以动态改变数据，这类似 MVVM 实现，在 setState 之后，改变的数据会触发 Widget 重新构建刷新。

State 中主要的生命周期有：

* **initState**：初始化，理论上只有初始化一次，但是也有特殊情况。
* **didChangeDependencies**：在 initState 之后调用，此时可以获取其他 State。
* **dispose**：销毁，只会调用一次。

```dart
import 'dart:async';
import 'package:flutter/material.dart';

class DemoStateWidget extends StatefulWidget {
  
  // 实例常量
  final String text;

  // 通过构造方法传值
  DemoStateWidget(this.text);

  // 主要是负责创建state
  @override
  _DemoStateWidgetState createState() => _DemoStateWidgetState(text);
}

class _DemoStateWidgetState extends State<DemoStateWidget> {
  String text;
  _DemoStateWidgetState(this.text);
  
  @override
  void initState() {
    // 初始化，这个函数在生命周期中只调用一次
    super.initState();
    // 定时2秒
    new Future.delayed(const Duration(seconds: 1), () {
      setState(() {
        text = "这就变了数值";
      });
    });
  }

  @override
  void dispose() {
    // 销毁，只会调用一次
    super.dispose();
  }

  @override
  void didChangeDependencies() {
    // 在initState之后调用，Called when a dependency of this [State] object changes
    // 在initState之后调用，此时可以获取其他State
    super.didChangeDependencies();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Text(text ?? "这就是有状态DMEO"),
    );
  }
}
```

## 4 Flutter 布局

> * Widget 是用于构建 UI 的类
> * Widget 用于布局和构建 UI 元素
> * 通过简单的 Widget 来构建复杂的 Widget

Flutter 布局机制的核心就是 Widget，在 Flutter 中，几乎所有东西都是 Widget，甚至布局模型都是 Widget。在 Flutter 应用中看到的图像、图标和文本都是 Widget，甚至看不到的东西也是 Widget，例如行（row）、列（column）以及用来排列、约束和对齐这些可见 Widget 的网格（grid）。

Flutter 中拥有需要将近 30 种内置的布局 Widget，其中常用的有 Container、Padding、Center、Flex、Stack、Row、Column、ListView 等。

* Container：只有一个子 Widget。包含了 padding、margin、color、宽高、decoration（一般配置边框和阴影） 等配置。
* Padding：只有一个子 Widget。只用于设置 Padding，常用于嵌套 child，给 child 设置 padding。
* Center：只有一个子 Widget。只用于居中显示，常用于嵌套 child，给 child 设置居中。
* Stack：可以有多个子 Widget。子 Widget 堆叠在一起。
* Column：可以有多个子 Widget。垂直布局。Column 和 Row 是必备布局，
* Row：可以有多个子 Widget。水平布局。

```dart
// 主轴方向，Column的竖向，Row的横向
mainAxisAlignment: MainAxisAlignment.start,

// 默认是最大充满，还可以根据child显示最小尺寸
mainAxisSize: MainAxisSize.max,

// 副轴方向，Column的横向，Row的竖向
crossAxisAlignment: CrossAxisAlignment.center,
```

* Expanded：只有一个子 Widget。在 Column 和 Row 中代表着平均充满的作用，当有两个存在的时候默认均分充满。同时可以设置 flex 属性决定比例。
* ListView：可以有多个子 Widget。

**补充：**Column、Row 绝对是必备布局，横竖布局也是日常中最常见的场景。它们常用的有这些属性配置：主轴方向是 start 或 center 等，副轴方向是 start 或 center 等，mainAxisSize 是充满最大尺寸或者只根据子 Widget 显示最小尺寸。

## 5 Flutter 页面

Flutter 中除了布局的 Widget，还有**交互显示**的 Widget 和**完整页面呈现**的 Widget，其中常见的有 MaterialApp、Scaffold、Appbar、Text、Image、FlatButton 等。

* MaterialApp：一般作为 APP 顶层主页入口，可配置主题、多语言、路由等。

* Scaffold：一般用户页面的承载 Widge，包含 Appbar、Snackbar、Drawer 等 material design 的设定。

* Appbar：一般用于 Scaffold 的 Appbar，内有标题、二级页面返回按键等，当然不止这些，Tabbar 等也会需要它 。

* Text：显示文本，几乎都会用到，主要是通过 style 设置 TextStyle 来设置字体样式等。

* RichText：富文本，通过设置 TextSpan，可以拼接出富文本场景。

* TextField：文本输入框

  ```dart
  new TextField(
    controller: "文本控制器",
    obscureText: "hint文本",
  );
  ```

* Image：图片加载

  ```dart
  new FadeInImage.assetNetwork(
    placeholder: "预览图",
    fit: BoxFit.fitWidth,
    image: "url",
  );
  ```

* FlatButton：按键点击

  ```dart
  new FlatButton(
    onPressed: () {},
    child: new Container(),
  );
  ```

## 6 Container

最常用的默认布局。只有一个子 Widget。默认充满。是由多个内置控件组成的模板。支持配置 padding、margin、边框、color、背景色、宽高、decoration（一般配置边框和阴影）等配置。在 Flutter 中，不是所有的控件都有宽高、padding、margin、color 等属性，所以才会有 Padding、Center 等 Widget 的存在。如果要添加填充、边距、边框或背景色，可以使用 Container 来设置（只有容器有这些属性）。

```dart
new Container(
  // 四周10大小的marging
  margin: EdgeInsets.all(10.0),
  height: 120.0,
  width: 500.0,
  child:new Text("666666"),
  // 透明黑色遮罩
  decoration: new BoxDecoration(
    // 弧度为4.0
    borderRadius: BorderRadius.all(Radius.circular(4.0)),
    // 设置了decoration的color，就不能设置Container的color
    color: Colors.black,
    // 边框
    border: new Border.all(color: Color(GSYColors.subTextColor), width: 0.3)
  ),
);

new Container(
  child: Text(
    'Hello Zoe',
    style: TextStyle(fontSize: 40.0),
  ),
  alignment: Alignment.topLeft, // bottomCenter、bottomLeft、bottomRight、centerLeft、topCenter
  width: 500.0,
  height: 400.0,
  // color: Colors.lightBlue,
  // padding: const EdgeInsets.all(20.0), // 四周内边距都是20.0
  padding: const EdgeInsets.fromLTRB(10.0, 30.0, 0.0, 0.0),
  margin: const EdgeInsets.all(20.0), // 四周外边距都是20.0
  decoration: new BoxDecoration( // 渐变色
    gradient: const LinearGradient(
      colors: [
        Colors.lightBlue,
        Colors.greenAccent,
        Colors.yellow,
      ],
    ),
  ),
);

```

## 7 Text

```dart
new Text(
  '在跨平台开领域被 JS 一统天下的今天，Dart 语言的出现无疑是一股清流。',
  textAlign: TextAlign.left, // 文本对齐方式，left、right、center、start、end、justify
  maxLines: 2,
  overflow: TextOverflow.fade, // 文字超出行后的显示方式，clip、ellipsis（三个省略号）、fade
);

new Text(
  '在跨平台开领域被 JS 一统天下的今天，Dart 语言的出现无疑是一股清流。',
  textAlign: TextAlign.left, // 文本对齐方式，left、right、center、start、end、justify
  style: TextStyle(
    fontSize: 25.0,
    color: Color.fromARGB(255, 255, 150, 150),
    decoration: TextDecoration.underline, // 下划线
    decorationStyle: TextDecorationStyle.solid, // 实线
  ),
);
```

## 8 Image

##### 1. 几种加入方式

* Image.asset：加载资源图片，会使打包时包体过大
* Image.network：网络资源图片，经常换的或者动态的图片
* Image.file：本地图片，比如相机拍照后的图片预览
* Image.memory：加载到内存中的图片，Uint8List

##### 2. Fit 属性

* BoxFit.fill：图片充满整个容器
* BoxFit.contain：图片在容器中以原比例最大（不超过容器）显示，可能有空隙
* BoxFit.cover：图片在容器中以原比例最大（超过容器）显示，可能会被裁切
* BoxFit.fitWidth、BoxFit.fitHeight：宽度（高度）充满容器，高度（宽度）可能有空隙或者被裁切

```dart
new Container(
  child: new Image.network(
    'https://img.mukewang.com/5b6947970001d34d06000338-240-135.jpg',
    scale: 1.5, // 值越大，图越小
    fit: BoxFit.cover, // contain、cover、fill、fitWidth、fitHeight、scaleDown
  ),
  width: 400.0,
  height: 300.0,
  color: Colors.lightBlue, // 容器背景颜色
);
```

##### 3. 图片的混合模式

```dart
new Container(
  child: new Image.network(
    'https://img.mukewang.com/5b6947970001d34d06000338-240-135.jpg',
    scale: 1.5, // 值越大，图越小
    color: Colors.greenAccent, // 图片背景颜色，和colorBlendMode一起使用，给图片加上混合模式
    colorBlendMode: BlendMode.difference, // 图片混合模式
  ),
  width: 400.0,
  height: 300.0,
  color: Colors.lightBlue, // 容器背景颜色
);
```

##### 4. 图片的 repeat 属性

```dart
new Container(
  child: new Image.network(
    'https://img.mukewang.com/5b6947970001d34d06000338-240-135.jpg',
    scale: 1.5, // 值越大，图越小
    repeat: ImageRepeat.repeatY, // 纵向重复充满容器
  ),
  width: 400.0,
  height: 300.0,
  color: Colors.lightBlue, // 容器背景颜色
);
```

## 9 ListView

```dart
new ListView(
  scrollDirection: Axis.horizontal, // 水平方向滚动
  children: <Widget>[
    ListTile( // 列表瓦片，可以放在任何容器中，不止是ListView
      leading: Icon(Icons.arrow_back),
      title: Text('arrow_back'),
    ),
    ListTile(
      leading: Icon(Icons.android),
      title: Text('android'),
    ),
    ListTile(
      leading: Icon(Icons.iso),
      title: Text('iso'),
    ),
    // 也可以放图片
   // new Image.network(
     // 'https://img1.sycdn.imooc.com/5c18cf540001ac8206000338-240-135.jpg',
     // fit: BoxFit.cover,
   // ),
  ],
);
```

**scrollDirection 属性：**

* Axis.horizontal：横向滚动或者水平方向滚动
* Axis.vertical：纵向滚动或者垂直方向滚动

## 10 GridView

* padding：设置内边距
* crossAxisSpacing：网格间的空隙（副轴方向）

* mainAxisSpacing：网格间的空隙（主轴方向）

* crossAxisCount：网格的列数
* childAspectRatio：宽高比

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: '电影海报实例',
      home: Scaffold(
        appBar: AppBar(
          title: Text('电影海报实例'),
        ),
        // body: GridView.count(
        //   padding: const EdgeInsets.all(10.0), // padding不能直接设置数值
        //   crossAxisSpacing: 10.0,
        //   crossAxisCount: 3, // 只有double类型的才需要在后面加上.0
        //   children: <Widget>[
        //     const Text('I love Nanjing'),
        //     const Text('I love Nanjing'),
        //     const Text('I love Nanjing'),
        //     const Text('I love Nanjing'),
        //     const Text('I love Nanjing'),
        //     const Text('I love Nanjing'),
        //   ],
        // ),
        body: GridView(
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: 3,
            crossAxisSpacing: 2.0,
            mainAxisSpacing: 2.0,
            childAspectRatio: 0.7,
          ),
          children: <Widget>[
            new Image.network(
              'http://img5.mtime.cn/mg/2019/03/21/105842.67810645_170X256X4.jpg',
              fit: BoxFit.cover,
            ),
            new Image.network(
              'http://img5.mtime.cn/mg/2019/03/21/105842.67810645_170X256X4.jpg',
              fit: BoxFit.cover,
            ),
            new Image.network(
              'http://img5.mtime.cn/mg/2019/03/21/105842.67810645_170X256X4.jpg',
              fit: BoxFit.cover,
            ),
            new Image.network(
              'http://img5.mtime.cn/mg/2019/03/21/105842.67810645_170X256X4.jpg',
              fit: BoxFit.cover,
            ),
            new Image.network(
              'http://img5.mtime.cn/mg/2019/03/21/105842.67810645_170X256X4.jpg',
              fit: BoxFit.cover,
            ),
            new Image.network(
              'http://img5.mtime.cn/mg/2019/03/21/105842.67810645_170X256X4.jpg',
              fit: BoxFit.cover,
            ),
          ],
        ),
      ),
    );
  }
}

```

## 11 Expanded













## 12 自定义组件

在项目中，我们可以采用**自定义组件**的方法来把项目拆成很多的组件，方便分工合作，减少代码的层层嵌套。

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      home: Scaffold(
        appBar: AppBar(
          title: Text('ListView'),
        ),
        body: Center(
          child: Container(
            height: 200.0,
            child: MyList(), // 自定义组件
          ),
        ),
      ),
    );
  }
}

// 自定义组件
class MyList extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ListView(
      scrollDirection: Axis.horizontal,
      children: <Widget>[
        new Container(
          width: 180,
          color: Colors.limeAccent,
        ),
        new Container(
          width: 180,
          color: Colors.amber,
        ),
        new Container(
          width: 180,
          color: Colors.lightBlue,
        ),
        new Container(
          width: 180,
          color: Colors.redAccent,
        ),
      ],
    );
  }
}
```

## 13 动态列表

Dart 中的 List 类型可以简单理解为 JS 中的数组，它的声明方式如下：

```dart
new List()
new List(3) // 固定List元素个数
new List<String>() // 固定List元素类型
[1, 2, 3]
```

```dart

import 'package:flutter/material.dart';

void main() => runApp(
  // 用生成器生成1000个元素
  MyApp(items: new List<String>.generate(1000, (i) => 'item $i'))
);

class MyApp extends StatelessWidget {
  final List<String> items;
 
  // 重写默认的构造方法，Key是默认参数
  // @required表明第二参数是必须要传的
  // 因为继承的类是无名无参的，所以需要调用父类的super方法
  MyApp({Key key, @required this.items}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      home: Scaffold(
        appBar: AppBar(
          title: Text('ListView'),
        ),
        body: new ListView.builder(
          itemCount: items.length,
          itemBuilder: (context, index) {
            return new ListTile(title: new Text('${items[index]}'));
          },
        ),
      ),
    );
  }
}
```




