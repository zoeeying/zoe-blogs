---
title: TypeScript 入门
date: 2020-01-24
---

# TypeScript 入门

## 1 简介

Node 之父 Ryan Dahl 开源来了一个项目叫作 Deno，并且宣称 Deno 将是 Node 的代替品。Deno is a secure runtime for JavaScript and TypeScript and it aims to provide a productive and secure scripting environment for the modern programmer. It is built on top of V8, Rust, and TypeScript.

TypeScript 是 JavaScript 的一个超集，是静态类型语言，是由微软开发的自由和开源的编程语言，设计目标是开发大型应用。TypeScript 不可以直接运行在浏览器环境或者 node 环境，必须编译成 JavaScript 才行。

**补充：** TypeScript 是静态类型语言，即在运行之前的编译阶段就检查数据类型；JavaScript 是动态类型语言，即在运行时才去做数据类型检查。

**TypeScript 相对于 JavaScript 的优势：**

a. 有更好的错误提示，可以让我们在开发过程中，就发现错误。

b. 开发的时候，编辑器可以提供更友好的代码提示。

c. 通过静态类型的声明，可以让代码语义更清晰易懂，可读性更强。

```typescript
// 在函数外边声明静态类型
// type Point = { x: number; y: number } // 类型别名
// interface接口可以替代上面的类型别名写法
interface Point {
  x: number
  y: number
}
function tsDemo(data: Point) {
  return Math.sqrt(data.x ** 2 + data.y ** 2)
}
tsDemo({ x: 3, y: 4 })
```

## 2 环境搭建

**步骤一：** 安装 Node

**步骤二：** 全局安装 TS

```bash
cnpm install typescript -g
```

**步骤三：** 查看版本号

```bash
tsc --version
```

**步骤四：** 初始化项目，生成 package.json 文件

```bash
npm init -y
```

**步骤五：** 生成 tsconfig.json 文件，用于配置如何将 TS 编译成 JS

```bash
tsc --init
```

**步骤六：** 安装 @types/node 包

```bash
cnpm install @types/node -S
```

**步骤七：** 新建 hello.ts 文件

```typescript
var a: string = 'Hello Zoe'
console.log(a)
```

**步骤八：** 构建一下，把 TS 文件转换成 JS 文件：在 VSCode 中使用命令 Ctrl+Shift+B，选择 tsc: build - tsconfig.json。

**步骤九：** 使用 Node 运行 JS 文件。

```bash
node hello.js
```

## 2 数据类型

TypeScript 中的数据类型：

| 数据类型  | 描述                                                     |
| --------- | -------------------------------------------------------- |
| undefined | 声明一个变量，没有赋值，默认就是 undefined               |
| number    | 数值类型，既支持整型，也支持浮点型，NaN 也是 number 类型 |
| string    | 字符串类型                                               |
| boolean   | 布尔类型                                                 |
| enum      | 枚举类型，固定的多个值                                   |
| any       | 任意类型，万能类型                                       |
| void      | 空类型                                                   |
| array     | 数组类型                                                 |
| tuple     | 元祖类型                                                 |
| null      | 空类型                                                   |

```typescript
var a: number
console.log(a) // undefined

var age: number = 18
console.log(age)

var myName: string = '小畅叙'
console.log(myName)

var b: boolean = false
console.log(b)

// enum枚举类型，比如四季(春、夏、秋、冬)
enum SEASON { chun, xia, qiu, dong }
console.log(SEASON.qiu) // 2，索引
enum SEASON2 { chun = '春', xia = '夏', qiu = '秋', dong = '冬' }
console.log(SEASON2.qiu) // 秋

var t: any = 28
t = 'Zoe'
```

## 3 函数

### 3.1 函数定义的三种方式

#### (1) 函数声明法

```typescript
// 必须使用 function 关键字来定义函数，:string表示函数返回的数据类型是string
// age: number是形参
function searchPerson(age: number): string {
  return '找到了' + age + '岁的人'
}
// 28是实参
console.log(searchPerson(28))
```

#### (2) 函数表达式法

将函数赋值给一个变量，变量名就是函数名。

```typescript
const searchPerson = function (age: number): string {
  return '找到了' + age + '岁的人'
}
console.log(searchPerson(28))
```

#### (3) 箭头函数

```typescript
const searchPerson = (age: number): string => '找到了' + age + '岁的人'
console.log(searchPerson(28))
```

### 3.3 函数分类

#### (1) 有可选参数的函数

```javascript
// ?表示可选参数
function searchPerson(age: number, stature?: string): string {
  let personStr: string = ''
  personStr = '找到了' + age + '岁'
  // 这边if中的条件写法也需要注意！！！
  if (stature != undefined) {
    personStr = personStr + stature
  }
  return personStr + '的帅哥'
}
console.log(searchPerson(29, '北大毕业的'))
```

#### (2) 有默认参数的函数

```typescript
// 通过等号来设置默认参数
function searchPerson(age: number = 29, stature: string = '很帅的'): string {
  return '找到了' + age + '岁' + stature
}
console.log(searchPerson(18))
```

#### (3) 有剩余参数的函数

```typescript
// ...扩展运算符，string[]表示字符串数组
function searchPerson(...xuqiu: string[]): string {
  let person = '找到了'
  for (let i = 0;i < xuqiu.length;i++) {
    person = person + xuqiu[i]
    if (i < xuqiu.length - 1) {
      person = person + '、'
    }
  }
  person = person + '的帅哥'
  return person
}
console.log(searchPerson('29岁的', '清华毕业的', '185以上的'))
```

## 4 变量的作用域

let 声明的变量具有块级作用域（花括号内部）。

```typescript
var yangzi: string = '美女' // 全局变量
function zhengXing(): void { // 函数没有返回值，就用void
  console.log(yangzi) // 这里变量提升了，会打印出undefined（构建会报错）
  var yangzi: string = '丑女' // 局部变量
  console.log(yangzi) // 丑女
  {
    let temp = 'hello'
  }
  // 'hello'，构建后，TS文件中的let会被转成var，所以运行JS文件这里还是能打印出'hello'（构建会报错）
  console.log(temp)
}
zhengXing()
console.log(yangzi) // 美女
```

## 5 数组

```typescript
// 字面量赋值法
let arr1: number[] = [1, 2, 3, 2] // 数值数组
let arr2: Array<string> = ['Zoe', '小畅叙', '颖颖'] // 字符串数组
let arr3: Array<boolean> = [true, false, false] // 布尔数组
// let arr4: number[] = [1, true] // 报错，不允许有非number的数据
// 元祖，允许数组中有不同类型的数据，赋值需要注意顺序
// 不推荐使用
let arr5: [string, number] = ['Zoe', 28]
```

```typescript
// 构造函数赋值法
let arr1: number[] = new Array(1, 2, 3, 2)
let arr2: Array<string> = new Array('Zoe', '小畅叙', '颖颖')
let arr3: Array<boolean> = new Array(true, false, false)
```

## 6 字符串

TypeScript 中有两种类型的字符串：**基本类型字符串**和**引用类型字符串**，用法都是一样的，具有的方法和属性也都是一样的。都具有 length 属性，可以获取字符串的长度。

```typescript
let zoe1: string = '小畅叙1' // 基本类型字符串
let zoe2: String = new String('小畅叙2') // 引用类型字符串
```

| 方法分类   | 方法                                            |
| ---------- | ----------------------------------------------- |
| 查找字符串 | indexOf、lastIndexOf，返回索引，查找不到返回 -1 |
| 截取字符串 | substring                                       |
| 替换字符串 | replace                                         |

```typescript
let something: string = '清早起来打开窗，心情美美哒，我是一个美美哒小姐姐'
let me: string = '美美哒'
console.log(something.indexOf(me)) // 10
console.log(something.lastIndexOf(me)) // 18
// 从索引8截取到末尾
console.log(something.substring(8)) // 心情美美哒，我是一个美美哒小姐姐
console.log(something.substring(8, 13)) // 心情美美哒
console.log(something.replace('小姐姐', '小哥哥')) // 把something字符串中的'小姐姐'替换成'小哥哥'返回新的字符串
```

## 7 日期

```typescript
// 不传递参数
let d1: Date = new Date()
console.log(d1)

// 传递整数
let d2: Date = new Date(1000) // 1970-01-01 00:00:00往后1000毫秒
console.log(d2)

// 传递字符串
let d3: Date = new Date('2020/01/30 06:03:10')
let d4: Date = new Date('2020-01-30 06:03:10')
let d5: Date = new Date('2020-01-30T06:03:10')
console.log(d3)
console.log(d4)
console.log(d5)
```

## 8 正则表达式

**正则表达式修饰符：**

| 修饰符 | 描述         |
| ------ | ------------ |
| i      | 忽略大小写   |
| g      | 全局         |
| m      | 多行匹配模式 |

```typescript
// 构造函数声明法
let reg1: RegExp = new RegExp('zoeeying')
let reg2: RegExp = new RegExp('zoeeying', 'gi')
// 字面量声明法
let reg3: RegExp = /zoeeying/
let reg4: RegExp = /zoeeying/gi
```

**正则表达式方法：**

| 方法               | 描述                                                                                                                                   |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| `reg.test(string)` | 如果字符串中存在匹配 reg 正则的字符串，则返回 true，否则返回 false                                                                     |
| `exec(string)`     | 如果字符串中存在匹配 reg 正则的字符串，则返回该字符串的相关信息数组，否则返回 null，但是不能用数组类型的变量接收 exec 的返回值，会报错 |

```typescript
// 构造函数声明法
let reg1: RegExp = new RegExp('zoeeying')
let reg2: RegExp = new RegExp('zoeeying', 'gi')

// 字面量声明法
let reg3: RegExp = /zoeeying/
let reg4: RegExp = /zoeeying/gi

let reg5: RegExp = /zoeeying/i
let str: string = 'zoeeying.github.io'
console.log(reg5.test(str)) // true
/**
 * [ 'zoeeying',
 *   index: 0,
 *   input: 'zoeeying.github.io',
 *   groups: undefined
 * ]
 */
console.log(reg5.exec(str))
```

## 9 面向对象

### 9.1 类的声明

类是具体事物（对象）的抽象，对象是类的具体表现。

```typescript
class Person {
  name: string
  age: number
  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }
  say() {
    console.log('这人说话了')
  }
}

let ming: Person = new Person('小明', 28)
console.log(ming) // Person { name: '小明', age: 28 }
ming.say() // 这人说话了
```

### 9.2 类的修饰符

**访问修饰符：** public、protected、private

| 修饰符    | 描述                                                        |
| --------- | ----------------------------------------------------------- |
| public    | 属性或方法是公开的                                          |
| protected | 如果属性或方法用 protected 修饰，则只能在类中及其子类中访问 |
| private   | 如果属性或方法用 private 修饰，则只能在类中访问             |

```typescript
// 访问修饰符
class Person {
  public sex: string
  protected name: string
  private age: number
  constructor(sex: string, name: string, age: number) {
    this.sex = sex
    this.name = name
    this.age = age
  }
  public sayHello() {
    console.log('hello')
  }
  protected sayLove() {
    console.log('love')
  }
}

var ming: Person = new Person('男', '小明', 29)
console.log(ming.sex)
// console.log(ming.name) // 报错，只能在类Person及其子类中访问
// console.log(ming.age) // 报错，只能在类Person中访问
ming.sayHello()
// ming.sayLove() // 报错，只能在类Person及其子类中访问
```

**只读修饰符：**readonly

```typescript
// 只读修饰符readonly
class Man {
  public readonly sex: string = 'male' // 只读属性直接赋值
}
```

### 9.3 继承和重写

**继承：** 允许我们创建一个类（子类），继承已有的类（父类）的所有属性和方法，子类可以创建父类中没有的属性和方法。

TypeScript 不支持多重继承。

```typescript
class Father {
  public name: string
  public age: number
  public skill: string
  constructor(name: string, age: number, skill: string) {
    this.name = name
    this.age = age
    this.skill = skill
  }
  public showInterests() {
    console.log('兴趣爱好是打游戏')
  }
}

class Child extends Father {
  public character: string = '善良'

  public showInterests() {
    super.showInterests() // 调用父类的showInterests方法
    console.log('兴趣爱好还有看书')
  }

  public hasCapacity() {
    console.log('黑客')
  }
}
```

### 9.4 接口

接口是用来定义一些规范的。

```typescript
// 用接口规范属性
interface BoyFriend {
  sex: string
  interests: string
  isWealthy?: Boolean // 表示可选的
}
let myBoy: BoyFriend = {
  sex: '男',
  interests: '看书、电影、音乐'
}

// 用接口规范方法
interface SearchMan {
  (source: string, subString: string): boolean
}
let mySearch: SearchMan

mySearch = function (source: string, subString: string): boolean {
  let flag = source.search(subString)
  return flag != -1
}
console.log(mySearch('nice, clever, skillful', 'clever')) // true
```

### 9.5 命名空间

在大型应用中，我们可以采用命名空间来区分不同的模块，让程序更有层次感且变量之间互不干扰。

```typescript
namespace niceGirl {
  export class Girl {
    public name: string = '小畅叙'
    talk() {
      console.log('我是小畅叙')
    }
  }
}
namespace badGirl {
  export class Girl {
    public name: string = '坏女人'
    talk() {
      console.log('我是坏女人')
    }
  }
}

let girl1: niceGirl.Girl = new niceGirl.Girl()
let girl2: badGirl.Girl = new badGirl.Girl()
girl1.talk()
girl2.talk()
```
