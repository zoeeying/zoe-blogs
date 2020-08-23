---
title: React 生命周期
date: 2020-02-24
---

# React 生命周期

React 生命周期可以用如下两个图来示意。

![React生命周期](https://wx4.sinaimg.cn/mw690/600a9336gy1gdsbdkb8whj20o90dlt9d.jpg)

生命周期函数（钩子函数），通俗的说就是在某一时刻会被自动调用执行的函数。

## 1 挂载

当组件实例被创建并插入 DOM 中时，其生命周期函数执行顺序如下：

* `constructor()`
* `static getDerivedStateFromProps()`
* `render()`
* `componentDidMount()`

#### (1) constructor

如果不初始化 state 或不进行方法绑定，则不需要为 React 组件**实现**构造函数。

在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，应在其他语句之前调用 `super(props)`，否则，`this.props` 在构造函数中可能会出现未定义的 bug。

通常，在 React 中，构造函数一般用于以下两种情况：初始化内部 state（为 this.state 赋值一个对象）；为事件处理函数绑定实例。

要避免在构造函数中引入任何副作用或订阅。

**注意：** 避免将 props 的值复制给 state！这是一个常见的错误！只有在刻意忽略 props 更新的情况下使用。此时，应将 props 重命名为 `initialColor` 或 `defaultColor`。

```javascript
constructor(props) {
 super(props)
 // 不要这样做
 this.state = { color: props.color }
}
```

#### (2) getDerivedStateFromProps

```jsx
static getDerivedStateFromProps(props, state)
```

生命周期函数 `componentWillReceiveProps` 是响应 props 变化之后进行更新的方式，但是在16.3 版本中，使用了一个替代版的生命周期函数 `getDerivedStateFromProps`。`getDerivedStateFromProps` 的存在只有一个目的：让组件在 **props 变化**时更新 state。

在每次 re-rendering 前都会触发 getDerivedStateFromProps 方法，componentWillReceiveProps 仅在父组件重新渲染时触发，而不是在内部调用 `setState` 时。

`getDerivedStateFromProps` 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。

此方法无权访问组件实例。

```jsx
static getDerivedStateFromProps(props, state) {
  // 当传入的type发生变化的时候，更新state
  if (props.type !== state.type) {
    return {
      type,
    }
  }
  // 否则返回null，表示props传入的内容会影响到state，即对于state不进行任何操作
  // 这个返回值是必须的，所以尽量将其写在函数的末尾
  return null
}
```

#### (3) render

render 方法是 class 组件中唯一必须实现的方法。

当 render 被调用时，它会检查 this.props 和 this.state 的变化并**返回**以下类型之一：

* React 元素，通常通过 JSX 创建，包括自定义组件和 DOM 标签
* 数组或 fragments，使得 render 方法可以返回多个元素
* 字符串或数值类型，在 DOM 中会被渲染为文本节点
* 布尔类型或 `null`，什么都不渲染
* Portals，可以渲染子节点到不同的 DOM 子树中

render 函数应该为纯函数，这意味着在不修改组件 state 的情况下，每次调用时都返回相同的结果，并且它不会直接与浏览器交互。如需与浏览器进行交互，需要在 `componentDidMount()` 或其他生命周期方法中执行操作。保持 render 为纯函数，可以使组件更容易思考。

#### (4) componentDidMount

`componentDidMount()` 会在组件挂载后（插入 DOM 树中）立即调用。依赖于 DOM 节点的初始化应该放在这里，如需通过网络请求获取数据，此处是实例化请求和比较适合添加订阅（不要忘记在 `componentWillUnmount()` 里取消订阅）的地方。

在 componentDidMount 中可以 setState 方法，它将触发额外渲染，但此渲染会发生在浏览器更新屏幕之前（如此保证了即使在 `render()` 两次调用的情况下，用户也不会看到中间状态）。不建议在这个生命周期函数中使用 setState，会导致性能问题。

**问题：那如果我想把请求到的后台数据作为初始化 state 呢？**

## 2 更新

当组件的 props 或 state 发生变化时会触发更新，组件更新的生命周期函数执行顺序如下：

* `static getDerivedStateFromProps()`
* `shouldComponentUpdate()`
* `render()`
* `getSnapshotBeforeUpdate()`
* `componentDidUpdate()`

注意以上的执行顺序中，只要 shouldComponentUpdate 返回 false，后面的生命周期函数就都不会执行了。

#### (1) shouldComponentUpdate

根据 `shouldComponentUpdate()` 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。

首次渲染或使用 `forceUpdate()` 时不会调用该方法。

`shouldComponentUpdate(nextProps, nextState)` 会在组件每次更新之前触发，是更新生命周期的门卫，是可以取消更新操作的谓词（必须返回布尔值，返回 true  才会更新组件）。

实际开发中，父组件的 render 执行的时候，子组件的 render 也会执行，这会导致一些性能问题，可以在子组件的 shouldComponentUpdate 中判断新旧 state/props 是否相同，相同则返回 false（阻止不必要的更新），从而优化性能。

#### (2) getSnapshotBeforeUpdate

`getSnapshotBeforeUpdate(prevProps, prevState)` 在最近一次渲染输出（提交到 DOM 节点）之前调用，它使得组件能在发生更改之前从 DOM 中捕获一些信息（比如滚动位置）。此生命周期的任何返回值（应返回 snapshot 的值或 null）将作为参数传递给 `componentDidUpdate()`。

#### (3) componentDidUpdate

`componentDidUpdate(prevProps, prevState, snapshot)` 会在更新后会被立即调用，首次渲染不会执行此方法。

当组件更新后，可以在此处对 DOM 进行操作。如果对更新前后的 props 进行了比较，也可以选择在此处进行网络请求。

```jsx
componentDidUpdate(prevProps) {
  // 典型用法，不要忘记比较props
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID)
  }
}
```

可以在 `componentDidUpdate()` 中**直接调用 `setState()`**，但注意**它必须被包裹在一个条件语句里**，否则会导致死循环。它还会导致额外的重新渲染，虽然用户不可见，但会影响组件性能。不要将 props 镜像给 state，请考虑直接使用 props。

如果组件实现了 `getSnapshotBeforeUpdate()` 生命周期（不常用），则它的返回值将作为 `componentDidUpdate()` 的第三个参数 snapshot 传递，否则此参数将为 undefined。

## 3 卸载

组件从 DOM 中移除时会调用 componentWillUnmount 方法，可以在该生命周期函数中清除 componentDidMount 中启动的后台进程（比如一些监听、计时器、订阅等）。

`componentWillUnmount()` 中**不应调用 `setState()`**，因为该组件将永远不会重新渲染。组件实例卸载后，将永远不会再挂载它。

## 4 错误处理

当渲染过程、生命周期或子组件的构造函数中抛出错误时，会调用如下方法：

* `static getDerivedStateFromError(error)`
* `componentDidCatch()`

如果 class 组件定义了生命周期方法 `static getDerivedStateFromError()` 或 `componentDidCatch()` 中的任何一个（或两者），它就成为了 Error boundaries，通过生命周期更新 state 可让组件捕获树中未处理的 JavaScript 错误并展示降级 UI。

Error boundaries 组件会捕获**子组件**在渲染期间，在生命周期方法以及其整个树的构造函数中发生的错误，并记录这些错误，展示降级 UI 而不是崩溃的组件树。

#### (1) getDerivedStateFromError

`static getDerivedStateFromError(error)` 生命周期会在后代组件抛出错误后被调用，它将抛出的错误作为参数，并返回一个值以更新 state。`getDerivedStateFromError()` 会在渲染阶段调用，因此不允许出现副作用。

#### (2) componentDidCatch

`componentDidCatch(error, info)` 生命周期在后代组件抛出错误后被调用。`componentDidCatch()` 会在**提交**阶段被调用，因此允许执行副作用，它应该用于记录错误之类的情况。

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props)
    this.state = { hasError: false }
  }

  static getDerivedStateFromError(error) {
    // 更新state使下一次渲染可以显示降级UI
    return { hasError: true }
  }

  componentDidCatch(error, info) {
    // "组件堆栈"例子:
    //   in ComponentThatThrows (created by App)
    //   in ErrorBoundary (created by App)
    //   in div (created by App)
    //   in App
    logComponentStackToMyService(info.componentStack)
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的降级UI
      return <h1>Something went wrong.</h1>
    }

    return this.props.children
  }
}
```

## 5 补充

不要在 PureComponent 中使用 shouldComponentUpdate。

PureComponent 的原理是继承了 Component 类，自动执行 shouldComponentUpdate 函数，当组件更新时，shouldComponentUpdate 会对 props 和 state 进行了一层**浅比较**，如果组件的 props 和 state 都没有发生改变，render 方法就不会执行，省去 Virtual DOM 的生成和对比过程，达到提升性能的目的。

如果 state 和 props 一直变化的话，还是建议使用 Component，PureComponent 最好用于展示组件。