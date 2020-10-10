# React 中的 Refs

React 支持一个特殊的、可以附加到任何组件和 DOM 元素上的 ref 属性，该属性可以是 `React.createRef()` 创建的 Refs 对象、回调函数、字符串（废弃）。

默认情况下，是不能在函数组件上使用 ref 属性的，因为函数组件没有实例。

学习 React Refs 之前，我们需要知道，应该避免使用 Refs 来做任何可以通过声明式实现的事情，切忌过度使用 Refs，因为它是在典型数据流之外强制修改子组件，不符合 React 数据驱动的思想。

## 1 ref 属性类型

#### (1) 字符串

ref 属性可以是字符串。需要注意的是，string ref 已经被新版 React 废弃，不建议使用。

但是我们还是需要学习一下，如果老版本的 React 项目中使用到了，不至于一脸懵逼。

下面的例子，input 元素的 ref 属性是字符串 `"inputRef"`，在 App 组件内部，可以通过 `this.refs.inputRef` 获取到 input 这个 DOM 元素：

```

```

#### (2) `React.createRef()`

ref 属性可以是 `React.createRef()` 创建的 Refs 对象。通常，我们会把创建的 Refs 分配给组件的实例属性，这样在整个组件内部都可以引用到。

如果使用了 React Hooks，可以用 `useRef(null)` 代替 `React.createRef()`。

当 Refs 对象被传递给 render 中的元素的 ref 属性，可以通过 Refs 对象的 current 属性访问该元素：

```

```

当 ref 属性用于 HTML 元素，Refs 对象接收底层 DOM 元素作为其 **current 属性**。

当 ref 属性用于自定义的 class 组件，Refs 对象接收组件的挂载实例作为其 **current 属性**。

#### (3) 回调函数

当 ref 属性是一个**回调函数**时，此函数会根据元素的类型，接收底层 DOM 元素或者 class 实例作为其参数，然后在函数体中，我们可以对它们进行存储和访问。

下面通过一个例子，学习如何使用 ref 回调函数，把 DOM 元素存储到当前组件的实例属性中：

```

```

React 将在组件挂载时，调用 ref 回调函数并传入 DOM 元素或 class 实例，卸载时调用并传入 null。

## 2 Refs 转发

Refs 转发允许某些组件接收 ref，并将其向下传递（转发）给子组件，换句话说，可以像暴露自己的 ref 一样暴露子组件的 ref。

下面看一个例子：

1、通过 `React.createRef()` 创建 Refs 对象，赋值给变量 inputRef，并设置为 Children 的 ref 属性：

```

```

2、通过 `React.forwardRef` 内部函数的第二个参数 ref 接收父组件传递过来的 ref，并设置为 button 的 ref 属性：

```

```

3、这样在父组件中，我们就可以通过 `inputRef.current` 访问到 Children 组件中的 button 元素了。

## 3 Refs 转发替代方案

## 4 父组件调用子组件中的方法























