# JavaScript 数组 API

Array 原型上暴露了 3 个用于遍历数组的方法，它们都返回一个遍历器对象。

1、`keys()`：返回数组索引的迭代器

2、`values()`：返回数组元素值的迭代器

3、`entries()`：返回索引/值对的迭代器

```javascript
let a = ['foo', 'bar', 'baz', 'qux']
Array.from(a.keys()) // [ 0, 1, 2, 3 ]
Array.from(a.values()) // [ 'foo', 'bar', 'baz', 'qux' ]
Array.from(a.entries()) // [ [ 0, 'foo' ], [ 1, 'bar' ], [ 2, 'baz' ], [ 3, 'qux' ] ]
```

## 2 Array.from()、Array.of()

`Array.from()` 用于将**类数组对象**和**可遍历对象**（包括 Set 和 Map）转为真正的数组。第一个参数是要转换的对象，第二个可选参数是一个映射函数，第三个可选参数用于指定映射函数中 this 的值（在箭头函数中不适用）。

```javascript
let a = ['foo', 'bar', 'baz', 'qux']
let b = Array.from(a)
b.push('fuc') // 修改数组b不会影响数组a，所以是深拷贝
console.log(a) // [ 'foo', 'bar', 'baz', 'qux' ]
console.log(b) // [ 'foo', 'bar', 'baz', 'qux', 'fuc' ]
console.log(a === b) // false
```

`Array.of()` 用于把一组参数转换为数组，替代了 `Array.prototype.slice.call(arguments)`。