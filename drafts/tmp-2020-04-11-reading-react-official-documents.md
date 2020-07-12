# 阅读 React 官方文档的学习笔记

## 1 JSX

```jsx
const element = <h1>Hello, world!</h1>
```

以上的标签语法就是 JSX 语法，它是 JavaScript 的语法扩展。

React 并没有采用将**标记**与**逻辑**进行分离到不同文件这种人为地分离方式，而是通过将二者共同存放在称之为**组件**的松散耦合单元之中，来实现**关注点分离**。

**补充：** **关注点分离**是日常生活和生产中广泛使用的解决复杂问题的一种系统思维方法，大体思路是，先将复杂问题做合理的分解，再分别仔细研究问题的不同侧面，也就是关注点，最后综合各方面的结果，合成整体的解决方案，模块化是其中最有代表性的具体设计原则之一。

在编译之后，JSX 表达式（JSX 也是一个表达式）会被转为普通 JavaScript 函数调用，并且对其取值后得到 JavaScript 对象。Babel 会把 JSX 转译成一个名为 `React.createElement()` 函数调用。

## 2 元素渲染

元素是构成 React 应用的最小砖块，描述了我们在屏幕上想看到的内容。与浏览器的 DOM 元素不同，React 元素是创建开销极小的普通对象。React DOM 会负责更新 DOM 来与 React 元素保持一致。

React 元素是不可变对象。一旦被创建，就无法更改它的子元素或者属性。一个元素就像电影的单帧：它代表了某个特定时刻的 UI。

React DOM 会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使 DOM 达到预期的状态。

应该考虑 UI 在任意给定时刻的状态，而不是随时间变化的过程。

## 3 组件

组件允许我们将 UI 拆分为独立可复用的代码片段，并对每个片段进行独立构思。从概念上，组件类似于 JavaScript 函数，它接受任意的入参（即 props），并返回用于描述页面展示内容的 React 元素。

组件名称必须以大写字母开头。React 会将以小写字母开头的组件视为原生 DOM 标签。

组件无论是使用函数声明还是通过 class 声明，都决不能修改自身的 props，所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。

## 4 state

state 与 props 类似，但是 state 是私有的，并且完全受控于当前组件。

```jsx
constructor(props) {
  super(props) // 将props传递到父类的构造函数中，使用props参数来调用父类的构造函数
  this.state = {date: new Date()}
}
```

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }

  componentDidMount() {
    // timerID没有必要放在state中，可以作为实例属性
    this.timerID = setInterval(
      () => this.tick(),
      1000
    )
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick = () => {
    this.setState({
      date: new Date()
    })
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)
```

## 5 事件处理

React 元素的事件处理和 DOM 元素的很相似，只是 React 事件的命名采用小驼峰式（camelCase），而不是纯小写，且使用 JSX 语法时需要传入一个函数作为事件处理函数，而不是一个字符串。

在 React 中不能通过返回 `false` 的方式阻止默认行为，必须显式的使用 `preventDefault`。传统的 HTML 中阻止链接默认打开一个新页面：

```html
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```

使用 React 时，一般不需要使用 `addEventListener` 为已创建的 DOM 元素添加监听器，事实上，只需要在该元素初始渲染的时候添加监听器即可。

## 10 Refs

Refs 提供了一种方式，允许我们访问 DOM 节点和在 render 方法中创建的 React 元素。

**何时使用 Refs：** 管理焦点，文本选择或媒体播放；触发强制动画；集成第三方 DOM 库。

创建 Refs 如下：

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props)
    this.myRef = React.createRef()
  }
  render() {
    return <div ref={this.myRef} />
  }
}
```

当 ref 被传递给 `render` 中的元素时，对该节点的引用可以在 ref 的 `current` 属性中被访问：

```javascript
const node = this.myRef.current
```

如果 ref 属性用于 HTML 元素，ref 对象接收底层 DOM 元素作为其 current 属性；如果 ref 属性用于自定义的 class 组件，ref 对象接收组件的挂载实例作为其 current 属性；不能在函数组件上使用 ref 属性，因为函数组件没有实例。

组件卸载的时候，React 会给 current 属性传入 null 值。

`ref` 会在 `componentDidMount` 或 `componentDidUpdate` 生命周期钩子触发前更新。

```jsx
// ref属性用于HTML元素
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    // 创建一个ref来存储textInput的DOM元素
    this.textInput = React.createRef()
    this.focusTextInput = this.focusTextInput.bind(this)
  }

  focusTextInput() {
    // 直接使用原生API使text输入框获得焦点
    // 注意：我们通过current来访问DOM节点
    this.textInput.current.focus()
  }

  render() {
    // 告诉React我们想把<input>的ref关联到构造器里创建的textInput上
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

```jsx
// ref属性用于自定义的class组件
// CustomTextInput是上面的自定义的class组件，组件的构造器中的focusTextInput实例方法可以通过this.textInput.current.focusTextInput来调用，即this.textInput.current表示的是组件的实例
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focusTextInput();
  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    );
  }
}
```

```jsx
// 可以在函数组件内部使用ref属性
function CustomTextInput(props) {
  // 这里必须声明textInput，这样ref才可以引用它
  const textInput = useRef(null)

  function handleClick() {
    textInput.current.focus()
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  )
}
```

如果希望在父组件中引用子节点的 DOM 节点，可以通过 **Ref 转发**使组件像暴露自己的 ref 一样暴露子组件的 ref。

如果想更精细地控制何时 refs 被设置和解除，可以使用**回调 Refs**。不同于传递 `createRef()` 创建的 `ref` 属性，我们可以传递一个函数，这个函数中接受 React 组件实例或 HTML DOM 元素作为参数，以使它们能在其他地方被存储和访问。

```jsx
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props)

    this.textInput = null

    this.setTextInputRef = element => {
      this.textInput = element
    }

    this.focusTextInput = () => {
      // 使用原生DOM API使text输入框获得焦点
      if (this.textInput) this.textInput.focus()
    }
  }

  componentDidMount() {
    // 组件挂载后，让文本框自动获得焦点
    this.focusTextInput()
  }

  render() {
    // 使用ref的回调函数将text输入框DOM节点的引用存储到React实例上（比如this.textInput）
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    )
  }
}
```

看下面的例子，在 `Parent` 中的 `this.inputElement` 被设置为了与 `CustomTextInput` 中的 `input` 元素相对应的 DOM 节点：

```jsx
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  )
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    )
  }
}
```

## 11 Refs 转发

Refs 转发是一项将 ref 自动地通过组件传递到其一子组件的技巧，它是一个可选特性，其允许某些组件（父组件）接收 `ref`，并将其向下传递（转发）给子组件。

```jsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
))

// 可以直接获取DOM button的ref
const ref = React.createRef()
<FancyButton ref={ref}>Click me!</FancyButton>
```

上面的例子，使用 `FancyButton` 的组件可以获取底层 DOM 节点 `button` 的 ref，并在必要时访问，就像其直接使用 DOM `button` 一样。

**HOC 中的 Refs 转发：**

```jsx
function logProps(WrappedComponent) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps)
      console.log('new props:', this.props)
    }

    render() {
      return <WrappedComponent {...this.props} />
    }
  }

  return LogProps
}
```

HOC 可以透传（pass through）所有 `props` 到其包裹的组件，refs 将不会透传下去。如果对 HOC 添加 ref，该 ref 将引用最外层的容器组件，而不是被包裹的组件。但是，我们可以使用 `React.forwardRef` API 明确地将 refs 转发到内部的 `FancyButton` 组件中。

```jsx
function logProps(Component) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps)
      console.log('new props:', this.props)
    }

    render() {
      const {forwardedRef, ...rest} = this.props

      // 将自定义的prop属性forwardedRef定义为ref
      return <Component ref={forwardedRef} {...rest} />
    }
  }

  // 注意React.forwardRef回调的第二个参数ref，我们可以将其作为常规props属性传递给LogProps，例如forwardedRef，然后它就可以被挂载到被LogProps包裹的子组件上
  return React.forwardRef((props, ref) => {
    return <LogProps {...props} forwardedRef={ref} />
  });
}
```

## 12 forceUpdate

```jsx
component.forceUpdate(callback)
```

默认情况下，当组件的 state 或 props 发生变化时，组件将重新渲染。如果 `render()` 方法依赖于其他数据，则可以调用 `forceUpdate()` 强制让组件重新渲染。





