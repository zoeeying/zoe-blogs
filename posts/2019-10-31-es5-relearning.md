# ES5 复习与回顾

## 1 严格模式

除了正常运行模式（混杂模式），ES5 添加了第二种运行模式**严格模式（strict mode）**。这种模式使得 JS 在更严格的语法条件下运行。

**目的：** 消除 JS 语法的一些不合理、不严谨之处，减少一些怪异行为；消除代码运行的一些不安全之处；为未来新版本的 JS 做好铺垫。  

**使用：** 在全局或函数的第一条语句定义为：`'use strict'`，如果浏览器不支持，只是解析为一条简单的语句，没有任何副作用。  

**规则：** 变量必须先用 var 关键字声明；禁止自定义的函数中的 this 指向 window；创建 eval 作用域；对象不能有重名的属性。

```javascript
// 禁止自定义的函数中的this指向window
function Person(name, age){
    this.name = name
    this.age = age
}
// 如果不new，直接调用，会使得this指向window，加上new的话，this会指向实例对象
// 使用严格模式的话，直接调用会报错。
Person('zoe', 27)  
```

```javascript
// 创建eval作用域
var str = 'A'
eval('var str = "B"; alert(srt)')
// eval函数会执行单引号中的JS代码
// 如果使用严格模式，单引号中的JS代码会有自己的作用域，不会污染全局
// 如果不使用严格模式，单引号中的str会覆盖全局的str
alert(str)
```

## 2 Object 扩展

#### (1) create

**语法：** `Object.create(prototype, [descriptors])`。

**作用：** 以指定的对象为原型创建新的对象，为新的对象指定新的属性，并对属性进行描述（用配置对象来描述）。

**补充：** Object 是对象上一层的构造函数。

```javascript
var obj1 = { username: 'zoe', age: 27 }
var obj2 = {}
obj2 = Object.create(obj1, {
  sex: {
    value: 'female', // 指定值
    writable: true, // 当前属性是否可诶修改，默认为false
    configurable: true, // 当前属性是否可被删除，默认为false
    enumerable: true, // 当前属性是否可被for..in...枚举，默认为false
  }
})
// obj2的原型指向obj1，同时为obj2扩展了属性sex
```

#### (2) defineProperties

**语法：** `Object.defineProperties(prototype, descriptors)`。

**作用：** 为指定对象定义扩展多个属性。

**get：** 用来获取当前属性值的回调函数。

**set：** 监听当前属性值改变的回调函数，并且实参即为修改后的值。

**存取器属性：** setter 用来存值，getter 用来取值。

```javascript
var obj = { firstName: 'ying', lastName: 'zhou' }
Object.defineProperties(obj, {
  fullName: {
    // 获取扩展属性的值
    // 当试图获取扩展属性值的时候，比如console.log(obj.fullName)，get方法自动调用
    get: function(){
      return this.firstName + ' ' + this.lastName
    },
    // 监听扩展属性，当试图修改扩展属性值的时候自动调用，会把修改的值作为实参注入到set函数
    // 通过修改原属性，达到修改值的目的
    set: function(data){
      var names = data.split(' ')
      this.firstName = names[0]
      this.lastName = names[1]
    }
  }
})
```

**补充：** 学习一下对象本身的两个方法。

```javascript
// get propertyName(){}用来得到当前属性值的回调函数
// set propertyName(){}用来监听当前属性值变化的回调函数
var obj = {
  firstName: 'chao',
  lastName: 'liu',
  get fullName(){
    return this.firstName + ' ' + this.lastName
  },
  set fullName(data){
    var names = data.split(' ')
    this.firstName = names[0]
    this.lastName = names[1]
  }
}
```

## 3 Array 扩展

```javascript
Array.prototype.indexOf(value) // 到值在数组中的第一个下标
Array.prototype.lastIndexOf(value) // 得到值在数组中的最后一个下标
Array.prototype.forEach((item, index)=>{}) // 遍历数组
Array.prototype.map((item, index)=>{}) // 遍历数组，返回一个新数组，新数组中的元素经过了加工
Array.prototype.filter((item, index)=>{}) // 遍历数组，过滤出一个新的子数组，子新数组中的元素满足return的条件
```

## 4 Function 扩展

**apply：** 传入两个参数，一个是作为函数上下文的对象，另外一个是作为函数参数所组成的数组，会立即调用函。

**call：** 第一个参数也是作为函数上下文的对象，但是后面传入的是一个参数列表，而不是单个数组，会立即调用函数。

**bind：** 将函数返回，不会立即调用，传参方式同 call。

```javascript
var obj = { username: 'kobe' }
function foo(data){
  console.log(this, data)
}
foo.call(obj, 33) // 传入函数的参数从第二个参数开始，依次传入
foo.apply(obj, [33]) // 第二个参数必须是数组，传入函数的参数必须放在数组中

// 绑定完this不会立即调用，而是将函数返回，传参方式同call
// 通常用它来指定回调函数的this
foo.bind(obj, 33)()

// 定时器中改变回调函数的this只能用bind，因为这是回调函数，不能立即执行
setTimeout(function(){
  console.log(this)
}.bind(obj), 1000)
```
