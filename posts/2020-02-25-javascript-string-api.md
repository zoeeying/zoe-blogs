# JavaScript 字符串 API

## 1 字符方法

字符表示字符串中某一个字符。

| 方法                | 描述                                               |
| ------------------- | -------------------------------------------------- |
| `charAt(index)`     | 接收一个位置索引，返回字符串中特点位置的字符       |
| `charCodeAt(index)` | 接收一个位置索引，返回字符串中特点位置的字符的编码 |
| `str[index]`        | 使用方括号加数字索引来访问字符串中特定位置的字符   |

```javascript
let str = 'hello zoe'
console.log(str.charAt(1)) // e
console.log(str.charCodeAt(1)) // 101，字符e的字符编码
console.log(str[1]) // 2
```

## 2 fromCharCode

fromCharCode 方法是 String 构造函数中的一个静态方法，这个方法用于接收一个或多个字符编码，将其转换成一个字符串。与 charCodeAt 执行的是相反的操作。

```javascript
console.log(String.fromCharCode(104, 101, 108, 108, 111)) // hello
```

## 3 字符串拼接

`concat` 是专门用来拼接字符串的方法，但实际开发中，都使用**加号操作符**来做字符串拼接。

## 4 截取字符串

下面是工作中特别常用的三个**截取字符串方法**。这三个方法会返回截取出来的子字符串；不会修改原字符串；接收一个或两个参数；第一个参数都是字符串的开始位置；如果没有传递第二个参数，会截取并返回从开始位置到结束位置的子字符串。

#### (1) slice()

第二个参数是结束位置；第一个参数是负数，转换为字符串的长度加上该负数；第二个参数是负数，同样转换为字符串的长度加上该负数。

#### (2) substr()

第二个参数是要截取的字符串长度；第一个参数是负数，转换为字符串的长度加上该负数；第二个参数是负数，转换为 0。

#### (3) substring()

第二个参数是结束位置；第一个参数是负数，转换为 0；第二个参数是负数，同样转换为 0。这个方法还有一个怪异的地方，如果传递了两个参数（如果参数有负数，会先把负数转换为 0），会比较这两个参数，把较小的参数作为开始位置。

```javascript
let str = 'hello world'
console.log(str.slice(-3)) // rld
console.log(str.substr(-3)) // rld
console.log(str.substring(-3)) // hello world
console.log(str.slice(3, -4)) // lo w
console.log(str.substr(3, -4)) // 空字符串
console.log(str.substring(3, -4)) // hel
```

## 5 查找字符串位置

下面两个方法都是从一个字符串中查找给定的子字符串**第一次出现**的位置，如果没有找到就返回 -1。我们还可以给这两个方法指定第二个参数，表示从这个位置往后（前）查找。

本人在解 LeetCode 题时，经常使用这两个方法。如果某个字符在字符串中的 indexOf 位置和 lastIndexOf 位置不一样，那么该字符在字符串中肯定不止一个；如果位置一样，那么该字符在字符串中是唯一的。

| 方法            | 描述                 |
| --------------- | -------------------- |
| `indexOf()`     | 从字符串开头向后查找 |
| `lastIndexOf()` | 从字符串结尾向前查找 |

我们可以通过循环调用这两个方法，从一个字符串中查找出给定的子字符串的所有位置。

```javascript
let str = 'though the future remains unknown, may our good luck last, may our past be past'
let childStr = 't'
let posArr = []
let pos = str.indexOf(childStr)
while (pos > -1) {
  posArr.push(pos)
  pos = str.indexOf(childStr, pos + 1)
}
console.log(posArr)
```

## 6 掐头去尾

| 方法          | 描述                                                     |
| ------------- | -------------------------------------------------------- |
| `trim()`      | 把给定字符串的前后所有空格去掉然后返回，不会修改原字符串 |
| `trimLeft()`  | 用于去掉字符串开头的所有空格，该方法非标准               |
| `trimRight()` | 用于去掉字符串末尾的所有空格，该方法非标准               |

```javascript
var str = '   hello zoe   '
console.log(str.trim()) // hello zoe
console.log(str.trimLeft() + 'aa') // hello zoe   aa
console.log('aa' + str.trimRight()) // aa   hello zoe
```

## 7 大小写转换

| 方法                  | 描述                                 |
| --------------------- | ------------------------------------ |
| `toLowerCase()`       | 把给定字符串转换为小写               |
| `toUpperCase()`       | 把给定字符串转换为大写               |
| `toLocaleLowerCase()` | 针对特定地区，把给定字符串转换为小写 |
| `toLocaleUpperCase()` | 针对特定地区，把给定字符串转换为大写 |

## 8 模式匹配方法

#### (1) match

只接收一个参数，由字符串或 RegExp 对象指定的一个正则表达式。

```javascript
const string = 'cat, bat, sat, fat'
const pattern = /.at/
// 如果正则后面不加g，那么string.match(pattern)与pattern.exec(string)相同，返回一个数组
// 如果加了g，string.match(pattern)会把匹配到的所有子字符串组合成一个数组返回，而pattern.exec(string)方法返回的与不加g是一样的
console.log(string.match(pattern))
```

#### (2) search

只接收一个参数，由字符串或 RegExp 对象指定的一个正则表达式，从字符串开头向后查找，返回字符串中第一个匹配到的子字符串的索引，如果没有找到匹配项，就返回 -1。

```javascript
const string = 'cat, bat, sat, fat'
console.log(string.search(/at/)) // 1
```

#### (3) replace

接收两个参数，第一个参数是一个 RegExp 对象或者一个字符串（跟 match 和 search 方法中传递的字符串不同，不会被转换成正则表达式），第二个参数是一个字符串或者一个函数。

```javascript
const string = 'cat, bat, sat, fat'
console.log(string.replace('at', 'ond')) // 只会替换匹配到的第一个子字符串
console.log(string.replace(/at/g, 'ond')) // 替换匹配到的所有子字符串，第一个参数必须是一个带g的正则表达式
```

如果第二个参数是字符串，那么可以使用一些特殊的**字符序列**，将正则表达式操作得到的值插入到结果字符串中。这里不赘述了，因为几乎用不到。

如果第二个参数是函数，我们可以使用函数中的参数，实现更加精细的替换操作，这个函数应该返回一个字符串，用于替换被匹配到的子字符串。

```javascript
const htmlEscape = str =>
  str.replace(/[<>"&]/g, (match, pos, origin) => {
    // 如果匹配到多个子字符串，那么每个匹配项都会执行一下这个函数
    switch (match) {
      case '<':
        return '&lt;'
      case '>':
        return '&gt;'
      case '&':
        return '&amp;'
      case '"':
        return '&quot;'
    }
  })
console.log(htmlEscape('<p class="greeting">Hello Zoe</p>'))
// &lt;p class=&quot;greeting&quot;&gt;Hello Zoe&lt;/p&gt;
```

#### (4) split

基于指定的分隔符将一个字符串分割成多个子字符串，并将结果放在一个数组中并返回。该方法接收两个参数，第一个参数是分割符字符串（或者 RegExp 对象，这种情况比较怪异，少用），第二个参数可选，用于指定返回的数组大小。

## 9 padStart 和 padEnd

如果某个字符串不够指定长度，会在头部（padStart）或尾部（padEnd）补全。

padStart 和 padEnd 接收两个参数，第一个参数是用来指定字符串的**最小长度**，第二个参数是用来补全的**字符串**。

如果**原字符串**的长度等于或大于指定的**最小长度**，则**返回原字符串**。

如果省略第二个参数，则会用**空格**来补全。

如果用来补全的字符串与原字符串的长度之后**超过了**指定的**最小长度**，则会把**用来补全的字符串**的超出部分截去。

如果原字符串和用来补全的字符串的长度之后**小于**指定的**最小长度**，则用来补全的字符串会重复自身，以使得最终的结果字符串的长度等于指定的最小长度。

```javascript
console.log('xx'.padEnd(2, 'abc')) // 'xx'
console.log('x'.padStart(4)) // '   x'
console.log('abc'.padStart(10, '0123456789')) // '0123456abc'
console.log('12'.padStart(10, 'YYYY-MM-DD')) // 'YYYY-MM-12'
console.log('x'.padStart(5, 'abc')) // 'abcax'
```

## 10 其它

| 方法              | 描述                                        |
| ----------------- | ------------------------------------------- |
| `includes(str)`   | 判断是否包含指定的字符串，返回 `true/false` |
| `startsWith(str)` | 判断是否以指定字符串开头，返回 `true/false` |
| `endsWith(str)`   | 判断是否以指定字符串结尾，返回 `true/false` |
| `repeat(count)`   | 重复指定次数，返回重复后的新的字符串        |

localeCompare 方法用于比较两个字符串，并返回 -1、0、1。注意，对于英语而言，大写字母在字母表中排在小写字母前面。当然这个方法对于不同地区的语言是有差别的。

```javascript
const string = 'yellow'
console.log(string.localeCompare('brick')) // 1，brick在字母表中排在yellow之前
console.log(string.localeCompare('yellow')) // 0
console.log(string.localeCompare('zoo')) // -1，zoo在字母表中排在yellow之后
```





