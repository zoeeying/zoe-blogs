---
title: JavaScript 函数式编程
date: 2020-02-25
---

# JavaScript 函数式编程

一个函数被当作**第一类成员（公民）**时，它不仅可以被声明为一个变量，而且可以被当作函数参数传递，这些函数甚至可以作为函数的执行结果被返回。一个语言是否可以进行函数式编程，取决于该语言中的函数是否是**第一类公民**。JavaScript 就是函数式编程语言，因为它的函数是第一类公民，这意味着函数就是数据，可以像变量那样被保存、检索或者在应用程序内部传递。

**JavaScript 函数式编程规则：** 保持数据的不可变性；确保尽量使用纯函数，只接收一个参数，返回数据或者其它函数；尽量使用递归处理循环。

## 1 命令式和声明式

函数式编程是更广义编程范式**声明式编程**的一部分。

**命令式编程**的特点是代码重点关注的是达成目标的具体过程。使用命令式编程风格来编写代码时，需要辅以大量注释说明来帮助其它人理解代码。

**声明式编程**是一种编程风格，采用该风格的应用程序代码有一个比较突出的特点，就是**对执行结果的描述远胜于执行过程**。在一个声明式程序中，语法本身描述了将会发生什么，相关的执行细节则被隐藏了。声明式程序易于解释具体用途，因为其代码本身就描述了将会发生什么。

React 组件就是以声明式风格来构造 DOM 元素的，省去了很繁琐的创建元素、设置元素、把元素挂载到 DOM 树上的操作。

## 2 不可变性

不可变性就是指不可改变。在函数式编程中，数据是不可变的，它们永远无法被修改。

在应用程序中的**不可变数据**体现在，在不修改原生数据结构的前提下，我们在这些数据结构的拷贝上进行编辑，并使用它们取代原生的数据。

## 3 纯函数

纯函数是一个返回结果只依赖于输入参数的函数。纯函数**至少需要接收一个参数**并且总是**返回一个值或者其它函数**，它**不会产生副作用（比如修改 DOM）**、不修改全局变量或者任何应用程序的 state，它将输入的参数**当作不可变数据**。

纯函数天生是可测试的，它不会改变执行环境或者环境中的任何东西，因此不需要装配或者卸载复杂的测试环境。纯函数需要访问的任意数据都是通过参数进行传递的。当测试一个纯函数时，用户控制着参数，因此也可以预估执行结果。

在 React 中，UI 是用纯函数表示的。

当编写纯函数时，必须遵循以下**三个原则**：函数应该至少接收一个参数；函数应该返回一个值或者其它函数；函数不应该修改或者影响任何传给它的参数。

## 4 数据转换

如果数据是不可变的，那么应用程序内部如何进行数据转换呢？函数式编程的做法是将一种数据转换成另外一种数据。我们使用函数来生成转换后的副本，这些函数使得命令式的代码更少，并且大大降低了复杂度，JavaScript 提供了很多这样的函数：Array.join、Array.map、Array.reduce、Array.reduceRight、Array.filter。函数式编程武器库中的终极武器必须具备将数组转换成**基元**或**其他对象类型**的能力。

#### (1) Array.filter

Array.filter 函数可以根据源数组创建一个新数组，该函数的唯一参数是一个谓词（一个谓词就是一个永远都会返回布尔值的函数），它会在数组的每个元素上调用该谓词，根据返回值来决定该元素是否应该被添加到新的数组中。

#### (2) Array.map

Array.map 函数可以构造任意对象、数值、数组、函数等类型的数组。

下面是一个小案例，有个需求，给定一个学校对象数组，把其中的**六合一中**改成**六合高级中学**。

```javascript
const schools = [
  { name: '竹程小学' },
  { name: '竹程中学' },
  { name: '六合一中' },
  { name: '南通大学' },
  { name: '河海大学' }
]

// 方法1
const updatedSchools = schools.map(item => {
  if (item.name === '六合一中') {
    return {
      ...item,
      name: '六合高级中学'
    }
  }
  return item
})

// 方法2是方法1的简写，工作中很常用
const updatedSchools2 = schools.map(item =>
  item.name === '六合一中' ? { ...item, name: '六合高级中学' } : item
)
```

Array.map 会将每个元素的索引作为第二个参数注入回调函数中，即变量 i。当 i 和目标元素的索引不相等时，我们只需将相同元素插入新数组即可，当 i 和目标元素的索引相等时，我们会使用一个新对象替换新数组中相同索引位置的对象。

如果想把一个对象转换成一个数组，可以结合 Array.map 和 Object.keys 来达成目的。

#### (3) Array.reduce

Array.reduce 函数可以用来将数组转换成任意值，比如数字、字符串、布尔值、对象、函数。Array.reduceRight 和 Array.reduce 的工作原理类似，主要差别在于，它是从数组的末尾开始处理元素的。

Array.reduce 函数接收两个参数：回调函数和原生值。

下面 Array.reduce 函数的几个常用用途：

```javascript
// 找出数字数组中最大的数字
const ages = [32, 12, 56, 21, 68, 7, 28]
const max = ages.reduce((max, value) => (value > max ? value : max), 0)
console.log(max)
```

```javascript
// 数组去重
const colors = ['yellow', 'green', 'blue', 'red', 'green', 'blue']
const distinctColors = colors.reduce(
  (distinct, color) =>
    distinct.includes(color) ? distinct : [...distinct, color],
  []
)
console.log(distinctColors)
```

```javascript
// 把数组转换成哈希对象
const persons = [
  {
    name: 'Zoe',
    age: 28,
    skill: 'Coding'
  },
  {
    name: 'Cha',
    age: 12,
    skill: 'Sleeping'
  },
  {
    name: 'Xu',
    age: 21,
    skill: 'Gaming'
  }
]
const hashPersons = persons.reduce((hash, { name, age, skill }) => {
  hash[name] = { age, skill }
  return hash
}, {})
console.log(hashPersons)
```

## 5 高阶函数

**高阶函数**可以精确地控制函数，既可以将函数当作参数传递，也可以将函数作为执行结果返回，或者二者兼而有之。高阶函数既可以接收作为参数的函数，也可以将其它函数作为返回值。如果声明一个函数的时候，使用一个以上的箭头，那么该函数就是高阶函数。下面是一个高阶函数的例子：

```javascript
const createScream = logger => message => logger(message.toUpperCase() + '!!!')
```

**第一类高阶函数**是将其它函数当作参数传递的函数，比如 Array.filter、Array.map、Array.reduce 都是高阶函数。

**第二类高阶函数**是可以返回其它函数的函数，它可以帮助我们处理 JavaScript 中复杂的异步操作，也可以帮助我们方便地创建需要使用或者复用的函数。**柯里化（Currying）**是一种采用了高阶函数的函数式编程技巧，它实际上是一种将某个操作中已经完成的结果保留，直到其余部分也完成后一并提供的机制，这是通过一个函数返回另外一个函数实现的，即**柯里函数**。下面是一个柯里化的例子：

```javascript
const userLogs = username => message => console.log(`${username} -> ${message}`)
const log = userLogs('zoe')
log('will be a great coder') // zoe -> will be a great coder
```

## 6 递归

递归是用户创建的函数调用自身的一种技术。递归是一种非常强大的函数式编程技巧，并且易于实现。在实际开发过程中处理循环操作时应该尽量使用递归。

下面是几个很常用的递归案例，可以学习一下：

#### (1) 从 10 开始倒数计数

一般来说，在解决实际问题涉及到**循环**时，递归函数可以提供一种替代性的方案。

递归是一个可以很好地处理异步过程的**函数式编程技术**，函数调用自身也能实现延迟调用。

```javascript
const countdown = (value, fn, delay = 1000) => {
  fn(value)
  // return value > 0 ? countdown(value - 1, fn) : value
  // 延迟调用
  return value > 0 ? setTimeout(() => countdown(value - 1, fn), delay) : value
}
countdown(10, value => console.log(value))
```

#### (2) 搜索数据结构

递归是一种搜索数据结构的好方法。

```javascript
var zoe = {
  type: 'person',
  data: {
    gender: 'female',
    info: {
      id: 28,
      fullname: {
        first: 'Zhou',
        last: 'Zoe'
      }
    }
  }
}
const deepPick = (fields, object = {}) => {
  const [first, ...remaining] = fields.split('.')
  return remaining.length
    ? deepPick(remaining.join('.'), object[first])
    : object[first]
}

// 需求：给定一个对象，根据点符号拼接的嵌套属性字符串，找出对应的属性值
console.log(deepPick('type', zoe))
console.log(deepPick('data.info.fullname.first', zoe))
```

## 7 合成

函数式编程会将具体的业务逻辑拆分成小型的纯函数，再将这些小型函数整合到一起，或者合成为一个更大的函数，最终构造出一个应用程序。

下面是一个合成为高阶函数的例子：

```javascript
const func1 = date => date
const func2 = date => date.toLocaleString()

// 普通写法，both函数充当了两个函数之间传递数据的管道
// 如果传递的值需要穿过很多个不同的函数，就很麻烦了
// const both = date => func2(func1(date))

// 优化写法，方便扩展，方便添加更多函数，方便调整函数执行顺序
// 数组fns是函数之间传递数据的管道
// reduce的使用，可以正序调用fns数组中的函数
const compose = (...fns) => arg => fns.reduce((composed, f) => f(composed), arg)
const both = compose(func1, func2)
console.log(both(new Date()))
```
