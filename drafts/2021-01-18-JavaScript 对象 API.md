# JavaScript 对象 API

## 1 对象属性的遍历

1、for...in... 遍历对象**自身**的和**继承**的**可枚举**属性，不含 Symbol 属性。

2、`Object.keys(obj)` 返回一个数组，包含对象**自身**的所有**可枚举**属性，不含 Symbol 属性和继承的属性。

3、`Object.getOwnPropertyNames(obj)` 返回一个数组，包含对象**自身**的所有属性，包含**不可枚举属性**，不含 Symbol 属性。

4、`Object.getOwnPropertySymbols(obj)` 返回一个数组，包含对象**自身**的**所有 Symbol 属性**。

5、`Refect.ownKeys(obj)` 返回一个数组，包含对象**自身的所有属性**，包含 **Symbol 属性**，包含**可枚举和不可枚举属性**。

## 2 Object 实例属性和方法

`hasOwnProperty(propertyName)`：判断当前对象**实例**（不是原型）上是否存在给定的属性，要检查的属性名必须是字符串，比如 `o.hasOwnProperty("name")`。

