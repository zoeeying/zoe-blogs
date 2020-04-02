---
layout: post
title: Generator 函数
subtitle: Generator 函数是一个状态机，用来生成遍历器对象
author: 小畅叙
tags:
    - ES6
---

# Generator 函数

## 1 简介

#### (1) 基本概念

a. 是 ES6 提供的解决异步编程的方法之一。

b. 是一个状态机，内部封装了不同状态的数据。

c. 是用来生成遍历器对象的。

d. 也可称为**可暂停函数（惰性求值函数）**，yield 可暂停，next 方法可启动，每次返回的是 yield 后面的表达式结果。

#### (2) 特征

a. function 与函数名之间有一个星号。

b. 函数体内部使用 yield 语句定义不同的状态。

c. Generator 函数返回的是指针对象，而不会执行函数内部的逻辑。

d. 调用 next 方法函数内部逻辑开始执行，遇到 yield 暂停执行，返回 `{ value: yield后表达式结果/undefined, done: true/false }`。

e. 再次调用 next 方法会从上一次停止时的 yield 处开始，直到最后。

f. yield 语句返回值通常为 undefined，当调用 next 方法时，传参内容会作为被启动的 yield 语句的返回值。

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
