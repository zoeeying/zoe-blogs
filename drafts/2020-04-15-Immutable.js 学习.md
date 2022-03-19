# Immutable.js 学习
## 1 概念
Immutable Data 就是一旦创建，就不能再被更改的数据。对 Immutable 对象的任何修改或添加删除操作都会返回一个新的 Immutable 对象。  

Immutable 实现的原理是 Persistent Data Structure（持久化数据结构），也就是使用旧数据创建新数据时，要保证旧数据同时可用且不变。  

同时为了避免 deepCopy 把所有节点都复制一遍带来的性能损耗，Immutable 使用了 Structural Sharing（结构共享），即如果对象树中一个节点发生变化，只修改这个节点和受它影响的父节点，其它节点则进行共享。

## 2 几种数据类型

- List：有序索引集，类似 JS 中的 Array
- Map：无序索引集，类似 JS 中的 Object
- OrderedMap：有序的 Map，根据数据的 set() 进行排序
- Set：没有重复值的集合
- OrderedSet：有序的 Set，根据数据的 add 进行排序
- Stack：有序集合，支持使用 unshift() 和 shift() 添加和删除
- Record：一个用于生成 Record 实例的类，类似于 JS 的 Object，但是只接收特定字符串为 key，具有默认值
- Seq：序列，但是可能不能由具体的数据结构支持
- Collection：是构建所有数据结构的基类，不可以直接构建

## 3 常用API
* `fromJS() `
  将一个 JS 数据转换为 Immutable 数据，默认情况会将数组转换为List类型，将对象转换为Map类型，其余不做操作。  

```javascript
// 用法，value是要转变的数据，converter是要做的操作，第二个参数可不填
fromJS(value, converter)
```

* `toJS() `
  将一个 Immutable 数据转换为 JS 数据

```javascript
// 用法
value.toJS()
```

* `is()`
  对两个对象进行比较，和 JS 中对象的比较不同，在 JS 中比较两个对象比较的是地址，但是在 Immutable 中比较的是这个对象的 hashCode 和 valueOf，只要两个对象的 hashCode 相等，值就是相同的，避免了深度遍历，提高了性能。

```javascript
// 用法
is(map1, map2)
```

* List() 和 Map()
  用来创建一个新的 List 或 Map 对象

```javascript
import { Map, List, is } from 'immutable'
// List
List() // 空List
List([1, 2])

// Map
Map() // 空Map
Map({ a: '1', b: '2' })
```

* List.isList() 和 Map.isMap()
  判断一个数据结构是不是 List 或 Map 类型

```javascript
List.isList([]) // false
List.isList(List()) // true

Map.isMap({}) // false
Map.isMap(Map()) // true
```