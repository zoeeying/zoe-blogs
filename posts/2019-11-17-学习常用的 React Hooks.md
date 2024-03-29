# 学习常用的 React Hooks

React Hooks 是用**函数形式**代替原来的**类形式**来定义组件的。React Hooks 可以在无需修改组件结构的情况下**复用状态逻辑**，将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据），而并非强制按照生命周期划分。

**组件类的缺点：** 大型组件很难拆分和重构，也很难测试；业务逻辑分散在组件的各个方法之中，导致重复逻辑或关联逻辑；组件类引入了复杂的编程模式，比如 render props 和高阶组件。

**使用 Hooks 的规则：** 只能在**函数最外层**调用 Hooks，不要在循环、条件判断或者嵌套函数中调用；只能在 **React 的函数组件**中调用 Hooks；不要在其他普通 JavaScript 函数中调用（自定义的 Hooks 中也可以调用 Hooks）。

只要 Hooks 的调用顺序在多次渲染之间保持一致，React 就能正确地将内部 state 和对应的 Hooks 进行关联，**这就是为什么 Hooks 需要在我们组件的最顶层调用。**

## 1 小案例

1、使用 create-react-app 创建一个项目：

```bash
npx create-react-app demo01 # demo01是项目名
cd demo01
npm start
```

2、使用组件类的计数器例子：

```jsx
import React, { Component } from 'react'
export default class Counter extends Component {
  constructor(props) {
    super(props)
    this.state = {
      count: 0,
    }
  }
  render() {
    const { count } = this.state
    return (
      <>
        <p>点击了 {count} 次</p>
        <button onClick={this.addCount}>点击</button>
      </>
    )
  }
  addCount = () => {
    this.setState(prevState => ({ count: prevState.count + 1 }))
  }
}
```

3、使用 React Hooks 改写上面的计数器例子：

```jsx
import React, { useState } from 'react'
export default props => {
  const [count, setCount] = useState(0)
  return (
    <>
      <p>点击了 {count} 次</p>
      <button
        onClick={() => {
          setCount(prev => prev + 1)
        }}
      >
        点击
      </button>
    </>
  )
}
```

## 2 useState

```jsx
const count = useState(0)[0]
const setCount = useState(0)[1]

// 上面的写法可以使用数组解构来简写
const [count, setCount] = useState(0)
```

函数组件本身是没有状态的，但是我们可以在函数组件里调用 useState 来给组件添加一些内部 state，React 会在重复渲染时保留这个 state。useState 会返回一个数组，数组中有两个元素：**当前状态**和一个**更新状态的函数**。

**React 是如何记住状态的呢？它的工作原理是什么呢？**

React 的工作原理是调用顺序规则（每次必须以相同的顺序调用）。

React 第一次渲染函数组件时，它同时会创建一个对象与之共存，该对象是该组件实例的定制对象，而不是全局对象。只要组件存在于 DOM 中，这个对象就会一直存在。使用该对象，React 可以跟踪属于组件的各个元数据位（我理解是用于检索 Hooks）。

React 可以在调用每个组件之前设置 Hooks 数组，它开始是空的，每次调用一个 Hook 时，React 都会向该数组添加该 Hook。

比如我们使用 useState 三次，给组件添加了三个状态，React 会在第一次渲染时将这三个 Hook 放入 Hooks 数组中。下次渲染时，同样的 3 个 Hooks 会以相同的顺序被调用，所以 React 可以查看它的数组，如果发现已经在位置 0 有一个 useState Hook，那么 React 不会创建新状态，而是返回现有状态。

这就是为什么 React 能够在多个函数调用中创建和维护状态。

#### (1) 给 useState 传递函数

如果初始 state 需要通过复杂计算获得，则可以传入一个函数，在函数中计算并返回初始的 state，此函数只在**初始渲染**时被调用。

```jsx
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props)
  return initialState
})
```

#### (2) 给 setState 传递函数

如果新的 state 需要通过使用先前的 state 计算得出，那么可以将函数传递给 setState，该函数将接收先前的 state，并返回一个更新后的值。

```jsx
setCount(prevCount => prevCount + 1)}

// useState不会自动合并更新对象，可以用函数式的setState结合展开运算符来达到合并更新对象的效果
setState(prevState => {
  // 也可以使用Object.assign
  return {...prevState, ...updatedValues}
})
```

调用 setState 并传入当前的 state 时，React 将跳过**子组件的渲染**及 **effect 的执行**。

## 3 useEffect

useEffect 是一个 Hook，它接收两个参数，一个是**包含命令式、且可能有副作用（数据获取、订阅或者手动修改 DOM）代码**的函数，一个是**依赖项数组**。一旦依赖项发生变化，useEffect 就会执行。**依赖项数组**不会作为参数传给 useEffect 函数。

useEffect 跟 class 组件中的 componentDidMount、componentDidUpdate 和 componentWillUnmount 具有相同的用途，只不过被合并成了一个 API。

```jsx
useEffect(() => {
  console.log('页面加载了') // return上面的语句在组件加载了之后执行一次，相当于componentDidMount
  return () => {
    // 返回的匿名函数只在组件卸载前执行一次，相当于componentWillUnmount
    console.log('页面卸载了')
  }
}, []) // 依赖项数组是空数组
```

**补充**：推荐启用 [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation) 中的 [`exhaustive-deps`](https://github.com/facebook/react/issues/14920) 规则，此规则会在添加错误依赖时发出警告并给出修复建议。

```bash
npm install eslint-plugin-react-hooks --save-dev
```

```javascript
// ESLint配置
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error", // 检查Hook的规则
    "react-hooks/exhaustive-deps": "warn" // 检查effect的依赖
  }
}
```

## 4 useContext

React Context 提供了一种在组件之间**共享状态**的方法，而不必显式地通过组件树逐层传递 props。React Context 的设计目的是为了共享那些对于一个组件树而言是**全局**的状态。我们可以使用 useContext 来改写传统的类组件中的 Context 写法，useContext 接收一个 **Context 对象**（`React.createContext` 的返回值）并返回该 Context 的当前值。

```jsx
// Context可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树
// {theme: 'light'}为defaultValue，只有当组件所处的树中没有匹配到Provider时，defaultValue参数才会生效，这有助于在不使用Provider包装组件的情况下对组件进行测试
// 注意：将undefined传递给Provider时，消费组件的defaultValue不会生效
import React, { createContext } from 'react'
const ThemeContext = createContext({ theme: 'light' }) // 建立一个Context对象

class App extends React.Component {
  render() {
    // AppContext.Provider提供了一个Context对象，这个对象可以被子组件共享
    // 使用一个Provider来将当前的theme传递给下面的组件树，无论多深，任何组件都能读取这个值
    return (
      <ThemeContext.Provider value={{ theme: 'dark' }}>
        <Toolbar />
      </ThemeContext.Provider>
    )
  }
}

// 中间的组件再也不必指明往下传递theme了
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  )
}

class ThemedButton extends React.Component {
  // 指定contextType读取当前的Context对象
  // 当React渲染一个订阅了这个Context对象的组件，这个组件会从组件树中离自身最近的那个匹配的Provider中读取到当前的Context值
  static contextType = ThemeContext
  render() {
    // 注意是通过this.context读取当前的Context值
    return <Button theme={this.context}>按钮</Button>
  }
}
```

也可以使用 useContext 改写上面的 ThemedButton 组件。

```jsx
const ThemedButton = () => {
  const theme = useContext(ThemeContext)
  return <Button theme={theme}>按钮</Button>
}
```

## 5 useReducer

React 本身不提供状态管理功能，通常需要使用外部库，最常用的库是 Redux。Redux 的核心概念是，组件发出 action 与状态管理器通讯，状态管理器收到 action 以后，使用 Reducer 函数算出新的状态，Reducer 函数的形式是 `(state, action) => newState`。

useReducer 用来引入 Reducer 功能，它接收一个 Reducer，并返回当前的 state 以及与其配套的 dispatch 方法。React 会确保 dispatch 函数的标识是稳定的，并且不会在组件重新渲染时改变。

如果 useReducer 的返回值与当前 state 相同，React 将跳过子组件的渲染及副作用的执行。

由于 useReducer 可以提供共享状态和 Reducer 函数，所以它在这些方面可以取代 Redux。但是它没法提供中间件（middleware）和时间旅行（time travel），所以需要这两个功能，还是要用 Redux。

```jsx
// useReducer基本用法
const [state, dispatch] = useReducer(reducer, initialState)

// 用于计算状态的Reducer函数
const myReducer = (state, action) => {
  switch (action.type) {
    case 'countUp':
      return {
        ...state,
        count: state.count + 1,
      }
    default:
      return state
  }
}

// 组件代码
function App() {
  const [state, dispatch] = useReducer(myReducer, { count: 0 })
  return (
    <div className="App">
      <button onClick={() => dispatch({ type: 'countUp' })}>+1</button>
      <p>Count: {state.count}</p>
    </div>
  )
}
```

[使用 useReducer 和 useContext 代替 Redux 的小案例：](https://github.com/zoeeying/react-redux-learning)

1、新建一个文件 redux.js，用于放使用 Context 管理的状态以及使用 useReducer 改变状态的操作。

```jsx
import React, { createContext, useReducer } from 'react'

// Context对象
export const ColorContext = createContext({})

// 改变状态的操作
export const UPDATE_COLOR = 'UPDATE_COLOR'

// reducer函数
const reducer = (state, action) => {
  switch (action.type) {
    case UPDATE_COLOR:
      return action.color
    default:
      return state
  }
}

// 包裹起来的组件可以共享Context对象
export const Color = props => {
  const [color, dispatch] = useReducer(reducer, 'blue')
  return <ColorContext.Provider value={{ color, dispatch }}>{props.children}</ColorContext.Provider>
}
```

2、新建 ShowText.js 文件，创建 ShowText 组件。

```jsx
import React, { useContext } from 'react'
import { ColorContext } from './redux'

export default () => {
  const { color } = useContext(ColorContext)
  return <div style={{ color }}>我是什么颜色的呀？</div>
}
```

3、新建 Buttons.js 文件，创建 Buttons 组件。

```jsx
import React, { useContext } from 'react'
import { ColorContext, UPDATE_COLOR } from './redux'
export default () => {
  const { dispatch } = useContext(ColorContext)
  return (
    <>
      <button
        onClick={() => {
          dispatch({ type: UPDATE_COLOR, color: 'red' })
        }}
      >
        红色
      </button>
      <button
        onClick={() => {
          dispatch({ type: UPDATE_COLOR, color: 'green' })
        }}
      >
        绿色
      </button>
    </>
  )
}
```

4、新建 index.js 文件作为入口文件。

```jsx
import React from 'react'
import ReactDOM from 'react-dom'
import Buttons from './Buttons'
import ShowText from './ShowText'
import { Color } from './redux'

const App = () => {
  return (
    // 使用Color把ShowText组件和Buttons组件包裹起来
    <Color>
      <ShowText />
      <Buttons />
    </Color>
  )
}
ReactDOM.render(<App />, document.getElementById('root'))
```

## 6 useMemo

如果某个函数执行比较耗时，在组件重新渲染的时候不希望重复执行该函数，可以使用 useMemo 将其**执行结果**返回值缓存起来。

useMemo 的第一个参数是一个函数，该函数返回的值会被缓存起来，同时这个值会作为 useMemo 的返回值；useMemo 的第二个参数是一个依赖数组，如果数组中的值有变化，那么就会去重新执行第一个参数中的函数，并将函数返回值缓存起来作为 useMemo 的返回值。

```jsx
import React, { useState, useMemo } from 'react'
export default () => {
  const [num, setNum] = useState(0)

  const expensiveFn = () => {
    let result = 0
    for (let i = 0; i < 100000; i++) {
      result += i
    }
    console.log(result)
    return result
  }
  
  const base = useMemo(expensiveFn, [])

  return (
    <div className="App">
      <h1>count：{num}</h1>
      <button onClick={() => setNum(num + base)}>+1</button>
    </div>
  )
}
```

## 7 useCallback

如果一个父组件包含子组件，子组件接收一个函数作为 props，那么，如果父组件重新渲染了，该父组件（函数组件）中的代码都会重新执行，每次执行都会创建新的 callback 函数，导致子组件接收到的 props 发生改变，从而触发子组件的重新渲染，而这种情况下的重新渲染其实是没有必要的，可以借助 useCallback 把 callback 函数缓存起来，在父组件重新渲染时保持两个 callback 函数的引用一致，从而避免**子组件的不必要的重新渲染**。

useCallback 和 useMemo 的参数跟 useEffect（用于处理副作用，而 useCallback 和 useMemo 不可以）一致。

useMemo 和 useCallback 都会在组件第一次渲染的时候执行，之后会在其依赖的变量发生改变时再次执行，并且它们都返回缓存的值，**useMemo 返回缓存的变量，useCallback 返回缓存的函数**。

```jsx
import React, { useState, useCallback, useEffect } from 'react'
function Parent() {
  const [count, setCount] = useState(1)
  const [val, setVal] = useState('')

  const callback = useCallback(() => {
    return count
  }, [count])

  return (
    <div>
      <h4>{count}</h4>
      <Child callback={callback} />
      <div>
        <button onClick={() => setCount(count + 1)}>+</button>
        <input value={val} onChange={event => setVal(event.target.value)} />
      </div>
    </div>
  )
}

function Child({ callback }) {
  const [count, setCount] = useState(() => callback())

  useEffect(() => {
    setCount(callback())
  }, [callback])

  return <div>{count}</div>
}
```

## 8 useRef

useRef 可以用来获取 DOM 元素，也可以用来保存变量。

```jsx
import React, { useRef, useState, useEffect } from 'react'
export default () => {
  // 用来获取DOM元素
  const inputElement = useRef()
  // 用来保存变量
  const textRef = useRef()
  const [text, setText] = useState('Zoe')

  useEffect(() => {
    textRef.current = text
    console.log('使用useRef保存了变量！', textRef.current)
  }, [text])

  const onClick = () => {
    inputElement.current.value = 'Hello, Zoe'
    console.log(inputElement)
  }

  return (
    <>
      <input ref={inputElement} />
      <button onClick={onClick}>按钮</button>
      <br />
      <input
        value={text}
        onChange={e => {
          setText(e.target.value)
        }}
      />
    </>
  )
}
```

## 9 自定义 Hooks

通过自定义 Hooks，可以将组件逻辑提取到可重用的函数中来复用状态逻辑，但是它不复用 state 本身，在复用状态逻辑的组件中，各自的 state 是相互独立的。事实上 Hooks 的每次调用都有一个完全独立的 state，因此可以在单个组件中多次调用同一个自定义 Hook。

自定义 Hooks 更像是一种约定而不是功能，并不是 React 的特性。

自定义 Hooks 名字以 use 开头，内部可以调用其他的 Hooks，但是要确保只在顶层无条件地调用其他 Hooks。

```jsx
import React, { useState, useEffect, useCallback } from 'react'

// 自定义Hook，用于监听窗口大小的改变
const useSize = () => {
  const [size, setSize] = useState({
    width: document.documentElement.clientWidth,
    height: document.documentElement.clientHeight,
  })

  // 使用useCallback来缓存方法
  const onResize = useCallback(() => {
    setSize({
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight,
    })
  }, [])

  useEffect(() => {
    window.addEventListener('resize', onResize)
    return () => {
      window.removeEventListener('resize', onResize)
    }
  }, [onResize])

  return size
}

// 使用自定义Hook
export default () => {
  const size = useSize()
  return (
    <>
      <div>页面宽度：{size.width}</div>
      <div>页面高度：{size.height}</div>
    </>
  )
}
```

## 10 容器模式

由于 Hooks 必须在顶层作用域执行，而不能放在条件判断、循环等语句中，也不能在可能的 return 语句之后执行（Hooks 必须按顺序被执行到），会导致一些业务逻辑受限，比如下面的业务场景：

```react
import { useState } from 'react'
import { Modal } from 'antd'

function UserInfoModal({ visible, ...rest }) {
  // 当visible为false时，不渲染任何内容
  if (!visible) return null

  // 报错，不能在可能的return语句之后执行Hooks
  const [userInfo, setUserInfo] = useState({})
  
  // ...

  return (
    <Modal visible={visible} {...rest}>
      {/* 对话框的内容 */}
    </Modal>
  )
}
```

可以使用**容器模式**来实现上面的逻辑，具体做法就是把条件判断的结果放到两个组件之中，确保真正 render UI 的组件收到的所有属性都是有值的，针对上面的例子，可以在 UserInfoModal 外层加一个容器，这样就能实现条件渲染了：

```react
export default function UserInfoModalWrapper({ visible, ...rest }) {
  // 如果对话框不显示，则不render任何内容
  if (!visible) return null
  
  // 否则执行对话框的组件逻辑
  return <UserInfoModal visible {...rest} />
}
```

## 11 render props

在 Class 组件时期，render props 和 HOC（高阶组件）两种模式可以说是进行**逻辑重用**（包括数据逻辑和 UI 逻辑）的两把利器，但是实际上，HOC 的所有场景几乎都可以用 render props 来实现。

Hooks 出现后，对于**数据逻辑重用**，首选 **Hooks**，而对于 **UI 逻辑的重用**，还是需要借助于 **render props**。

render props 就是把一个 **render 函数**作为属性传递给某个组件，由这个组件去执行这个函数从而 render 实际的内容，适用于类组件和函数组件。

下面看一个使用 render props 来封装计数器**数据逻辑**的例子：

```react
import { useState, useCallback } from 'react'

// 使用render props模式来做数据逻辑重用
// 其实对于数据逻辑重用，使用Hooks更合适
function CounterRenderProps({ render }) {
  const [count, setCount] = useState(0)

  const increment = useCallback(() => {
    setCount(count + 1)
  }, [count])

  const decrement = useCallback(() => {
    setCount(count - 1)
  }, [count])

  // 不render任何UI的组件
  return render({ count, increment, decrement })
}

export default function CounterRenderPropsExample() {
  return (
    <CounterRenderProps
      // render属性值是一个函数，在CounterRenderProps中调用
      render={({ count, increment, decrement }) => {
        return (
          <div>
            <button onClick={decrement}>-</button>
            <span>{count}</span>
            <button onClick={increment}>+</button>
          </div>
        )
      }}
      />
  )
}
```

下面是一个使用 render props 做 **UI 逻辑重用**的例子：

```react
import { Popover, Tag, Button } from 'antd'

const data = [
  {
    id: '1',
    name: 'Kennedy',
    job: 'Chief Mobility Orchestrator',
    city: 'North Alec',
  },
  {
    id: '2',
    name: 'Lucius',
    job: 'Internal Research Manager',
    city: 'Littleland',
  },
  {
    id: '3',
    name: 'Carlos',
    job: 'Lead Configuration Analyst',
    city: 'South Lillian',
  },
  
  // ...
]

function ListWithMore({ renderItem, data = [], max }) {
  const elements = data.map((item, index) => renderItem(item, index, data))
  const show = elements.slice(0, max)
  const hide = elements.slice(max)

  return (
    <span>
      {show}
      {hide.length > 0 && (
        <Popover content={hide}>
          <a>
            <span>and {hide.length} more...</span>
          </a>
        </Popover>
      )}
    </span>
  )
}

export default function ListWithMoreExample() {
  return (
    <div>
      <h2>User Names</h2>
      <div>
        <ListWithMore
          renderItem={user => {
            return <Tag>{user.name}</Tag>
          }}
          data={data}
          max={3}
        />
      </div>
    </div>
  )
}
```

## 12 处理表单

```react
import { useState, useCallback, useMemo } from 'react'

function useForm(initialValues = {}, validators) {
  const [values, setValues] = useState(initialValues)
  const [errors, setErrors] = useState({})

  const setFieldValue = useCallback(
    (name, value) => {
      setValues(values => ({
        ...values,
        [name]: value,
      }))

      // validators是针对某个字段的验证函数，验证函数存在才调用
      if (validators[name]) {
        const errMsg = validators[name](value) || ''
        setErrors(errors => ({
          ...errors,
          [name]: errMsg,
        }))
      }
    },
    [validators]
  )

  // 返回整个form的值、设置值的方法和错误信息
  return { values, errors, setFieldValue }
}

export default function UseFormDemo() {
  // 用useMemo缓存validators对象
  const validators = useMemo(() => {
    return {
      name: value => {
        // 要求name的长度不得小于2
        if (value.length < 2) {
          return 'Name length should be no less than 2.'
        }
        return ''
      },
      email: value => {
        // 简单的实现一个email验证逻辑：必须包含@符号
        if (!value.includes('@')) {
          return 'Invalid email address'
        }
        return ''
      },
    }
  }, [])

  const { values, errors, setFieldValue } = useForm({}, validators)

  // 处理表单的提交事件
  const onSubmit = useCallback(
    e => {
      e.preventDefault()
      console.log(values)
    },
    [values]
  )

  return (
    <form onSubmit={onSubmit}>
      <div>
        <label>Name: </label>
        <input value={values.name} onChange={e => setFieldValue('name', e.target.value)} />
        <span style={{ color: 'red' }}>{errors.name}</span>
      </div>
      <div>
        <label>Email: </label>
        <input value={values.email} onChange={e => setFieldValue('email', e.target.value)} />
        <span style={{ color: 'red' }}>{errors.email}</span>
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}
```

## 13 一些思想

本节摘抄极客时间王沛的《React Hooks 核心原理与实战》课程中的一些思想。

1、React Hooks 的本质就是提供了让 React 组件能够**绑定到某个可变数据源**的能力。

2、React 的开发其实就是复杂应用程序**状态的管理和开发**。

3、**扩展点机制：** 在任何可能产生单点复杂度的模块中，通过扩展点的方式，允许其它模块为其增加功能。

```react
function ArticleView({ id }) {
  const { article } = useArticle(id)

  return (
    <div className="article-view">
      <MainContent article={article} />
      {/* 定义了一个名为article.footer的扩展点 */}
      <Extension name="article.footer" args={article} />
    </div>
  )
}

extensionEngine.register('article.footer', article => {
  return <CommentList article={article} />
})
```

