---
title: Dart 基础
date: 2019-12-29
---

# Dart 基础

## 1 入口方法

Dart 中所有要被执行的代码都需要放在入口方法中。

```dart
// void表示main方法没有返回值，可以省略
void main() {
  print('你好dart');
}
```

## 2 变量

Dart 是一个强大的脚本类语言，可以不预先定义变量类型，会自动推断类型。

Dart 中定义变量可以通过 var 关键字和类型关键字，var 和类型关键字不能同时写，会报错。

Dart 中有类型校验。

**Dart 中的标识符（变量名称）命名规则如下：**

a. 变量名称必须由数字、字母、下划线或 $ 组成；

b. 标识符开头不能是数字；标识符不能是保留字和关键字；

c. 变量的名称是区分大小写的，比如 age 和 Age 就是不同的变量，建议在实际的运用中不要用一个单词大小写区分两个变量；

d. 标识符一定要见名思意，**变量名称建议用名词，方法名称建议用动词**。

## 3 常量

在 Dart 中使用关键字 final 和 const 定义常量。

const 值不变，一开始就得赋值。

final 可以开始不赋值，可以在后面赋值，但是只能赋值一次。final 不仅有 const 的编译时常量的特性，最重要的它是运行时常量，并且 final 是惰性初始化，即在运行时第一次使用前才初始化。

永远不改变值的量，请使用 final 或 const 声明，而不要使用 var 或类型关键字来声明。

```dart
final a = new DateTime.now();
// const a = new DateTime.now(); // 报错，调用一个方法赋值给一个常量，只能用final关键字
```

## 4 数据类型

Dart 中支持以下数据类型：

#### (1) Number（数值）

int、double，可以把整型的值赋值给浮点型的变量。

#### (2) String（字符串）

String，三个单引号中可以放多行字符串。

#### (3) Boolean（布尔）

bool，值只有 true 和 false。Dart 中的条件判断语句中的条件必须是布尔类型。

#### (4) List（数组）

在 Dart 中，数组是列表对象，所以也被称为列表。

  ```dart
// List类型的定义方式
var l1 = ['aaa', 'bbbb', 'cccc'];

var l2 = new List();
l2.add('张三'); // 往数组中添加元素
l2.add('李四');
l2.add('王五');

// 定义List指定类型
var l3 = new List<String>();
  ```

#### (5) Map（字典）

通常来说，Map 是一个键值对相关的对象，键和值可以是任何类型的对象，一个键只能出现一次，而一个值可以出现多次。

  ```dart
// Map类型的定义方式
var person = {
  "name": "张三",
  "age": 20,
  "work": ["程序员", "送外卖"]
};
print(person["name"]); // 访问Map中属性的方式，不能通过点来访问

var p = new Map();
p["name"] = "李四"; // 为Map中增加属性并且赋值
  ```

#### (6) Rune

Rune 是 UTF-32 编码的字符串。

#### (7) Symbol

Symbol 对象表示在 Dart 程序中声明的运算符或标识符。我们可能永远不需要使用 Symbol，但它对于按名称引用标识符的 API 非常有用。

Dart 中可以通过 is 关键字来判断数据类型。

```dart
var str = 123;
if (str is String) {
  print('string');
} else if (str is int) {
  print('int');
} else {
  print('其它类型');
}
```

## 5 运算符

#### (1) 算数运算符

```bash
+   -   *   /   ~/(取整)   %(取余)
```

#### (2) 关系运算符

关系运算符主要用于条件判断。

```bash
==   !=   >   <   >=   <=
```

#### (3) 逻辑运算符

```bash
!   &&   ||
```

#### (4) 基础赋值运算符

`b??=23` 表示如果 b 为空的话，把 23 赋值给 b。

```bash
=   ??=
```

#### (4) 复合赋值运算符

如果 `++`、`--` 写在前面，表示先运算后赋值，如果 `++`、`--` 写在后面，表示先赋值后运行运算。

```bash
+=   -=   *=   /=   %=   ~/=
```

```dart
var a = 10;
var b = a++;
print(a); // 11
print(b); // 10

var a = 10;
var b = ++a;
print(a); // 11
print(b); // 11
```

#### (5) 条件表达式

```dart
// if else、switch case
...

// 三目运算符
...

// ??运算符
var a;
var b = a ?? 10;
print(b); // 10
```

## 6 类型转换

#### (1) Number 类型转换成 String 类型

  ```dart
var myNum = 12;
var str = myNum.toString();
print(str is String);
  ```

#### (2) String 类型转换成 Number 类型

建议用 double 来转换。

  ```dart
String str = '123';
var myNum = int.parse(str);
print(myNum is int);

// 建议用double转，因为用int转123.1的话会报错
String str = '123.1';
var myNum = double.parse(str);
print(myNum is double);

// 空字符串直接转换的话，会报错
String price = '';
try {
  var myNum = double.parse(price);
  print(myNum);
} catch (err) {
  print(err);
}
  ```

#### (3) 其它类型转换成 Boolean 类型

```dart
// 使用isEmpty判断字符串是否为空
var str = '';
if (str.isEmpty) {
  print('str空');
} else {
  print('str不为空');
}

// 判断变量是否为null
var myNum;
if (myNum == null) {
  print('空');
} else {
  print('非空');
}

var myNum = 0 / 0;
print(myNum); // 不会报错，会打印出NaN
if (myNum.isNaN) { // 判断是否为NaN
  print('NaN');
}
```

## 7 循环

#### (1) for 循环

```javascript
for (int i = 1; i <= 10; i++) {
  print(i);
}
```

#### (2) while 循环

```javascript
while ('表达式/循环条件') {
  '语句/循环体'
}

do {
  '语句/循环体'
} while ('表达式/循环条件');
```

**注意：** do while 循环最后的分号不能忘记；循环条件中使用的变量需要初始化；循环体中应有结束循环的条件，否则会造成死循环。

do while 循环和 while 循环的区别在于第一次循环条件不成立的情况下，do while 循环会先执行一次。

#### (3) break

break 可以用在 switch case 中，也可以用在 for 循环和 while 循环中。

在 switch case 中使用 break 可以使流程跳出 switch 结构。在循环语句中使用 break 可以使流程**跳出**循环，后面代码也不会执行，结束这个循环。在多层循环中，一个 break 只能向外跳出一层。

#### (4) continue

continue 表示**跳过**当前循环。

continue 可以用在 for 循环以及 while 循环中，但是不建议用在 while 循环中，不小心容易死循环。

只能在循环语句中使用，使本次循环结束，跳过 continue 下面尚未执行的语句，接着进行循环条件判断，继续往往下循环。

## 8 List

#### (1) 常用属性

| 属性       | 描述       |
| ---------- | ---------- |
| length     | 长度       |
| reversed   | 翻转       |
| isEmpty    | 是否为空   |
| isNotEmpty | 是否不为空 |

```dart
List myList = ['香蕉', '苹果', '西瓜'];
print(myList.length); // 3
print(myList.isEmpty); // false
print(myList.isNotEmpty); // true
print(myList.reversed); // (西瓜, 苹果, 香蕉)
var newMyList = myList.reversed.toList();
print(newMyList); // [西瓜, 苹果, 香蕉]
```

#### (2) 常用方法

| 方法                     | 描述                                            |
| ------------------------ | ----------------------------------------------- |
| `add()`                  | 往 List 中增加数据，只能增加一个                |
| `addAll()`               | 拼接数组                                        |
| `indexOf()`              | 查找，传入具体值，找不到返回 -1，找到返回索引值 |
| `remove()`               | 删除，传入具体值                                |
| `removeAt()`             | 删除，传入索引值                                |
| `fillRange()`            | 修改                                            |
| `insert(index, value)`   | 指定位置插入                                    |
| `insertAll(index, list)` | 指定位置插入 List                               |
| `toList()`               | 将其他类型转换成 List                           |
| `join()`                 | List 转换成字符串                               |
| `split()`                | 将字符串转换成 List                             |

其它方法：forEach、map、where、any、every

```dart
List myList = ['香蕉', '苹果', '西瓜'];
myList.add('桃子'); // 增加一个数据
// myList.add('桃子', '葡萄'); // 报错
myList.addAll(['桃子', '葡萄']); // 增加多个数据

print(myList.indexOf('苹果')); // 1

myList.remove('西瓜'); // 移除西瓜
myList.removeAt(1); // 移除索引为1的数据
```

```dart
List myList = ['香蕉', '苹果', '西瓜'];
myList.fillRange(1, 2, 'aaa'); // 把苹果修改成aaa
```

```dart
List myList = ['香蕉', '苹果', '西瓜'];
myList.insert(1, 'aaa'); // 在香蕉后苹果前插入aaa
myList.insertAll(1, ['aaa', 'bbb']); // 在香蕉后苹果前插入list
```

  ```dart
  List myList = ['香蕉', '苹果', '西瓜'];
  var str = myList.join(',');
  print(str); // 香蕉,苹果,西瓜
  
  var str = '香蕉-苹果-西瓜';
  var list = str.split('-');
  print(list); // [香蕉, 苹果, 西瓜]
  ```

## 9 Set

Set 是没有顺序且不能重复的集合，不能通过索引去获取数据，它的功能主要是去除数组中重复的数据。

```dart
var s = new Set();
s.add('香蕉');
s.add('苹果');
s.add('苹果');
print(s); // {香蕉, 苹果}，也是集合类型
print(s.toList()); // [香蕉, 苹果]

// 数组去重
List myList = ['香蕉', '苹果', '西瓜', '香蕉', '苹果', '香蕉', '苹果'];
var s = new Set();
s.addAll(myList);
print(s); // {香蕉, 苹果, 西瓜}
print(s.toList()); // [香蕉, 苹果, 西瓜]
```

## 10 Map

Map（映射）是无序键值对。

#### (1) 常用属性

| 属性       | 描述                |
| ---------- | ------------------- |
| keys       | 获取所有的 key 值   |
| values     | 获取所有的 value 值 |
| isEmpty    | 判断是否为空        |
| isNotEmpty | 判断是否不为空      |

```dart
Map person = {"name": "张三", "age": 20, "sex": "男"};
print(person.keys); // (name, age, sex)
print(person.keys.toList()); // [name, age, sex]
print(person.values.toList()); // [张三, 20, 男]
print(person.isEmpty); // false
print(person.isNotEmpty); // true
```

#### (2) 常用方法

| 方法            | 描述                                               |
| --------------- | -------------------------------------------------- |
| `remove(key)`   | 删除指定 key 的数据                                |
| `addAll({...})` | 合并映射，给映射内增加属性                         |
| `containsValue` | 查看映射内的是否包含某个 value，返回 true 或 false |

其它方法：forEach、map、where、any、every

```dart
Map person = {"name": "张三", "age": 20, "sex": "男"};
person.addAll({
  "work": ['敲代码', '送外卖'],
  "height": 160
});
person.remove("sex");
print(person.containsValue('张三')); // true
```


## 11 List、Map、Set 中通用的遍历方法

#### (1) for...in...

```dart
List myList = ['香蕉', '苹果', '西瓜'];
for (var item in myList) {
  print(item);
}
```

#### (2) forEach

```dart
myList.forEach((value) { // 数组只能传入一个参数
  print("$value");
});
myList.forEach((value) => print(value)); // 一行代码可以使用箭头函数

Map person = {"name": "张三", "age": 20};
person.forEach((key, value) { // Map可以传入两个参数
  print("$key---$value");
});
```

#### (3) map

```dart
List myList = [1, 3, 4];
var newList = myList.map((value) {
  return value * 2;
});
print(newList.toList()); // [2, 6, 8]
```

#### (4) where

```dart
List myList = [1, 3, 4, 5, 7, 8, 9];
var newList = myList.where((value) {
  return value > 5;
});
print(newList.toList()); // [7, 8, 9]
```

#### (5) any

```dart
List myList = [1, 3, 4, 5, 7, 8, 9];
var f = myList.any((value) {
  return value > 5;
});
print(f); // true，只要集合里面有满足条件的就返回true
```

#### (6) every

```dart
List myList = [1, 3, 4, 5, 7, 8, 9];
var f = myList.every((value) {
  return value > 5;
});
print(f); // false，每一个都要满足条件才返回true，否则返回false
```

## 12 方法

#### (1) 内置方法

```dart
print();
```

#### (2) 自定义方法

```dart
// 自定义方法的结构
返回类型 方法名称(参数1, 参数2...) {
  方法体
    return 返回值;
}

// 用int约束传入的参数的类型，n是形参
int sumNum(int n) {
  var sum = 0;
  for (var i = 1; i <= n; i++) {
    sum += i;
  }
  return sum;
}

print(sumNum(100)); // 100是实参
```

#### (3) 可选参数、默认参数

```dart
// sex和age都是可选参数，sex的默认参数是"男"
String printUserInfo(String username, [String sex = '男', int age]) {
  if (age != null) {
    return "姓名:$username---性别:$sex---年龄:$age";
  }
  return "姓名:$username---性别:$sex---年龄保密";
}
```

#### (4) 命名参数

```dart
String printUserInfo(String username, {int age, String sex = '男'}) {
  if (age != null) {
    return "姓名:$username---性别:$sex--年龄:$age";
  }
  return "姓名:$username---性别:$sex--年龄保密";
}
// 调用的时候，age和sex的顺序可以随意颠倒
print(printUserInfo(
  '张三',
  age: 20,
  sex: '未知',
));
```

#### (5) 把方法当作参数的方法

```dart
fn1() {
  print('fn1');
}

fn2(fn) {
  fn();
}
// 调用fn2，把fn1当作参数传入
fn2(fn1);
```

#### (6) 匿名方法

```dart
// 把匿名方法赋值给变量fn
var fn = () {
  print('我是一个匿名方法');
};
fn();
```

#### (7) 自执行方法

```dart
((int n) {
  print(n);
  print('我是自执行方法');
})(12);
```

#### (8) 箭头函数

```dart
List list = ['苹果', '香蕉', '西瓜'];
list.forEach((value) {
  print(value);
});
// 箭头函数中只能写一条语句，后面不能加分号
list.forEach((value) => print(value));

// 花括号中只能写一条语句，后面不能加分号，跟JS中的箭头函数还是有区别的
// 如果想在花括号中写多条语句，那么就不要用箭头函数
list.forEach((value) => {print(value)});
```

```dart
List list = [4, 1, 2, 3, 4];
var newList = list.map((value) {
  if (value > 2) {
    return value * 2;
  }
  return value;
});
print(newList.toList());
// 跟JS中一样，可以省略return
var newList2 = list.map((value) => value > 2 ? value * 2 : value);
print(newList2.toList());
```

#### (9) 递归

```dart
// 递归求一个数的阶乘
var sum = 1;
fn(n) {
  sum *= n;
  if (n == 1) {
    return;
  }
  fn(n - 1);
}

fn(5);
print(sum);
```

## 13 闭包

**全局变量：** 常驻内存，会污染全局

**局部变量：** 不会常驻内存，会被垃圾回收机制回收，不会污染全局

如果想实现一个变量常驻内存，但是又不会污染全局，即既具有全局变量的特点，又具有局部变量的特点，我们可以使用闭包。

我们可以这样通俗地理解闭包，函数内部嵌套函数，内部函数会调用外部函数的变量或参数，变量或参数会驻留在内存中。

闭包的写法：函数嵌套函数，并 return 里面的函数。

```dart
fn() {
  var a = 123; // 局部变量，常驻内存，不会污染全局
  return () {
    a++;
    print(a);
  };
}

var b = fn();
b();
b();
b();
```

## 14 库

在 Dart 中，库的使用是通过 import 关键字引入的。library 指令可以创建一个库，每个 Dart 文件都是一个库（即使没有使用 library 指令来指定）。

#### (1) Dart 中的库有三种

a. 我们自定义的库

```dart
import 'lib/xxx.dart';
```

b. 系统内置库

```dart
import 'dart:math';
import 'dart:io';
import 'dart:convert';

main() {
  // min方法和max方法是math库中的
  print(min(12, 23)); // 12
  print(max(12, 25)); // 23
}
```

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  var result = await getDataFromZhihuAPI();
  print(result);
}

// 接口：http://news-at.zhihu.com/api/3/stories/latest
getDataFromZhihuAPI() async {
  var httpClient = new HttpClient(); // 创建HttpClient对象
  var uri = new Uri.http('news-at.zhihu.com', '/api/3/stories/latest'); // 创建uri对象
  var request = await httpClient.getUrl(uri); // 发起请求，等待请求
  var response = await request.close(); // 关闭请求，等待响应
  // 解码响应的内容，用的convert库
  return await response.transform(utf8.decoder).join();
}
```

c. pub 包管理系统中的库

<https://pub.dev/packages>
<https://pub.flutter-io.cn/packages>
<https://pub.dartlang.org/flutter/>

#### (2) pub 包管理系统中的库的使用步骤

**步骤一：** 需要在项目根目录下新建一个 pubspec.yaml 文件

**步骤二：** 在 pubspec.yaml 文件中配置名称 、描述、依赖等信息

  ```yaml
name: xxx
description: A new flutter module project.
dependencies:
  http: ^0.12.0+2
  date_format: ^1.0.6
  ```

**步骤三：** 运行 pub get 获取包下载到本地

**步骤四：** 项目中引入库 `import 'package:date_format/date_format.dart';`

**步骤五：** 看文档使用

  ```dart
print(formatDate(DateTime(1989, 2, 21), [yyyy, '-', mm, '-', dd]));
  ```

#### (3) 库的重命名

如果引入的两个库中有相同名称标识符，这时候会出现冲突。在 Java 中，通常通过完整的包名路径来指定使用的具体标识符，甚至不用 import 都可以，但是 Dart 里面是必须使用 import 的，当冲突的时候，可以使用 as 关键字来指定库的前缀。

```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;

Element element1 = new Element(); // 使用lib1中的Element
lib2.Element element2 = new lib2.Element(); // 使用lib2中的Element
```

#### (4) 部分导入

如果只需要导入库的一部分，有两种模式：

a. 只导入需要的部分，使用 show 关键字

```dart
import 'package:lib1/lib1.dart' show foo;
```

b. 隐藏不需要的部分，使用 hide 关键字

```dart
import 'package:lib2/lib2.dart' hide foo;
```

#### (5) 延迟加载

延迟加载也称为懒加载，可以在需要的时候再进行加载。懒加载的最大好处是可以减少 APP 的启动时间。

懒加载使用 deferred as 关键字来指定，如下例子所示：

```dart
import 'package:deferred/hello.dart' deferred as hello;

// 当需要使用的时候，需要使用loadLibrary()方法来加载
greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```
