# 前端易忘知识点1

## 1 把字符串中最后一个字符去掉

```javascript
let str = str.substr(0, str.length - 1)
```

## 2 去掉字符串中的空格

```javascript
let str = str.replace(/\s/g, '')
```

## 3 dva models 中使用 select 拿到 state

```javascript
// staff是namespace，data是models中的state
const temp = yield select(({ staff: { data } }) => data)
```

## 4 奇偶数判断

```javascript
num % 2 === 0 ? '偶数' : '奇数'
```

## 5 数组去重

```javascript
arr = [...new Set(arr)]
```

## 6 对象数组中两个字段排序

```javascript
// 排序，先按照名称排序再按照编号排序
// 直接修改原数组
arr.sort((a, b) => {
  const value1 = a.name
  const value2 = b.name
  if (value1 === value2) {
    return a.code.localeCompare(b.code)
  }
  return value1.localeCompare(value2)
})
```

## 7 react 中路由跳转传参

```javascript
router.push({
  pathname: './dictionaryStopItem',
  query: {
    dictionaryId: record.id,
    dictionaryName: record.name,
    dictionaryCode: record.code,
    dictionaryStatus: record.dataStatus,
  },
})
const { dictionaryId } = this.props.location.query
```

## 8 使用 ES6 交换两个变量的值

```javascript
var a = 1, 
    b = 2
a = [b, (b = a)][0]
console.log(a, b) // 2 1
```

## 9 交换数组中的元素

```javascript
// 交换第三个和第四个元素
// x < y
let arr = [1, 2, 3, 4, 5]
let x = 3,
    y = 4
arr.splice(x - 1, 1, ...arr.splice(y - 1, 1, arr[x - 1]))
console.log(arr) // [1,2,4,3,5]
```

## 10 moment 时间格式

```javascript
'YYYY-MM-DD HH:mm:ss'
```

## 11 找到满足条件的数组元素索引

```javascript
const index = dataSource.findIndex((item) => row.id === item.id)
```

## 12 一些正则

```javascript
^(([1-9][0-9]*)|(([0]\.\d{1,2}|[1-9][0-9]*\.\d{1,2})))$ // 允许保留小数点后面两位数
^[1-9]\d*$ // 匹配正整数
^-[1-9]\d*$ // 匹配负整数
^-?[1-9]\d*$ // 匹配整数
^[1-9]\d*|0$ // 匹配非负整数（正整数+0）
^-[1-9]\d*|0$ // 匹配非正整数（负整数+0）
^[1-9]\d*\.\d*|0\.\d*[1-9]\d*$ // 匹配正浮点数
^-([1-9]\d*\.\d*|0\.\d*[1-9]\d*)$ // 匹配负浮点数
^-?([1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0)$ // 匹配浮点数
^[1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0$ // 匹配非负浮点数（正浮点数+0）
^(-([1-9]\d*\.\d*|0\.\d*[1-9]\d*))|0?\.0+|0$ // 匹配非正浮点数（负浮点数+0）
```

+：元字符规定其前导字符必须在目标对象中**连续出现一次或多次**。

\*：元字符规定其前导字符必须在目标对象中**出现零次或连续多次**。

?：元字符规定其前导字符必须在目标对象中**连续出现零次或一次**。

## 13 使用 ES6 方法筛数组（函数式编程）

```javascript
const a1 = [{ id: 1 }, { id: 2 }, { id: 3 }, { id: 4 }]
const a2 = [{ id: 1 }, { id: 2 }]
const newArr = a1.filter((v) => !a2.some((a) => a.id === v.id))
console.log('newArr: ', newArr) // newArr: [{ id: 3 }, { id: 4 }]
```

## 14 antd 中 table row 背景颜色设置

```less
.checkedRow {
  td {
    background: #1890ff4d !important;
  }
  &:hover {
    td {
      background: #1890ff4d !important;
    }
  }
  cursor: pointer;
}

.pointer {
  cursor: pointer;
}
```

## 15 yekyll 项目启动

```bash
jekyll serve --incremental
```

## 16 Number.isFinite() 和 Number.isNaN()

它们与传统的全局方法 isFinite() 和 isNaN() 的区别在于，传统方法先调用 Number() 将非数值的值转为数值，再进行判断，而这两个方法只对数值有效，Number.isFinite() 对于非数值一律返回 false，Number.isNaN() 只有对于 NaN（是真的 NaN，或者表达式运算之后得到的 NaN）才返回 true，非 NaN 一律返回 false。

## 17 使用 momentJS 加减日期

```javascript
// 增加日期
moment().add(Number, String)
moment().add(Object)

// 减少日期
moment().subtract(Number, String)
moment().subtract(Object)

moment().add(7, 'd') // 增加7天
moment().add(7, 'days').add(1, 'months') // 链式操作，增加7天再增加1个月
moment().add({ days: 7, months: 1 }) // 增加7天再增加1个月
```

**类型列表（String）：**

- years（y）：年
- quarters（Q）：季度
- months（M）：月
- weeks（w）：周
- days（d）：日
- hours（h）：时
- minutes（m）：分
- seconds（s）：秒
- milliseconds（ms）：毫秒

## 18 dva 中的 connect 和 @connect

connect 的作用是将组件和 models 结合在一起，将 models 中的 state 绑定到组件的 props 中，并提供一些额外的功能，譬如 dispatch。

@connect 只是 connect 的装饰器、语法糖。

```javascript
import { connect } from 'dva'

// 将model和component串联起来
export default connect(({ user, login, global = {}, loading }) => ({
  currentUser: user.currentUser,
  collapsed: global.collapsed,
  fetchingNotices: loading.effects['global/fetchNotices'],
  notices: global.notices,
  menuData: login.menuData,
  redirectData: login.redirectData,
}))(BasicLayout)

// 改造一下，export的不再是connect，而是class组件本身，但是要注意@connect必须放在export default class前面
// 将model和component串联起来
@connect(({ user, login, global = {}, loading }) => ({
  currentUser: user.currentUser,
  collapsed: global.collapsed,
  fetchingNotices: loading.effects['global/fetchNotices'],
  notices: global.notices,
  menuData: login.menuData,
  redirectData: login.redirectData,
}))
export default class BasicLayout extends React.PureComponent {
  // ...
}
```

## 19 react 中使用两个高阶组件

```javascript
export default connect(({ areas, loading }) => ({
  areas,
  loading,
}))(Form.create({ name: 'add_areas' })(AddAreas))

@connect(({ common, loading }) => ({ common, loading }))
@Form.create()
```

## 20 antdesign Table 的 rowClassName

```less
.necessary_matter {
  td {
    background: rgba(173, 220, 201, 0.5) !important;
  }
  &:hover {
    td {
      background: rgba(173, 220, 201, 0.5) !important;
    }
  }
  cursor: pointer;
}
```

## 21 git 使用

```bash
git reset --hard ca3cc90 // 回滚版本
git push origin dev --force // 强推
```

## 22 在字符串某个位置插入字符

```javascript
let insertStr = (soure, start, newStr) => {
  return soure.slice(0, start) + newStr + soure.slice(start)
}
let testStr = '蚂蚁部落'
console.log(insertStr(testStr, 1, '奋斗'))
```

## 23 在树形数据中找到某个数据

```javascript
// 这个方法是错误的
const findMenuObj = (arr, key) => {
  let tempObj = {}
  for (let i = 0; i < arr.length; i++) {
    if (arr[i].key === key) {
      tempObj = arr[i]
    } else if (arr[i].chilren) {
      findMenuObj(arr[i].children, key)
    }
  }
  return tempObj
}

// 这个方法也是错误的
const findMenuObj = (key, items) => {
  for (const i in items) {
    const item = items[i]
    if (item.key == key) {
      return item
    }
    if (item.children) {
      return findMenuObj(key, item.children)
    }
  }
}

let findObj = {}
function findMenuObj(key, items) {
  if (!items) {
    return
  }
  items.forEach((item) => {
    if (item.key === key) {
      findObj = item
    }
    findMenuObj(key, item.children)
  })
}

// antd中的方法
const getParentKey = (key, tree) => {
  let parentKey
  for (let i = 0; i < tree.length; i++) {
    const node = tree[i]
    if (node.nodes) {
      if (node.nodes.some((item) => item.key === key)) {
        parentKey = node.key
      } else if (getParentKey(key, node.nodes)) {
        parentKey = getParentKey(key, node.nodes)
      }
    }
  }
  return parentKey
}
```

## 24 在 UmiJS 中模拟接口和模拟数据

```js
let inverterList = Mock.mock({
  // 属性list的值是一个数组，其中含有1到10个元素
  'data|1-30': [
    {
      // 属性id是一个自增数，起始值为1，每次增1
      'id|+1': 1,
      forShort: '@ctitle(5, 8)',
      'numberCode|1': ['1A', '1B', '2A', '2B'],
      'ratio|100-110': 101,
      'mix|1-10': 2,
      'cover|1-10': 3,
      'angle|1-10': 4,
      'attenuation|1-10': 5,
      'bracket|1-10': 1,
      'self|1-10': 8,
      'isPost|0-1': 1,
      update: '@date("yyyy-MM-dd")',
    },
  ],
})
inverterList = {
  ...inverterList,
  startRow: 1,
  totalCount: 21,
  success: true,
}

export default {
  '/fetchInverterList': (req, res) => {
    res.send(inverterList)
  },
}
```

## 25 ES6 中数组的 reduce 方法

reduce 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素，接受四个参数：初始值（或者上一次回调函数的返回值），当前元素值，当前索引，调用 reduce 的数组。

```js
array.reduce(function(total, currentValue, currentIndex, arr){}, initialValue)
```

**注意：**提供初始值通常更安全，不然要么会报错，要么会从索引 1 开始执行 callback 方法。

- 数组求和

```js
let total = [0, 1, 2, 3].reduce((acc, cur) => acc + cur, 0)
console.log(total) // 6
```

- 数组中最大值

```js
let max = arr.reduce((prev, cur) => {
  return Math.max(prev, cur)
}, 0)
```

- 数组去重

```js
const arr = [1, 2, 3, 3, 4, 4, 2, 1, 5]
const newArr = arr.reduce((prev, cur, index, curarr) => {
  if (prev.indexOf(cur) === -1) {
    prev.push(cur)
  }
  return prev
}, [])
console.log(newArr) // [1,2,3,4,5]

const dedupe = (array) => Array.from(new Set(array))
console.log(dedupe([1, 1, 2, 3])) // [1,2,3]

console.log([...new Set([1, 2, 3, 4, 5, 2, 4, 1])])
```

- 二维数组转换成一维数组

```js
var array = [
  [1, 2],
  [3, 4],
  [5, 6],
].reduce((acc, cur) => {
  return acc.concat(cur)
}, [])
console.log(array) // [0,1,3,4,5,6]
```

- 数组中某个元素出现的次数

```js
const array = (arr, val) =>
  arr.reduce((acc, cur) => (cur === val ? acc + 1 : acc + 0), 0)
const arr = [0, 1, 3, 0, 2, 0, 2, 3]
console.log(array(arr, 0)) // 数组arr中0元素出现的次数为3
```

## 26 Number.isNaN 和 isNaN 的区别

NaN 是一个放在 global（浏览器里是 window）对象里的一个属性，是一个代表 Not-A-Number 的 value，所以 NaN 是一个 value，这个 value 的 type 是 number。

**Number.isNaN：** 判断传入的参数是否严格等于（===） NaN。当两个变量进行运算时，我们可以使用 Number.isNaN 来判断它的值是否为 NaN。

**isNaN：** 当我们向 isNaN 传递一个参数，它的本意是通过 Number 方法尝试将参数转换成 Number 类型，如果成功返回 false，如果失败返回 true。所以 isNaN 只是判断传入的参数是否能转换成数字，并不是严格的判断是否等于 NaN。

## 27 异步写法

```javascript
const fetchData = async () => {
  let companys = []
  let tags = []
  await dispatch({
    type: 'queryStaff/fetchCompanyListPermission',
  }).then((res) => {
    companys = res
  })
  await dispatch({
    type: 'queryStaff/fetchQueryStaffTags',
    payload: {
      business: 'queryStaff',
    },
  }).then((res) => {
    tags = res
  })
}
fetchData()
```

## 28 React 中的 ref

Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。

Refs 是使用 `React.createRef()` 创建的，并通过 `ref` 属性附加到 React 元素上。在构造组件时，通常将 Refs 分配给实例属性，以便可以在整个组件中引用它们。

不能在函数组件上使用 `ref` 属性，因为它们没有实例。

```jsx
<input type="text" ref={(input) => (this.textInput = input)} />
// this.textInput可以获取到该dom元素
```

## 29 在字符串指定位置插入其它字符串

```js
// source是原字符串，name是要插入的字符串，place是原字符串的位置
let newStr = source.slice(0, place) + name + source.slice(place)
```

## 30 React Hooks 中自适应高度

```jsx
const [height, setHeight] = useState(
  () => document.documentElement.clientHeight
)
const handleHeight = () => {
  setHeight(document.documentElement.clientHeight)
}
useEffect(() => {
  window.addEventListener('resize', handleHeight)
  return () => {
    window.removeEventListener('resize', handleHeight)
  }
}, [])
```

## 31 给 this.setState 传入函数

```react
this.setState(prevState => ({ count: prevState.count + 1 }))
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}))
```

## 32 React 中引入图片

```react
import imgURL from './images/avatar.jpg'
<img src={imgURL} alt="" />
```

```react
<img src={require('./images/avatar.jpg')} alt="" />
```
