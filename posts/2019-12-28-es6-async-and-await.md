---
title: async&await
date: 2019-12-28
---

# async&await

## 1 简介

async 是异步的简写，而 await 是 async wait 的简写。async 用于声明一个异步函数，而 await 用于等待一个异步方法执行完成。

async&await 在真正意义上解决了**异步回调**的问题，它用同步流程表达异步操作。源自 ES2017。本质上，async&await 其实是 Generator 函数的语法糖。

await 会阻塞程序的执行，把异步变成同步。

```javascript
async function foo () {
  await 异步操作
  await 异步操作
}

async function foo () {
  return new Promise(resolve => {
    // setTimeout(function () {
    //   resolve()
    // }, 2000)
    setTimeout(resolve, 2000) // 上面方法的简写
  })
}
```

**async&await 的特点：**不需要像 Generator 那样去调用 next 方法，遇到 await 等待当前的异步操作完成再往下执行；返回的是 Promise 对象，可以用 then 方法进行下一步操作，进一步说，async 函数可以看作是由多个异步操作包装成的一个 Promise 对象，而 await 命令就是内部 then 命令的语法糖；取代 Generator 函数的星号，await 取代 Generator 函数的 yield；语义上更为明确，使用简单，暂时没有什么缺点。

## 2 基本用法

```java
// 异步方法
async function testAsync () {
  return 'Hello Async'
}
const result = testAsync()
console.log(result) // Promise { 'Hello Async' }，不是直接返回'Hello Async'字符串，而是返回Promise对象
```

**await 必须放在 async 方法中。**

await 不仅可以等待 async 函数返回 Promise 对象，也可以等待普通函数返回普通值。

```javascript
function getSomething () {
  return 'something'
}
async function testAsync () {
  return 'Hello Async'
}
async function test () {
  const v1 = await getSomething()
  const v2 = await testAsync()
  console.log(v1) // something
  console.log(v2) // Hello Async
}
test()
```

await 也可以等待普通函数来模拟异步，**普通函数必须返回 Promise 对象**。

```javascript
function takeLongTime () {
  return new Promise(resolve => {
    setTimeout(() => resolve('after a long time'), 3000)
  })
}
async function test () {
  const v = await takeLongTime()
  console.log(v) // after a long time
}
test()

async function asyncFunc () {
  // Promise通过resolve方法修改成功状态，并且传入值
  let result = await Promise.resolve('promise')
  console.log(result) // promise
  result = await Promise.reject('failed')
  console.log(result) // 红色报错，且会显示出failed
}
asyncFunc()
```

## 3 案例

```javascript
async function getNews (url) {
  // 是不是可以不用返回Promise对象，只是把data返回出去就行？
  return new Promise((resolve, reject) => {
    $.ajax({
      method: 'GET',
      url,
      success: data => resolve(data),
      // error: error => reject(error),
      error: error => resolve(false), // 使用resolve返回false，还是成功的状态
    })
  })
}
async function sendXml () {
  let result = await getNews('http://localhost:3000/news?id=9')
  if (!result) {
    alert('暂时没有新闻推送！')
    return
  }
  console.log(result) // 打印出新闻数据
  result = await getNews('http://localhost:3000' + result.commentsUrl)
  console.log(result) // 打印出新闻评论数据
}
sendXml()
```

## 4 Flutter 中的 async&await

Flutter 中支持 async&await 。这一点和 ES7 很像，如下代码所示，只是定义的位置不同。同时异步操作也和 ES6 中的 Promise 很像，只是 Flutter 中返回的是 Future 对象，通过 then 可以执行下一步。如果返回的还是 Future 便可以 `then().then.()` 的流式操作了。

```dart
// 模拟等待两秒，返回OK
request() async {
  await Future.delayed(Duration(seconds: 2))
    return "ok!"
}

// 得到"ok!"后，将"ok!"修改为"ok from request"
doSomeThing() async {
  String data = await request()
    data = "ok from request"
    return data
}

// 打印结果
renderSome() {
  doSomeThing().then((value) {
    print(value)
      // 输出ok from request
  })
}
```
