---
title: Generator 函数
date: 2019-12-28
---

# Generator 函数

## 1 简介

Generator 函数是 ES6 提供的解决异步编程的方案之一，语法行为与传统完全不同。从语法上，可以把 Generator 函数理解成一个状态机，封装了多个内部状态。执行 Generator 函数会返回一个遍历器对象，也就是说 Generator 函数不仅是状态机，还是一个遍历器对象生成函数，返回的遍历器对象可以依次遍历 Generator 函数内部的每一个状态。

Generator 函数也可称为**可暂停函数（惰性求值函数）**，yield 可暂停，next 方法可启动，每次返回的是 yield 后面的表达式结果。

从形式上看，Generator 函数是一个普通函数，但是它有两个**特征**：一是 function 命令与函数名之间有一个星号；二是函数内部使用 yield 语句定义不同的内部状态。

Generator 函数的调用方法与普通函数一样，不同的是，调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象（遍历器对象）。

必须调用遍历器对象的 next 方法，使得指针移向下一个状态，也就是说，每次调用 next 方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一条 yield 语句（或者 return 语句）为止。遇到 yield 语句会暂停执行，返回 `{ value: yield后表达式结果/undefined, done: true/false }`，yield 语句返回值通常为 undefined，当调用 next 方法时，传参内容会作为被启动的 yield 语句的返回值。

Generator 函数是分段执行的，yield 语句是暂停执行的标记，而 next 方法可以恢复执行。

```js
function* myGenerator() {
  console.log('开始执行')
  // 遇到yield就会暂停，返出去一个对象，{value: xxx, done: true/false}
  // value值是yield后面表达式的值或者是语句的执行结果
  // const result = yield 'hello'，result为undefined
  yield 'hello'
  console.log('暂停后，再次执行')
  yield 'generator'
  console.log('遍历结束')
  return '函数返回值'
}
// 返回的是遍历器对象，用于遍历Generator函数中的状态
// myGenerator()调用了函数，但是函数中的console.log不会打印
let myGeneratorPin = myGenerator()
// 先打印'开始执行'，然后再打印{value: 'hello', done: false}
console.log(myGeneratorPin.next())
// 打印'开始执行'=>对象=>'暂停后，再次执行'=>对象
// console.log(myGeneratorPin.next('aaa'))，如果这里传参了，上面的result就为'aaa'
console.log(myGeneratorPin.next())
// 打印'开始执行'=>对象=>'暂停后，再次执行'=>
// 对象=>'遍历结束'=>对象{value: '函数返回值', done: true}
console.log(myGeneratorPin.next())
```

## 2 为对象部署 Iterator 接口

默认情况下，JS 中的对象是没有部署 Iterator 接口的，即用 for...of... 去遍历的话，会报错这样的错：xxx is not iterable。我们可以人为地给对象添加 Symbol.iterator 属性，值为一个 Generator 函数（返回的是遍历器对象），从而实现为对象部署 Iterator 接口。

```javascript
let obj = {
  username: 'zoe',
  age: 28,
}
obj[Symbol.iterator] = function* objGenerator () {
  yield 1
  yield 2
  yield 3
}
// 遍历器对象调用next方法，遍历Generator函数中的状态
for (let i of obj) {
  console.log(i) // 1,2,3，遍历输出yield中的状态
}
```

## 3 案例

```javascript
function getNews (url) {
  // 使用了jQuery方法
  $.get(url, function (data) {
    let url = 'http://localhost:3000' + data.commentsUrl
    // 移动指针并且传参
    SX.next(url)
  })
}
function* sendXml () {
  // 这里的url是上面的SX.next(url)传过来的
  let url = yield getNews('http://localhost:3000/news?id=3')
  yield getNews(url)
}

// 获取遍历器对象
let SX = sendXml()
// 移动指针
SX.next()
```
