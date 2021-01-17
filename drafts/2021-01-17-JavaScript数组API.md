# JavaScript 数组 API

Array 原型上暴露了 3 个用于检索数组内容的方法：

1、`keys()`：返回数组索引的迭代器

2、`values()`：返回数组元素的迭代器

3、`entries()`：返回索引/值对的迭代器

```javascript
let a = ['foo', 'bar', 'baz', 'qux']
Array.from(a.keys()) // [ 0, 1, 2, 3 ]
Array.from(a.values()) // [ 'foo', 'bar', 'baz', 'qux' ]
Array.from(a.entries()) // [ [ 0, 'foo' ], [ 1, 'bar' ], [ 2, 'baz' ], [ 3, 'qux' ] ]
```

Array 构造函数上用于创建数组的静态方法：

1、`Array.from()` 用于将类数组结构转换为数组实例，第一个参数是一个类数组对象，即任何可迭代的结构，或者有一个 length 属性和可索引元素的结构，第二个可选参数是一个映射函数，第三个可选参数用于指定映射函数中 this 的值（在箭头函数中不适用）。

```javascript
let a = ['foo', 'bar', 'baz', 'qux']
let b = Array.from(a)
b.push('fuc') // 修改数组b不会影响数组a，所以是深拷贝
console.log(a) // [ 'foo', 'bar', 'baz', 'qux' ]
console.log(b) // [ 'foo', 'bar', 'baz', 'qux', 'fuc' ]
console.log(a === b) // false
```

2、`Array.of()` 可以把一组参数转换为数组，替代了在 ES6
之前常用的 `Array.prototype.slice.call(arguments)`。	