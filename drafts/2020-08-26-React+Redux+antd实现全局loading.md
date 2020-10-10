# React + Redux + antd 实现全局 loading

在实际项目中，如果环境比较慢，接口会耗费比较长的时间来返回值，为了防止用户在这个过程中多次点击导致一些不可预测的问题，我们常常需要添加 loading 遮罩加载效果。

React 项目中，我们使用了 antd 的 Spin 组件，结合 Redux，实现了全局 loading。

## 1 通过 Redux 管理 loading 状态

为了实现全局 loading，我们需要把 loading 状态管理起来，Redux 是一个很好的选择。下面我们通过详解如何使用 Redux 管理全局 loading 状态，顺便复习一下 Redux。

Redux 是一个独立的专门用于做集中式状态管理的 JS 库，实际项目开发中，我们通常会使用 react-redux 来简化在 React 应用中使用 Redux，同时把 Redux 与 React 组件解耦。

#### (1) action

在项目根目录下新建 redux 目录，在 redux 目录下新建 action-types.js 文件：

```

```

在 redux 目录下新建 actions.js 文件：

```

```

action 是 view 发出的通知，表示 state 要发生变化了，它是一个对象，包含 type 属性（表示 action 的名称，是必要属性，值为字符串，具有唯一性）和其它可选属性（用来传递数据）。

#### (2) reducer

在 redux 目录下新建 reducers.js 文件：

```

```

reducers.js 是一个可以包含 n 个 reducer 函数的模块，如果包含超过一个的 reducer 函数，可以使用 redux 提供的 combineReducers 函数，把多个 reducer 函数 combine 起来再暴露出去。

```

```

reducer 函数是纯函数，它根据老的 state 和 action 生成新的 state，而不修改原来的 state。

#### (3) store

在 redux 目录下新建 store.js 文件：

```

```

createStore 是 Redux 提供的用来根据指定的 reducer 函数生成 store 对象的函数，store 对象是 Redux 最核心的管理对象，可以将 state、action、reducer 联系在一起。

store 对象有三个核心方法：`getState()`、`dispatch(action)`、`subscribe(listener)`。

```

```

**补充：** 如果在组件模块中使用 redux-redux 的 connect 高阶组件，就没有必要使用上面的三个方法了；如果是在非组件模块中，比如封装了公共请求方法的 JS 模块中，读取状态必须使用 `store.getState()`，变更状态必须使用 `store.dispatch(action)`。

#### (4) Provider

在入口文件 index.js 中，从 react-redux 中引入组件 Provider，使用 Provider 把 App 组件包起来，同时把 store 对象传给 Provider，这样 App 的所有子组件都可以拿到 store 对象：

```

```

Provider 的原理其实就是使用了 React 的 Context。

#### (5) connect







