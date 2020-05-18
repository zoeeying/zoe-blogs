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

## 2 基本数据类型

#### (1) Number

Number 是数字类型，包括整数（int）和浮点数（float）。

需要注意的是，整数除以整数，如果使用单斜杠，那么结果是 float 类型；如果使用双斜杠，那么结果是 int 类型，前提是两个整数相除可以除尽：

```python
type(2/2) # <class 'float'>
type(2//2) # <class 'int'>
2//2 # 1
1//2 # 0
```

**补充：** 单斜杠是除法运算符，会把结果自动转换成 float 类型；双斜杠可以理解成整除，只会保留整数部分。









