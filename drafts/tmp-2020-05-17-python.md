# Python 入门

## 1 概述

Python 是一门编程语言，是众多编程语言中的一种，它的特点包括开源、面向对象、语法简洁优雅、易于学习、编写的程序容易阅读等。Python 是一个跨平台的语言，可以运行在 Windows、Linux 和 MacOS 操作系统上。Python 具有极为强大而丰富的标准库与第三方库，比如电子邮件、图形 GUI 界面。

相对于 C、C++、Java，Python 的运行效率较慢。

**Python 用途：** 爬虫、大数据与数据分析（Spark）、自动化运维与自动化测试、Web 开发（Flask、Django）、机器学习（Tensor Flow 深度学习框架）、胶水语言（混合如 C++、Java 等语言来一起编程，可以把其它语言编写的各种模块很轻松地联结在一起）。

> **Python 之禅：**
>
> 1、Simple is better than complex.
>
> 2、Now is better than never. Although never is often better than right now.

## 2 进制转换

Python 中通过在数字前面加上 **0b** 来表示该数字是**二进制**的数字，通过在数字前面加上 **0o** 来表示该数字是**八进制**的数字，通过在数字前面加上 **0x** 来表示该数字是**十六进制**的数字。

#### (1) 其它进制转二进制

其它进制转二进制，使用 bin 方法：

```python
bin(10) # => 0b1010
bin(0o7) # => 0b111
bin(0xE) # => 0b1110
```

#### (2) 其它进制转十进制

其它进制转二进制，使用 int 方法：

```python
int(0b111) # => 7
int(0o77) # => 63
```

#### (3) 其它进制转八进制

其它进制转八进制，使用 oct 方法：

```python
oct(0b111) # => 0o7
oct(0x777) # => 0o3567
```

#### (4) 其它进制转十六进制

其它进制转十六进制，使用 hex 方法：

```python
hex(888) # => 0x378
hex(0o7777) # => 0xfff
```

**补充：** 十进制转二进制，采用**短除法**；二进制转十进制，采用**权相加法**。

## 3 基本数据类型

#### (1) 数字

数字类型包括整数（int）、浮点数（float）、布尔类型（bool）和复数（complex）。

需要注意的是，整数除以整数，如果使用单斜杠，那么结果是 float 类型；如果使用双斜杠，那么结果是 int 类型，前提是两个整数相除可以除尽：

```python
type(2/2) # => <class 'float'>
type(2//2) # => <class 'int'>
2//2 # => 1
1//2 # => 0
```

**补充：** 单斜杠是除法运算符，会把结果自动转换成 float 类型；双斜杠可以理解成整除，只会保留整数部分；True 和 False 首字母都要大写，bool 是数字类型的子类；复数通过小写字母 j 来表示，比如 36j 就是一个复数。

```python
int(True) # => 1
int(False) # => 0
bool(1) # => True
# 非0的数值通过bool函数转换后都是True
bool(0) # => False
# 其它类型的数据也可以通过bool函数转换成布尔类型
bool('zoe') # => True
bool('') # => False
bool([]) # => False
bool({}) # => False
bool(None) # => False
```

#### (2) 字符串/str

```python
print(r'c:\northwind\northwest') # => c:\northwind\northwest
print('c:\\northwind\\northwest') # => c:\northwind\northwest
print('hello\
world') # => helloworld
```

当一个字符串前面加上 r/R，表示该字符串不是一个普通字符串，而是一个原始字符串。

字符串运算如下：

```python
'hello' * 3 # => 'hellohellohello'
'hello world'[-1] # => 'd'
'hello world'[0:5] # => 'hello'
'hello world'[0:-1] # => 'hello worl'
'hello world'[6:] # => 'world'
'hello ruby'[-4:] # => 'ruby'
```

#### (3) 列表/list

表示组（序列）的方式有很多种，列表是其中一种。

```python
type([1,2,3,4,5,6]) # => <class 'list'>
```

列表用 `[]` 来定义，列表中的元素可以是各种类型的数据，可以混合出现，且列表中可以嵌套列表。

```python
[0,1,2,3][0:2] # => [0,1]
[0,1,2,3][-1:] # => [3]
[0,1,2,3]+[4,5] # => [0,1,2,3,4,5]
[0,1]*3 # => [0,1,0,1,0,1]
```

#### (4) 元祖/tuple

元祖用 `()` 来定义，用 tuple 来标识。

元祖也是一种基本数据类型，是组的一种表示方式，元祖中的元素访问和基本操作同列表。

元祖中的元素可以是各种类型的数据，可以混合出现。

```python
type((1,2,3)) # => <class 'tuple'>
(1,2,3,4)[0] # => 1

# 注意，只有一个元素的元祖的数据类型并不是tuple
type((1)) # => <class 'int'>
type(('hello')) # => <class 'str'>
```

为什么 `type ((1))` 得到的结果是 `<class 'int'>`，而不是 `<class 'tuple'>` 呢？因为 `()` 在 Python 中也可以用来表示数学运算优先级符号，所以会先计算 `(1)` 得到 `1`，再用 type 函数去检查类型，所以 `type((1))` 等同于 `type(1)`。

如果我们想定义只有一个元素的元祖，可以通过在元素后面加上一个逗号来表示，即 `(1,)`。

可以用 `()` 表示一个空元祖。

```python
type (()) # => <class 'tuple'>
```

元祖和列表有什么区别呢？

#### (5) 集合/set

集合用 `{}` 来定义，它最重要的特征是**无序**和**不重复**。

```python
type({1,2,3,4,5}) # => <class 'set'>
# 获取一个集合的长度
len({1,2,3,4,5}) # => 5
1 in {1,2，3} # => True
1 not in {1,2，3} # => False
# 从集合1中剔除集合2中的元素，即求两个集合的差集
{1,2,3,4,5,6} - {3,4} # => {1,2,5,6}
# 找出两个集合中所共有的元素，即求两个集合的交集
{1,2,3,4,5,6} & {3,4} # => {3，4}
# 合并两个集合，并且去除重复元素，即求两个集合的合集（并集）
{1,2,3,4,5,6} | {3,4，7} # => {1,2,3,4,5,6,7} 
```

```python
type({}) # => <class 'dict'>
type(set()) # => <class 'set'>
```

`type({})` 的结果并不是 `<class 'set'>`，因此 `{}` 并不能用来定义空的集合，如果想定义空的集合，需要这样做：`set()`。

#### (6) 字典/dict

字典是一种集合类型，不是序列。

```python
type({'Q':'新月打击', 'W':'苍白之瀑', 'E':'月之降临', 'R':'月神冲刺'}) # => <class 'dict'>
{'Q':'新月打击', 'W':'苍白之瀑', 'E':'月之降临', 'R':'月神冲刺'}['Q'] # => '新月打击'

# 字典中的key可以是字符串，也可以是数字
{1:'新月打击', '1':'苍白之瀑', 'E':'月之降临', 'R':'月神冲刺'}
```

字典中的 value 可以是任意的 Python 数据类型；字典中的 key 必须是不可变类型（int、str、tuple）。

空的字典用 `{}` 来定义。

#### (7) 总结

在 Python 中，str、list、tuple 都是序列。

序列中的每个元素都会被分配一个序号，可以通过 `[序号]` 来访问序列中的元素。

序列可以使用 `+` 和 `*` 运算符。

```python
# 切片
[1,2,3,4,5][-1:] # => [5]
'hello world'[0:8:2] # => 'hlow'
```

```python
# 判断一个序列中是否包含某个元素
3 in [1,2,3,4,5] # => True
# 判断一个序列中是否不包含某个元素
3 not in [1,2,3,4,5] # => False
# 获取一个序列的长度，即一个序列中有几个元素
len('hello world') # => 11
# 求一个序列中最大的元素，min是用来求一个序列中最小的元素
max([1,2,3,4,5]) # => 5
# 如果是字符串的话，是根据字符编码（ascll码）来比较大小的
max('hello world') # => w
```

`in` 和 `not in` 是用来判断两个变量之间逻辑关系的**逻辑运算符（操作符）**。

`ord('w')` 方法用于将参数 `'w'` 转换成 ascll 码。

字符串和元祖是**不可变类型**。

![基本数据类型总结](https://wx2.sinaimg.cn/mw690/600a9336gy1ggbx0m52okj20po09hq5h.jpg)

## 4 变量

在 Python 中，变量就是一个名字。变量名一定要有意义。

**定义变量：** 通过赋值符号 `=` 把数据赋值给一个变量。

**变量名的命名规则：** 变量名只能包含字母、数字、下划线；变量名不能以数字开头；保留关键字不能用作变量名；变量名区分大小写。

Python 是动态类型语言，所以变量本身是没有类型的，比如，可以把字符串赋值给一个变量，也可以把列表再赋值给同一个变量。

```python
a = 'abc'
a = [1,2,3]
a = 1
a = (1,2,3)
```

**值类型：** 不可改变的，包括 str、int、tuple。

```python
'python'[0] = 'o' # 报错，因为字符串是不可改变的

a = (1,2,3)
a[0] = '1' # 报错，因为元祖是不可改变的，不可以修改元祖中的元素，也不可以追加、删除元素
```

**引用类型：** 可改变的，包括 list、set、dict。

**补充：** `id()` 方法用于显示某个变量在内存中的地址，一般用 16 进制来表示内存地址。

```python
a = (1,2,3,[1,2,4])
a[2] = '3' # 报错
a[3][2] = '4' # 可以修改成功！！！
```

## 5 运算符

```python
5 % 2 # 5除以2的余数
2 ** 2 # 2的2次方
```

1、算数运算符：`+、 -、 *、 /、 //、 %、 **`

2、赋值运算符：`=、 +=、 -=、 *=、 /=、 %=、 **=、 //=`

```python
b = 1
b += b >=1 # 先逻辑运算再赋值运算
print(b) # 2
```

3、关系运算符：`==、 !=、 >、 <、 >=、 <=`

```python
[1,2,3] > [1,2,4] # => False
(1,2,3) < (1,2,4) # => True
[1,2,3] == [1,2,3] # => True
```

4、逻辑运算符：`and、 or、 not`

```python
'a' and 'b' # => 'b'
'' and 'b' # => ''
'' or 'b' # => 'b'
'a' or 'b' # => 'a'
```

空的列表、集合、字典、元祖在做逻辑运算的时候，会被认为是 False。

5、成员运算符：`in、 not in`

```python
c = 'test'
c in {'test': 1} # => True
```

6、身份运算符：`is、 is not`

```python
# 如果两个变量取值相等，则is运算符返回True
d = 1
e = 1
d is e # => True

f = 1
g = 1.0
f == g # => True
f is g # => False

h = {1,2,3}
i = {1,3,2}
# 集合是无序的，所以h和i的值相等
h == i # => True
h is i # => False

j = (1,2,3)
k = (1,3,2)
j == k # => False
j is k # => False

a = 'hello'
type(a) == str # => True	
isinstance(a, str) # => True
# 判断a是否是int/float类型
isinstance(a, (int, float)) # => False
```

对象的三个特征：id（is 运算符）、value（== 运算符）、type（isinstance 方法）。

type 方法不可以判断变量的子类是否属于某种类型，而 isinstance 方法可以。

7、位运算符：`&（按位与）、 |（按位或）、 ^（按位异或）、 ~（按位取反）、 <<（左移动）、 >>（右移动）`，把数字转换成二进制数再进行运算

```python
a = 2
b = 3
a & b # => 2
a | b # => 3
```

## 6 表达式

表达式（expression）是运算符（operator）和操作数（operand）所构成的序列。

表达式中的运算符优先级如下：

![运算符优先级](https://wx3.sinaimg.cn/mw690/600a9336gy1ggqxw9hylsj20bm0gimxk.jpg)

**左结合：** 一般表达式的解析都是从左到右的。

## 7 补充

可以通过命令 `pip install pylint` 安装 pylint。pip 是 Python 自带的包管理工具。pylint 是语法检测工具，也可以使用 flake8 来检测语法。

Python 中，单行注释使用 `#`，多行注释使用 `'''`；单行注释快捷键是 `ctrl+/`，多行注释快捷键是 `alt+shift+a`。

## 8 流程控制语句

### 8.1 条件控制

条件控制语句用于解决选择性问题。

```python
mood = True
if mood:
    print('go to left')
else:
    print('go to right')
```

```python
a = 1
b = 2
c = 2
if a or b + 1 == c:
    print('真的') # 打印出真的
else:
    print('假的')
```

```python
account = 'zoe'
password = '123456'
print('please input account')
user_account = input()
print('please input password')
user_password = input()
if account == user_account and password == user_password:
    print('success')
else:
    print('fail')
```

```python
if condition:
    pass # 空语句/占位语句，用于保持代码结构的完整性
else:
    pass
```

```python
print('please input a')
a = input()
a = int(a)
if a == 1:
    print('apple')
elif a == 2:
    print('orange')
elif a == 3:
    print('banana')
else:
    print('others')
```

**补充：** input 方法用于在命令行收集数据。

### 8.2 循环控制

```python
counter = 1
while counter <= 10:
    print(counter)
    counter += 1
else:
    print('EOF')
```

while 循环一般用于不知道循环次数的情况，也常用于递归。

```python
a = [['apple', 'orange', 'banana', 'grape'], (1, 2, 3)]
for x in a:
    for y in x:
        # print(y, end=';') # 在一行打印出所有的元素，元素中间用';'分隔，end默认是'\n'
        print(y)
else:
    print('EOF')

for x in a:
    for y in x:
      	if y == 'orange':
          	break # 注意这里的break只是终止了内部的循环
        print(y)
else:
    print('EOF') # 会执行
```

```python
b = [1, 2, 3]
for x in b:
    print(x)
    if x == 2:
        break # for循环遇到break语句，会跳出整个循环（循环会立即终止），后面的else也不会执行
else:
    print('EOF')

for x in b:
    if x == 2:
        continue # for循环遇到continue语句，会跳过本次循环
    print(x)
```

```python
# 循环执行10次
for x in range(0, 10):
    print(x)
    
for x in range(0, 10, 2):
    print(x) # 会打印出0、2、4、6、8，range函数的第三个参数表示步长

for x in range(10, 0, -2):
    print(x)  # 会打印出10、8、6、4、2
```

```python
c = [1, 2, 3, 4, 5, 6, 7, 8]
for i in range(0, len(c), 2):
    print(c[i])
    
# 扩展学习一下序列的切片用法，第三个参数表示步长
d = c[0: len(c): 2]
print(d) # [1, 3, 5, 7]
```

for 循环一般用于遍历（循环）序列、集合、字典。

## 9 Python 项目的组织结构

**组织结构：** 包 => 模块 => 类 => 函数、变量

函数和变量其实并不属于组织结构，只是为了方便学习和理解。

`包名.模块名` 形成了一个路径，这个路径就叫做**命名空间**。

如何区分普通文件夹和包呢？普通文件夹中如果有特定的文件 `__init__.py`，那么 Python 会把该文件夹认为是一个包。

`__init__.py` 也是一个模块，`包名` 就代表这个模块，而无需用 `包名.__init__` 表示。













