---
title: 深拷贝和浅拷贝
date: 2019-10-24
---

# 深拷贝和浅拷贝

## 1 概述

JavaScript 数据类型分为基本类型和引用类型。

**基本类型（值类型）：** 字符串（String）、数字（Number）、布尔（Boolean）、空（Null）、未定义（Undefined）、Symbol（Symbol 是 ES6 引入的一种新的原始数据类型，表示独一无二的值）

**引用类型：** 对象（Object）、数组（Array）、函数（Function）

拷贝**基本类型**的数据，会生成一份新的数据，修改拷贝后的数据不会影响原数据。

拷贝**引用类型**的数据，不会生成新的数据，而是拷贝引用，修改拷贝后的数据会影响原来的数据。

深拷贝和浅拷贝只是针对引用类型（一般是数组和对象），基本类型没有深拷贝和浅拷贝之说。

**浅拷贝：** 将原对象或原数组的引用直接赋给新对象或新数组，新对象或数组只是原对象或原数组的一个引用，修改拷贝以后的数据会影响原数据。

**深拷贝：** 创建一个新的对象或数组，将原对象的各项属性的值或数组的所有元素拷贝过来，拷贝的是值，不是引用 ，修改拷贝以后的数据不会影响原数据。

## 2 深拷贝数组

如果数组的元素都是基本类型的值，那么通过下面的方式，可以实现深拷贝：

#### (1) 遍历

遍历的方法有很多，这里只是使用了 map 方法，它会返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。

```javascript
let arr = [1, 2, 3, 4, 5]
let arrCopy = arr.map(item => item)
arrCopy[0] = 100
console.log(arr) // [ 1, 2, 3, 4, 5 ]
console.log(arrCopy) // [ 100, 2, 3, 4, 5 ]
```

#### (2) ES6 扩展运算符

```javascript
let arr = [1, 2, 3, 4, 5]
let arrCopy = [...arr]
arrCopy[0] = 100
console.log(arr) // [ 1, 2, 3, 4, 5 ]
console.log(arrCopy) // [ 100, 2, 3, 4, 5 ]
```

#### (3) slice

`Array.prototype.slice(start, end)`，从已有的数组中返回选定的元素组成的新数组，不改变原数组。

start 表示起始元素的下标，end 表示终止元素的下标，新数组不包括 end；如果不带任何参数，默认返回一个和原数组元素相同的新数组；如果有负数参数，则用数组长度加上该负数来确定相应的值；如果 end 小于 start，则返回空数组。

```javascript
let arr = [1, 2, 3, 4]
let arrCopy = arr.slice()
arrCopy[0] = 100
console.log(arr) // [1, 2, 3, 4]
console.log(arrCopy) // [100, 2, 3, 4]
```

#### (4) concat

`Array.prototype.concat()`，用于连接两个或多个数组，不会改变原数组，而仅仅会返回被连接数组的一个副本。

```javascript
// 用法
// 如果不带任何参数，array.concat()相当于array.concat([])，即把array和一个空数组合并后返回
array.concat(array1, array2, ......, arrayN)

let arr = [1, 2, 3, 4]
let copyArr = arr.concat()
arrCopy[0] = 100
console.log(arr) //[1, 2, 3, 4]
console.log(arrCopy) //[100, 2, 3, 4]

// 如果数组中的元素有对象（数组），那么修改对象（数组），会影响原数组
let arr = [1, 3, { username: 'zoe' }]
let arrCopy = arr.concat() // 不传入参数，相当于拷贝原数组
arrCopy[2].username = 'chao'
console.log(arr) // [ 1, 3, { username: 'chao' } ]
```

## 3 深拷贝对象

如果对象的属性对应的值都是基本类型的，那么可以通过下面的方式来实现深拷贝：

#### (1) 遍历

```javascript
let obj = {
  name: '小畅叙',
  job: '程序员'
}
let objCopy = {}
Object.keys(obj).forEach(item => {
  objCopy[item] = obj[item]
})
objCopy.job = '画家'
console.log(obj) // { name: '小畅叙', job: '程序员' }
console.log(objCopy) // { name: '小畅叙', job: '画家' }
```

#### (2) 扩展运算符

ES6 扩展运算符，用于取出参数对象的所有可遍历属性，拷贝到当前对象之中。

```javascript
let obj = {
  name: '小畅叙',
  job: '程序员'
}
let objCopy = { ...obj }
objCopy.job = '画家'
console.log(obj) // { name: '小畅叙', job: '程序员' }
console.log(objCopy) // { name: '小畅叙', job: '画家' }
```

#### (3) Object.assign

ES6 的 `Object.assign()` 用于对象的合并，将源对象（source）的所有**可枚举属性**复制到目标对象（target），并返回 target。

```javascript
// 用法
Object.assign(target, source1, source2)

let obj = {
  name: '小畅叙',
  job: '程序员'
}
// 把obj作为源对象
let copyObj = Object.assign({}, obj)
copyObj.name = '我不是小畅叙'
console.log(obj) // {name: '小畅叙', job: '程序员'}
console.log(copyObj) // {name: '我不是小畅叙', job: '程序员'}

// 把obj作为目标对象，修改obj2会影响obj
let obj = { name: 'zoe' }
let obj2 = Object.assign(obj)
obj2.name = 'chao'
console.log(obj) // { name: 'chao' }
console.log(obj2) // { name: 'chao' }
```

## 4 深拷贝数组和对象

如果对象属性或数组元素中有引用类型数据，上面的方法都不行，如果改变这些引用类型数据，还是会导致原对象或者原数组改变。可以采用下面的方法实现完全的深拷贝：

#### (1) JSON 转换

使用 `JSON.parse(JSON.stringify())` 来拷贝数据，拷贝的数据里不能有函数，因为 `JSON.stringify` 中只能放入原生的 JS 数组或对象。

#### (2) 使用 lodash 库

```javascript
import _ from 'lodash'
_.cloneDeep(obj)
```

#### (3) 递归

**步骤1：** 检测数据类型

```javascript
// -1表示倒数第一个字符，slice截取字符串，包括end处的字符
console.log(Object.prototype.toString.call([1, 2]).slice(8, -1)) // Array

// 方法1
const checkType = target => {
  return Object.prototype.toString.call(target).slice(8, -1)
}

// 方法2
console.log([].constructor === Array) // true
```

**步骤2：** for...in...循环

循环对象，得到属性名。循环数组，得到下标。

**步骤3：** 实现深拷贝功能函数

```javascript
let arr = [{ number: 1 }, { number: 2 }, { number: 3 }]
function copy(obj) {
  let newobj = obj.constructor === Array ? [] : {}
  if (typeof obj !== 'object') {
    return obj
  }
  for (let i in obj) {
    newobj[i] = typeof obj[i] === 'object' ? copy(obj[i]) : obj[i]
  }
  return newobj
}
let copyArr = copy(arr)
copyArr[0].number = 100
console.log(arr) // [{number: 1}, { number: 2 }, { number: 3 }]
console.log(copyArr) // [{number: 100}, { number: 2 }, { number: 3 }]
```
