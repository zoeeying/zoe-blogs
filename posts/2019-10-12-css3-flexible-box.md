---
title: CSS3 弹性盒子
date: 2019-10-12
---

# CSS3 弹性盒子

弹性盒子是 CSS3 的一种新的布局模式。

CSS3 弹性盒（flexible box 或 flexbox），是一种当页面需要适应不同的屏幕大小以及设备类型时确保元素拥有恰当的行为的布局方式。

引入弹性盒布局模型的目的是提供一种更加有效的方式来对一个容器中的子元素进行排列、对齐和分配空白空间。

## 1 概览

弹性盒子由弹性容器（flex container）和弹性子元素（flex item）组成。弹性容器通过设置 display 属性的值为 flex 或 inline-flex 将其定义为弹性容器，这时，子元素的 float、clear 和 vertical-align 属性将失效。弹性容器内包含了一个或多个弹性子元素。

**注意：** 弹性容器外及弹性子元素内是正常渲染的。弹性盒子只定义了弹性子元素如何在弹性容器内布局。
弹性子元素通常在弹性盒子内一行显示。默认情况每个容器只有一行。

**注意：** 如果我们设置 direction 属性为 rtl（right-to-left），弹性子元素的排列方式是从右往左；direction 属性为 ltr（left-to-right），弹性子元素的排列方式是从左往右。

## 2 容器属性

#### (1) flex-direction

flex-direction 属性指定了弹性子元素在父容器中的位置。如果元素不是弹性盒对象的元素，则 flex-direction 属性不起作用。

```css
.flex-container { flex-direction: row | row-reverse | column | column-reverse; }
```

| 属性值         | 用途                                                 |
| -------------- | ---------------------------------------------------- |
| row            | 横向从左到右排列（左对齐），默认的排列方式           |
| row-reverse    | 反转横向排列（右对齐），从后往前排，最后一项排在最前 |
| column         | 纵向排列                                             |
| column-reverse | 反转纵向排列，从后往前排，最后一项排在最上面         |

#### (2) flex-wrap

flex-wrap 属性用于指定弹性盒子的子元素换行方式。规定 flex 容器是单行或者多行，同时横轴的方向决定了新行堆叠的方向。

```css
.flex-container { flex-wrap: nowrap | wrap | wrap-reverse; }
```

#### (3) flex-flow

flex-flow 属性是 flex-direction 属性和 flex-wrap 属性的简写形式，默认值为 row nowrap。

```css
.flex-container { flex-flow: <flex-direction> <flex-wrap>; }
```

#### (4) align-items

align-items 设置或检索弹性盒子元素在侧轴（纵轴）方向上的对齐方式。  

```css
.flex-container { align-items: flex-start | flex-end | center | baseline | stretch; }
```

#### (5) justify-content

justify-content 用于设置或检索弹性盒子元素在主轴（横轴）方向上的对齐方式。

```css
.flex-container { justify-content: flex-start | flex-end | center | space-between | space-around; }
```

## 3 子元素属性

#### (1) order

设置弹性盒子的子元素排列顺序。

```css
.flex-container .flex-item { order: <integer>; }  
```

**integer：** 用整数值来定义排列顺序，数值小的排在前面。可以为负值，默认为 0。

#### (2) flex-grow

```css
.flex-container .flex-item { flex-grow: <integer>; }
```

**integer：** 一个数字，规定项目将相对于其他灵活的项目进行扩展的量。默认值是 0。

#### (3) flex-shrink

```css
.flex-container .flex-item { flex-shrink: <integer>; }
```

**integer：** 一个数字，规定项目将相对于其他灵活的项目进行收缩的量。默认值是 1。

#### (4) flex-basis

```css
.flex-container .flex-item { flex-basis: <integer> | auto; }
```

**integer：** 一个长度单位或者一个百分比，规定元素的初始长度。
**auto：** 默认值。长度等于元素的长度。如果该项目未指定长度，则长度将根据内容决定。

#### (5) flex

flex 属性用于设置或检索弹性盒模型对象的子元素如何分配空间。
flex 属性是 flex-grow、flex-shrink 和 flex-basis 属性的简写属性。

```css
.flex-container .flex-item { flex: flex-grow flex-shrink flex-basis | auto | initial | inherit; }
```

#### (6) align-self

align-self 属性用于设置弹性元素自身在侧轴（纵轴）方向上的对齐方式。

```css
.flex-container .flex-item { align-self: auto | stretch | center | flex-start | flex-end | baseline | initial | inherit; }
```

## 4 块元素和行内元素的垂直居中

**块元素垂直居中**问题采用 flex 解决如下：

弹性子元素设置 `margin: auto;` 就可以使其在弹性容器的两轴方向上都完全居中（必须设置高度和宽度，如果只是设置了高度，就只是垂直居中）。

**行内元素垂直居中**问题解决如下：

**单行：** 该元素 CSS 属性 line-height 的值与该元素的父级元素 CSS 属性 height 一致即可。

**多行：** 设置该元素 CSS 属性：`display: table-cell; vertical-align: middle;`
还需设置该元素的父级元素 CSS 属性：`display: table;`
