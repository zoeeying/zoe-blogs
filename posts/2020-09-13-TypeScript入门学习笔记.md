# TypeScript 入门学习笔记

## 1 简介

TypeScript 是 JavaScript 的一个超集，是**静态类型语言**，是由微软开发的自由和开源的编程语言，设计目标是开发大型应用。TypeScript 不可以直接运行在浏览器环境或者 node 环境，必须编译成 JavaScript 才行。

**补充：** TypeScript 是静态类型语言，即在运行之前的编译阶段就检查数据类型；JavaScript 是动态类型语言，即在运行时才去检查数据类型。

**TypeScript 相对于 JavaScript 的优势：**

有更好的错误提示，可以让我们在编写代码过程中就发现错误；开发的时候，编辑器可以提供更友好的代码提示；通过静态类型的声明，可以让代码语义更清晰易懂，可读性更强。

```typescript
function tsDemo(data: {x: number, y: number}) {
  return Math.sqrt(data.x ** 2 + data.y ** 2)
}
tsDemo({ x: 3, y: 4 })
```

```typescript
// 在函数外边声明静态类型
// type Point = { x: number; y: number } // 类型别名语法
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

1、安装 Node

2、全局安装 TypeScript，可以通过命令 `tsc --version` 查看 TS 的版本号，同时检查 TS 是否安装成功

```bash
npm install -g typescript
```

3、新建项目目录，在项目目录中新建文件 demo.ts

```typescript
function tsDemo(data: {x: number, y: number}) {
  return Math.sqrt(data.x ** 2 + data.y ** 2)
}
tsDemo({ x: 3, y: 4 })
```

4、在项目目录下执行命令 `tsc demo.ts`（使用 TypeScript 对 demo.ts 进行编译），生成 demo.js 文件，通过命令 `node demo.js` 运行该文件。其实可以使用工具可以简化这些步骤，需要全局安装 ts-node，然后通过命令 `ts-node demo.ts` 即可完成对 demo.ts 文件的编译和运行

## 3 静态类型

通过 `let count: number = 10` 可以声明一个 count 变量，它的类型只能是 number，同时，count 也会具备 number 静态类型所有的属性和方法。

静态类型也可以自定义，如下：

```typescript
interface Point {
  x: number
  y: number
}
const point: Point = {
  x: 3,
  y: 4,
}
```

如果一个变量是静态类型，那么该变量的类型是不能修改的，同时也确定了该变量上的属性和方法。

#### (1) 基础类型

| 基础类型  | 描述                                                     |
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
| symbol    | symbol 类型                                              |

```typescript
let a: number
console.log(a) // undefined

let age: number = 18
console.log(age)

let myName: string = '小畅叙'
console.log(myName)

let b: boolean = false
console.log(b)

// enum枚举类型，比如四季(春、夏、秋、冬)
enum SEASON { chun, xia, qiu, dong }
console.log(SEASON.qiu) // 2，索引
enum SEASON2 { chun = '春', xia = '夏', qiu = '秋', dong = '冬' }
console.log(SEASON2.qiu) // 秋

let t: any = 28
t = 'Zoe' // 可以赋值

// 可以是number也可以是sring
let temp: number | string = 123
temp = 'hello'
```

#### (2) 对象类型

```typescript
// 对象：{}
const student: { name: string; age: number } = { name: 'zoe', age: 28 }

// 数组：[]
const numbers: number[] = [1, 2, 3]

// 类：Class
class Person {}
const zoe: Person = new Person()

// 函数：function
const getTotal: () => number = () => {
  return 4869
}

interface Person {
  name: string
}
const rawData = '{"name": "zoe"}'
const newData: Person = JSON.parse(rawData)
```

#### (3) 类型注解和类型推断

**类型注解**的英文是 type annotation，是一种轻量级的为函数或变量添加约束的方式。

**类型推断**的英文是 type inference，是指 TypeScript 会自动地去尝试分析变量的类型。

```typescript
// 可以类型推断出count变量是number类型
let count = 123
// 如果变量声明和变量赋值分开写，无法推断出变量num的类型，会认为是any
let num
num = 123
```

写 TypeScript 代码的时候，就是希望每个对象的属性以及每个变量的类型都是固定的。如果 TS 能够自动分析出变量类型，就不需要使用类型注解，但是如果 TS 无法分析变量类型的话，就需要使用类型注解了。

比如函数中的形参以及函数的返回值，需要使用类型注解：

```typescript
function getTotal(first: number, second: number): number {
  return first + second
}
const total = getTotal(3, 5)
```

## 4 函数

### 4.1 三种定义方式

#### (1) 函数声明法

```typescript
// 使用function关键字来定义函数，:string表示函数返回的数据类型是string
function searchPerson(age: number): string {
  return '找到了' + age + '岁的人'
}
console.log(searchPerson(28))
```

```javascript
// 该函数没有返回值
function sayHello(): void {
  console.log('Hello')
}
```

```typescript
// 下面两个函数永远不可能执行到最后
function errorEmitter(): never {
  throw new Error()
}

function whileFunc(): never {
  while (true) {}
}
```

```typescript
// 解构赋值的类型注解写法
function add({ first, second }: { first: number; second: number }): number {
  return first + second
}
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
// 方式1，:string可以省略，因为通过类型推断可以推断出来
const searchPerson = (age: number): string => '找到了' + age + '岁的人'
// 方式2
const searchPerson2: (age: number) => string = (age) =>
  '找到了' + age + '岁的人'
```

### 4.2 函数分类

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
```

```typescript
// 构造函数赋值法
let arr1: number[] = new Array(1, 2, 3, 2)
let arr2: Array<string> = new Array('Zoe', '小畅叙', '颖颖')
let arr3: Array<boolean> = new Array(true, false, false)
```

```typescript
const arr: (number | string)[] = [1, 2, 'hello']
const undefinedArr: undefined[] = [undefined, undefined]
const objectArr: { name: string }[] = [
  {
    name: 'zoe',
  },
]

// 类型别名type alias
type User = { name: string; age: number }
const userArr: User[] = [
  {
    name: 'zoe',
    age: 18,
  },
]

class Teacher {
  name: string
  age: number
}
const teacherArr: Teacher[] = [
  new Teacher(),
  // 并不要求数组中每个元素都是Teacher的实例，普通对象只要有name属性和age属性也可以
  {
    name: 'zoe',
    age: 18,
  },
]
```

```typescript
// 元祖tuple，允许数组中有不同类型的数据，赋值需要注意顺序
// userInfo是一个元祖
let userInfo: [string, number, string] = ['Zoe', 28, 'female']

// 处理csv文件
const teacherList: [string, number, string][] = [
  ['zoe', 18, 'female'],
  ['yuan', 19, 'male'],
  ['qiu', 20, 'female'],
]
```

一个数组的长度是固定的，数组中的每个元素的数据类型也是固定的，该数组也可以被叫作**元祖**。

## 6 interface

```typescript
// 类型别名
type Person = { name: string }

// interface
interface Person {
  name: string
}
```

interface 可以代表对象和函数，无法代表基础类型，而类型别名可以代表基础类型：

```typescript
type Point = number 
```

```typescript
interface Person {
  // readonly name: string // name属性只能读，不能修改
  // [propName: string]: any // 允许有其它属性，属性名字是string类型，属性值是任意类型
  // say(): string // say方法，返回值是string类型
  name: string
  age?: number // age属性可有可无
}
const getPersonName = (person: Person): void => {
  console.log(person.name)
}
const person = {
  name: 'zoe',
  sex: 'female',
}
getPersonName(person) // 只需要保证必须有name属性即可，允许多出sex属性
// 传递字面量形式的参数（对象字面量直接赋值），会进行类型强校验，不允许多出sex属性
getPersonName({
  name: 'zoe',
  sex: 'female', // 报错，如果在interface中增加[propName: string]: any，就不会报错了
})

// 使用接口来约束类的属性和方法
class User implements Person {
  name = 'zoe'
  say() {
    return 'Hello'
  }
}

// 接口继承
interface Teacher extends Person {
  teach(): string
}
```

interface 除了可以用来定义属性和方法以外，还可以用来定义函数类型：

```typescript
interface SayHi {
  (word: string): string
}
const say: SayHi = (word: string) => {
  return word
}
```

interface 也可以用来定义数组这样的索引类型，参考文档。

interface 只是用来帮助我们做语法校验的工具，并不会真正地编译成 JavaScript 代码。

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

## 9 类

### 9.1 声明

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

### 9.2 继承和重写

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

```typescript
class Person {
  name = 'zoe'
  getName() {
    return this.name
  }
}
class Teacher extends Person {
  getTeacherName() {
    return 'teacher zoe'
  }
  // 重写父类中的getName方法
  // super可以看成是父类Person，super.getName()表示调用Person中的getName方法
  // super主要应用场景：子类重写了父类中的方法，如果还想调用父类中的方法，可以使用super
  getName() {
    return super.getName() + 'ying'
  }
}
const teacher = new Teacher()
console.log(teacher.getName())
console.log(teacher.getTeacherName())
```

### 9.3 访问类型

**访问类型：** public、protected、private

| 访问类型  | 描述                                     |
| --------- | ---------------------------------------- |
| public    | 属性或方法是公开的，允许在类中和类外访问 |
| protected | 属性或方法只能在类中及其子类中访问       |
| private   | 属性或方法只能在类中访问                 |

```typescript
// 访问类型
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

### 9.4 constructor

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

```typescript
class Person {
  // 传统写法
  // public name: string
  // constructor(name: string) {
  //   this.name = name
  // }
  // 简化写法
  constructor(public name: string) {}
}
```

如果父类中有 constructor，子类中也有 constructor，子类需要通过 `super()` 手动调用一下父类的构造器，并且传入父类构造器需要的参数：

```typescript
class Person {
  constructor(public name: string) {}
}
class Teacher extends Person {
  constructor(public age: number) {
    // 如果父类中没有constructor，子类也需要在constructor中调用一下super()，传参为空
    super('zoe')
  }
}
const teacher = new Teacher(28)
console.log(teacher.age)
console.log(teacher.name)
```

### 9.5 getter 和 setter

我们可以通过 getter 访问类中的私有属性，通过 setter 修改类中的私有属性：

```typescript
class People {
  constructor(private _name: string) {}
  get name() {
    return this._name
  }

  // set name(name: string) {
  //   this._name = name
  // }

  set name(name: string) {
    const realName = name.split(' ')[0]
    this._name = realName
  }
}
const people = new People('zoe')
people.name = 'zoe ying' // 调用setter
console.log(people.name) // 调用getter，name后面不需要加括号，打印出zoe
```

### 9.6 单例模式

```typescript
class Demo {
  private static instance: Demo
  // 不可以通过new Demo()的形式创建实例
  private constructor(public name: string) {}

  static getInstance() {
    if (!this.instance) {
      this.instance = new Demo('zoe')
    }
    return this.instance
  }
}
const demo1 = Demo.getInstance()
const demo2 = Demo.getInstance()
console.log(demo1.name)
```

### 9.7 readonly

readonly 是类的属性修饰符，使用该修饰符修饰的属性，只能被访问，不能被修改。

```typescript
// 通过使用getter，实现只读
class Person {
  private _name: string
  constructor(name: string) {
    this._name = name
  }
  get name() {
    return this._name
  }
}
```

```typescript
// 通过使用只读修饰符readonly，实现只读
class Person {
  constructor(public readonly name: string) {}
}
const person = new Person('zoe')
// person.name = 'ying' // 报错
console.log(person.name)
```

### 9.8 抽象类和接口

抽象类是用来定义共有的属性或者方法的。抽象类不能被实例化，只能被继承。

```typescript
abstract class Geom {
  width: number
  getType() {
    return 'Geom'
  }

  // 类的方法前面加上abstract，表示该方法的实现是抽象的
  // 只能定义一下这个方法，后面不能加{}
  abstract getArea(): number // 返回number类型的数据
}

// Circle是抽象类Geom的实现类
class Circle extends Geom {
  // 必须实现一下Geom中的抽象方法，不然会报错
  getArea() {
    return 4869
  }
}
```

接口是用来定义一些规范的。

```typescript
interface Person {
  name: string
}

interface Teacher extends Person {
  teachingAge: number
}

interface Student extends Person {
  age: number
}

const getUserInfo = (user: Person) => {
  console.log(user.name)
}
getUserInfo({ name: 'zoe' })
```

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

### 9.9 命名空间

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







