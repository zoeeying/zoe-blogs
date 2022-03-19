# Iterator 遍历器

Iterator 是一种接口机制，为各种不同的**数据结构**提供统一的访问机制。ES6 已经把接口机制封装好了，部署在指定的数据结构上，指向遍历器对象。

#### (1) 作用

Iterator 遍历器为各种数据结构，提供一个统一的、简便的访问接口，使得数据结构的成员能够按照某种次序排列。

ES6 创造了一种新的遍历命令 for...of... 循环，只有部署了 Iterator 接口的数据才可以使用这个命令来遍历，Iterator 接口主要供 for...of... 消费。

#### (2) 工作原理

创建一个指针对象（遍历器对象），指向数据结构的起始位置。

第一次调用 next 方法，指针自动指向数据结构的第一个成员。

接下来不断调用 next 方法，指针会一直往后移动，直到指向最后一个成员。

每次调用 next 方法，返回的是一个包含 value 和 done 的对象，value 表示当前成员的值，done 对应的布尔值表示当前的数据结构是否遍历结束。

当遍历结束的时候，返回的 value 值是 undefined，done 值是 true。

```javascript
// 模拟指针对象（遍历器对象）
// myIterator是Iterator接口
function myIterator(arr) {
  // 记录指针的位置
  let nextIndex = 0
  return {
    next: function () {
      // nextIndex++可以先使用nextIndex的值，再+1
      // 返回遍历器对象
      return nextIndex < arr.length ? { value: arr[nextIndex++], done: false } : { value: undefined, done: true }
    },
  }
}
let arr = [1, 4, 53, 'abc']
let iteratorObj = myIterator(arr)
// 使用闭包，next函数引用了myIterator函数中的变量
// 在外部调用内部的next函数
// 延长了nextIndex的生命，会一直驻留于内存中，并且可以修改它
iteratorObj.next()
iteratorObj.next()
iteratorObj.next()
iteratorObj.next()
iteratorObj.next()
```

```javascript
// arguments是伪数组，不具有数组的一般方法，比如forEach
function fun() {
  for (let i of arguments) {
    console.log(i)
  }
}
fun(1, 2, 3, 'sss')
```

在学习 Symbol 的时候，提到 ES6 提供了 11 个内置的 Symbol 值，指向语言内部使用的方法。例如：对象的 Symbol.iterator 属性，指向该对象的默认遍历器方法。当使用 for...of... 去遍历某一个数据结构的时候，首先去找它的 Symbol.iterator 属性，找到了就去遍历，没有找到的话就报错 xxx is not iterable。

```javascript
// 等同于在指定的数据结构上部署了Iterator接口
// this代表targetData
let targetData = {
  [Symbol.iterator]: function () {
    let nextIndex = 0
    return {
      next: function () {
        return nextIndex < this.length ? { value: this[nextIndex++], done: false } : { value: undefined, done: true }
      },
    }
  },
}
```

#### (3) 补充

使用三点运算符，解构赋值，默认去调用 Iterator 接口。
