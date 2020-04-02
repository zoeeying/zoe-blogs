# Lodash

Lodash 是一个 JavaScript 的实用工具库，表现一致性、[模块化](https://www.npmjs.com/browse/keyword/lodash-modularized)、高性能、以及[可扩展](http://lodash.think2011.net/#features)。

**使用：**`import _ from 'lodash'`



## 1 Object

### 1.1 get

根据对象路径获取值，返回解析的值 。

```javascript
// object：要检索的对象
// path：要获取的对象路径
// 如果解析value是undefined会以defaultValue取代
_.get(object, path, [defaultValue])
```



## 2 Lang

### 2.1 isEmpty

检查 `value` 是否是一个空的对象、collection、map 或 set。

如果为空返回 `true`，否则返回 `false` 。

```javascript
_.isEmpty(value)
```

