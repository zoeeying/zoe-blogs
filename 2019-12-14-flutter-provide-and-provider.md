# Flutter 状态管理 Provide 和 Provider

在 Flutter 开发中，状态管理是一个永恒的话题。一般的原则是：如果状态是组件私有的，则应该由组件自己管理；如果状态要跨组件共享，则该状态应该由各个组件共同的父元素来管理。

如果我们的应用比较简单，Flutter 作为一个声明式框架，只需要将**数据**映射成**视图**就可以了。Flutter 实际上在一开始就为我们提供了一种状态管理方式，那就是 **StatefulWidget**。在 State 属于某一个特定的 Widget，在多个 Widget 之间进行交流的时候，虽然可以使用 callback 解决，但是当嵌套足够深的话，就会增加非常多可怕的垃圾代码。

## 1 Flutter 状态管理方案

**Scoped Model：** 最早的状态管理方案。

**Redux：** 现在国内用得最多，因为咸鱼团队一直在用，还出了自己 fish redux。

**Bloc：** 对于一个页面里的状态管理比较简单。

**StatefulWidget：** 缺点是耦合太强，如果是大型应用，管理起来非常混乱。

**Provide：** 是 Google 官方推出的状态管理模式，已经被弃用。

**Provider：** 取代 Provide 的状态管理方案，基于 InheritedWidget 实现的。

## 2 Provide

#### (1) 步骤一：新建数据仓库

在 lib 下新建一个文件夹 provide，在 provide 中新建文件 counter.dart，相当于一个数据仓库。

```dart
import 'package:flutter/material.dart';

// 建立一个类Counter，混入ChangeNotifier
// 意思就是不用管理听众
// 这个类似于创建一个State，但是为了跟State区分，我们叫创建Provide
class Counter with ChangeNotifier {
  int value = 0;

  increment() {
    value++;
    // 通知用到Counter对象的Widget，value改变了，可以局部刷新Widget
    notifyListeners();
  }
}
```

#### (2) 步骤二：main.dart 文件

在 App 的顶层 main.dart 文件中。

```dart
import 'package:flutter/material.dart';
import 'package:provide/provide.dart';

import './pages/index_page.dart';
import './provide/counter.dart';

// 顶层依赖
void main() {
  var counter = Counter();
  var childCategory = ChildCategory();
  var providers = Providers();
  // 注册依赖，两个点表示再返回providers，<Counter>是泛型
  // 多个状态可以在后面再..provide
  providers
  ..provide(Provider<Counter>.value(counter))
  ..provide(Provider<ChildCategory>.value(childCategory));
  runApp(ProviderNode(child: MyApp(), providers: providers));
}
```

#### (3) 步骤三：获取和修改状态

在 cart_page.dart 文件可以获取状态数据，也可以修改状态数据。修改状态数据是调用的 counter.dart 中的 increment 方法。

```dart
import 'package:flutter/material.dart';
import 'package:provide/provide.dart';
import '../provide/.dart';

class CartPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          children: <Widget>[
            Number(),
            MyButton(),
          ],
        ),
      ),
    );
  }
}

class Number extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      margin: EdgeInsets.only(top: 200),
      // 获取数据
      // 监听变化，数据变化了，这边UI也会跟着变化
      // 需要用Provide组件包裹一下，里面是泛型Counter
      child: Provide<Counter>(
        builder: (context, child, counter) {
          return Text(
            '${counter.value}',
            // 主题自带的样式
            style: Theme.of(context).textTheme.display1,
          );
        },
      ),
    );
  }
}

class MyButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      child: RaisedButton(
        onPressed: () {
          // 修改状态数据
          Provide.value<Counter>(context).increment();
        },
        child: Text('递增'),
      ),
    );
  }
}
```

## 3 Provider

Provider 是 Flutter 官方新推荐的状态管理方式之一，特点是不复杂、好理解，在代码量不大的情况下，可以方便组合和控制刷新颗粒度。

Provider 从名字上就很容易理解，它就是用于提供数据，无论是在**单个页面**还是在**整个** app 都有它自己的解决方案，我们可以很方便地管理状态。可以说，Provider 的目标就是**完全取代** StatefulWidget。

使用 Provider 的注意事项：

a. 保证 build 方法无副作用

b. 不要所有状态都放在全局

c. 尽量在 Model 中使用私有变量 `_`

d. 控制刷新范围

#### (1) 步骤一：创建数据 Model

这里的 Model 实际上就是我们的状态，它不仅储存了我们的**数据模型**，而且还包含了更改数据的方法，并暴露出它想要暴露出的数据。在 lib 下新建文件夹 model，在 model 文件夹下新建文件 CounterModel.dart 文件。

```dart
import 'package:flutter/material.dart';

// 使用mixin混入了ChangeNotifier，这个类可以帮助我们自动管理所有听众
class CounterModel with ChangeNotifier {
  // _表示私有
  int _count = 0;
  // getter，把_count值暴露出去
  int get value => _count;

  // 用于更改数据
  void increment() {
    _count++;
    // notifyListeners()通知所有听众进行刷新
    notifyListeners();
  }
}
```

#### (2) 步骤二：创建顶层共享数据

在 main 方法中初始化全局数据。`Provider.value` 还提供了 `UpdateShouldNotify` 函数，用于控制刷新时机。

```dart
import 'package:flutter/material.dart';
import './model/counter_model.dart';
import 'package:provider/provider.dart';
import './first_screen.dart';

void main() {
  final counter = CounterModel();
  final textSize = 48;

  runApp(
    // 通过Provider<T>.value能够管理一个恒定的数据，并提供给子孙节点使用
    // 我们只需要将数据在其value属性中声明即可
    // 这里将textSize传入
    // T泛型可以省略
    Provider<int>.value(
      value: textSize,
      // ChangeNotifierProvider<T>.value不仅能够提供数据供子孙节点使用
      // 还可以在数据改变的时候通知所有听众刷新（通过model中的notifyListeners）
      child: ChangeNotifierProvider.value(
        value: counter,
        child: MyApp(),
      ),
    ),
  );
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData.dark(),
      home: FirstScreen(),
    );
  }
}
```

```dart
import 'package:flutter/material.dart';
import './provider/counter_model.dart';
import 'package:provider/provider.dart';
import './first_screen.dart';

void main() {
  final counter = CounterModel();
  final textSize = 48;

  runApp(
    // 也可以通过MultiProvider来组合多个Provider
    MultiProvider(
      providers: [
        Provider.value(value: textSize),
        ChangeNotifierProvider.value(value: counter)
      ],
      child: MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData.dark(),
      home: FirstScreen(),
    );
  }
}

```

#### (3) 步骤三：在子页面中获取状态和修改状态

获取顶层数据最简单的方法就是 `Provider.of(context)`，这里的范型 T 指定了获取 FirstScreen 向上寻找最近的储存了 T 的祖先节点的数据。通过这个方法，我们获取了顶层的 CounterModel 及 textSize，并在 Text 组件中进行使用。

同时我们也可以使用 `Provider.of(context)` 修改状态，`Provider.of<CounterModel>(context).add()`。

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import './model/counter_model.dart';

class FirstScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final _counter = Provider.of<CounterModel>(context);
    final textSize = Provider.of<int>(context).toDouble();

    return Scaffold(
      appBar: AppBar(
        title: Text('FirstPage'),
      ),
      body: Center(
        child: Text(
          'Value: ${_counter.value}',
          style: TextStyle(fontSize: textSize),
        ),
      ),
      // 用来点击跳转到SecondScreen页面
      floatingActionButton: FloatingActionButton(
        onPressed: () => Navigator.of(context)
        .push(MaterialPageRoute(builder: (context) => SecondScreen())),
        child: Icon(Icons.navigate_next),
      ),
    );
  }
}
```

#### (4) 步骤四：Consumer

除了上面的 `Provider.of(context)` 获取状态的方法以外，我们还可以使用 Consumer 来获取祖先节点中的数据。Consumer 内部其实也是通过 `Provider.of(context)` 实现的。在复杂项目中，Consumer 可以极大地缩小控件刷新范围。`Provider.of(context)` 会导致调用的 context 页面范围的刷新。而使用 Consumer 的话，可以只刷新 Consumer 的部分。

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import './model/counter_model.dart';

class SecondScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Second Page'),
      ),
      // 两个泛型
      // _是Widget child
      body: Consumer2<CounterModel,int>(
        builder: (context, CounterModel counter, int textSize, _) => Center(
          child: Text(
            'Value: ${counter.value}',
            style: TextStyle(
              fontSize: textSize.toDouble(),
            ),
          ),
        ),
      ),
      // 这边只使用了一个Consumer
      // Consumer使用了Builder模式，收到更新通知就会通过builder重新构建
      // Consumer<T> 代表了它要获取哪一个祖先中的Model，T是获取到的最近一个祖先节点中的数据模型
      // Consumer的builder实际上就是一个Function，它接收三个参数
      // Consumer会返回一个通过这三个参数映射的Widget用于构建自身
      floatingActionButton: Consumer<CounterModel>(
        builder: (context, CounterModel counter, child) => FloatingActionButton(
          onPressed: counter.increment,
          // child用来构建那些与Model无关的部分，在多次运行builder中，child不会进行重建
          child: child,
        ),
        child: Icon(Icons.add),
      ),
    );
  }
}
```
