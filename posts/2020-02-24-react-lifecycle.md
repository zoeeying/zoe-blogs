---
title: React 生命周期
date: 2020-02-24
---

# React 生命周期

React 生命周期可以用如下两个图来示意。

![React生命周期](http://a1.qpic.cn/psc?/V10mSDSc0CUQs4/QNsgOSLzUrTyB8UN2gSlSE0d7W3hGPDz0.B4Da9PaE*UHTgy1Fevn0AMsSYJwoJVMc877OajkUQh6uHgFw3PNA!!/c&ek=1&kp=1&pt=0&bo=.QIgA*kCIAMDFzI!&tl=1&vuin=511616698&tm=1582318800&sce=60-2-2&rf=viewer_4)

![React生命周期](http://m.qpic.cn/psc?/V10mSDSc0CUQs4/6RAq0V9V8Td2AB7JS6C71DW2b9uIRnhJpYBBow39vqTAqzjdozS4.nmtSx4JOFFcFVqWLdzuk4uAYj3zfcixdYObTu0k9q481y7Sg3TAt50!/b&bo=0AecA9AHnAMDByI!&rf=viewer_4)

React 生命周期可以分为四个大的阶段：

a. Initialization，初始化阶段；

b. Mounting，挂载阶段

c. Updation，更新阶段

d. Unmounting，卸载阶段

生命周期函数（钩子函数），通俗的说就是在某一时刻会被自动调用执行的函数。

## 1 初始化阶段

初始化阶段，会获取 props 并且对 state 进行初始化，在 ES6 类中是通过 constructor 构造函数来完成的。constructor 其实并不是生命周期函数，它是 ES6 类中的构造函数，但是我们为了整体理解生命周期，可以把 constructor 当作是初始化阶段。当挂载某个组件的时候，constructor 永远都是第一个被触发的。

## 2 挂载阶段

**执行顺序：**componentWillMount > render > componentDidMount

#### (1) componentWillMount

一旦属性被获取并且也初始化了 state，componentWillMount 会被触发，它是在组件被挂载到页面之前触发的，只执行一次。在该函数中，可以调用 setState 方法修改状态，可以异步请求数据。

在组件被渲染完毕之前调用 setState 方法将**不会启动更新生命周期**，在组件更新完毕之后调用 setState 方法将**会启动更新生命周期**。如果在 componentWillMount 方法中定义的回调函数中调用 setState 方法，那么该函数将会在组件被渲染完毕之后被触发，并且会启动更新生命周期。

#### (2) render

state 或 props 发现变化时，render 会执行（更新生命周期）。React 在 render 中创建虚拟 DOM，进行 diff 算法，更新 DOM 树。注意不要在 render 中修改 state，会导致死循环。

`substring()`，第二个参数是结束位置；第一个参数是负数，转换为 0；第二个参数是负数，同样转换为 0。这个方法还有一个怪异的地方，如果传递了两个参数（如果参数有负数，会先把负数转换为 0），会比较这两个参数，把较小的参数作为开始位置。

组件被挂载到页面之后触发，只执行一次。在 componentDidMount 中，可以异步请求数据、修改状态、初始化任何需要用到 DOM 的第三方 JavaScript 库、启动诸如 intervals 或 timers 这样的后台进程。

该方法中的任意 setState 都将启动更新生命周期，并且重新渲染组件。但是不推荐在这个函数中使用 setState，它会触发一次额外的渲染，而且是在浏览器刷新屏幕之前执行，用户看不到这个状态，会导致性能问题。

## 3 更新阶段

**state 改变的执行顺序：**shouldComponentUpdate > componentWillUpdate > render > componentDidUpdate

**props 改变的执行顺序：**componentWillReceiveProps > shouldComponentUpdate > componentWillUpdate > render > componentDidUpdate

注意以上的执行顺序中，只要 shouldComponentUpdate 返回 false，后面的生命周期函数都不会执行了。

#### (1) componentWillReceiveProps(nextProps)

组件第一次存在于 DOM 中，该函数不会执行；组件已经存在于 DOM 中了，该函数才会执行。

组件从父组件中接收了新的 props（只要父组件重新渲染，那么都会给子组件传递一个新的 props，即使值没有发生变化）才会触发。

可以根据 props 的变化，调用 setState 方法来更新子组件的状态，旧的属性还是可以通过 props 获取到，在这个函数中调用 setState 是安全的，并且不会触发额外的 render。

注意：更新阶段只在 componentWillReceiveProps 中可以调用 setState 方法来修改状态。

#### (2) shouldComponentUpdate(nextProps, nextState)

这个生命周期函数在组件每次更新之前触发，是更新生命周期的门卫，是可以取消更新操作的谓词（必须返回布尔值，返回 true  才会更新组件）。

实际开发中，父组件的 render 执行的时候，子组件的 render 也会执行，这会导致一些性能问题，可以在子组件的 shouldComponentUpdate 中判断新旧 state/props 是否相同，相同则返回 false（阻止不必要的更新），从而优化性能。

#### (3) componentWillUpdate(nextProps, nextState)

这个生命周期函数在组件每次更新之前触发，和 componentWillMount 类似，只是 componentWillMount  只会执行一次。

#### (4) componentDidUpdate(prevProps, prevState)

这个生命周期函数在组件每次更新完成之后触发，和 componentDidMount 类似，只是 componentDidMount  只会执行一次。

## 4 卸载阶段（componentWillUnmount）

componentWillUnmount 生命周期函数只会在组件被卸载之前触发。可以在该生命周期函数中清除 componentDidMount 或者 componentWillMount 中启动的后台进程（比如一些监听，计时器等）。

## 5 补充

不要在 PureComponent 中使用 shouldComponentUpdate。

PureComponent 的原理是继承了 Component类，自动执行 shouldComponentUpdate 函数，当组件更新时，shouldComponentUpdate 会对 props 和 state 进行了一层**浅比较**，如果组件的 props 和 state 都没有发生改变，render 方法就不会执行，省去 Virtual DOM 的生成和对比过程，达到提升性能的目的。

如果 state 和 props 一直变化的话，还是建议使用 Component，PureComponent 最好用于展示组件。