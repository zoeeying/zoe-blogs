# React 琐碎知识点

## 1 单向数据流

单向数据流可以用下面的图来示意。

![单向数据流](http://m.qpic.cn/psc?/V10mSDSc0CUQs4/6RAq0V9V8Td2AB7JS6C71MkutX8WeGlC3R95Mg1HG0Zd84HKKezla1rcstXxZK8bT*8iATyVHmfjG6aL6WhK*rpZnoTNVZzolB0h.j.1Ces!/b&bo=AAViAwAFYgMFByQ!&rf=viewer_4)

* **State：** 驱动应用的数据源。

* **View：** 以**声明方式**将 State 映射到视图。

* **Actions：** 响应在 View 上的用户输入导致的状态变化。

简单的**单向数据流**是指用户访问 View，View 发出用户交互的 Actions，在 Action 中对 State 进行相应更新，State 更新后会触发 View 更新，这样数据总是清晰地单向流动，便于维护并且可以预测。

从**应用层面**来说，单向数据流指的是我们先把模板写好，然后把模板和数据（数据可能来自后台）整合到一起形成 HTML 代码，然后把这段 HTML 代码插入到文档流里面。 

**单向数据绑定**带来单向数据流，代表框架是 React。React 中父组件传递给子组件的数据，子组件只能读取，不能修改，这体现了典型的单向数据流。

与单向数据绑定相对应的就是**双向数据绑定**，代表框架是 Vue。双向数据绑定指的是数据模型（Module）和视图（View）之间的双向绑定。用户在视图上的修改会自动同步到数据模型中去，同样的，如果数据模型中的值发生了变化，也会立刻同步到视图中去。双向数据绑定的优点是无需进行单向数据绑定那样的 CRUD（Create、Retrieve、Update、Delete）操作。双向数据绑定最通常的应用场景是表单。

## 2 插件

Simple React Snippets 插件可以让我们通过快捷方式快速生成 React 代码。

| 快捷方式 | 快速生成                         |
| -------- | -------------------------------- |
| `imr`    | Import React                     |
| `imrc`   | Import React / Component         |
| `impt`   | Import PropTypes                 |
| `impc`   | Import React / PureComponent     |
| `cc`     | Class Component                  |
| `ccc`    | Class Component With Constructor |
| `sfc`    | Stateless Function Component     |
| `cdm`    | componentDidMount                |
| `cwm`    | componentWillMount               |
| `cwrp`   | componentWillReceiveProps        |
| `gds`    | getDerivedStateFromProps         |
| `scu`    | shouldComponentUpdate            |
| `cwu`    | componentWillUpdate              |
| `cdu`    | componentDidUpdate               |
| `cwu`    | componentWillUpdate              |
| `cdc`    | componentDidCatch                |
| `gsbu`   | getSnapshotBeforeUpdate          |
| `ss`     | setState                         |
| `ssf`    | Functional setState              |
| `ren`    | render                           |
| `rprop`  | Render Prop                      |
| `hoc`    | Higher Order Component           |

## 3 调试工具

在 Chrome 浏览器中安装 React Developer Tools，可以方便我们进行代码调试。比如可以在控制台中打开组件树，查看每个组件的状态、属性等信息。

## 4 create-react-app

create-react-app 是官方出的一个快速构建 React 单页面应用的脚手架工具，它本身集成了 Webpack，并配置了一系列 loader 和默认的 npm 脚本，可以让我们零配置直接上手开发 React 应用。

```bash
npx create-react-app my-app
cd my-app
npm start
```

```bash
# 创建基于TypeScript的React项目，并且使用npm安装依赖
npx create-react-app my-app --template typescript --use-npm
```

```bash
npm init react-app my-app
cd my-app
npm start
```

## 5 非受控组件

```jsx
// 方法1
<select ref={el => this.select = el}>
  <option value="1">1</option>
  <option value="2">2</option>
  <option value="3">3</option>
</select>

const number = this.select.value * 1 // 获取非受控组件的value
```

```jsx
// 方法2，这种方法已经过时了
<select ref="numSelect">
  <option value="1">1</option>
  <option value="2">2</option>
  <option value="3">3</option>
</select>

const number = this.refs.numSelect.value * 1 // 获取非受控组件的value
```

其实就是 Refs 的两种写法。

## 6 派生 state

生命周期函数 `componentWillReceiveProps` 是响应 props 变化之后进行更新的方式，但是在16.3 版本中，使用了一个替代版的生命周期函数 `getDerivedStateFromProps`。`getDerivedStateFromProps` 的存在只有一个目的：让组件在 **props 变化**时更新 state。

名词**受控**和**非受控**通常用来指代表单的 input，但是也可以用来描述数据频繁更新的组件。如果组件通过 props 传入数据的话，可以认为是受控组件（因为组件被父级传入的 props 控制），如果数据只保存在组件的 state 中的话，就是非受控组件（因为外部没办法直接控制 state）。

常见的错误就是把两者混为一谈，当一个**派生 state（即 state 依赖 props 的情况）**值也被 setState 方法更新时，这个值就不是一个单一来源的值了。

大部分使用**派生 state** 导致的问题不外乎两个原因：直接复制 props 到 state 上；如果 props 和 state 不一致就更新 state。解决这两个问题的关键在于，**任何数据，都要保证只有一个数据来源，而且避免直接复制它**。

下面是常见的问题：

```jsx
class EmailInput extends Component {
  state = { email: this.props.email }

  render() {
    return <input onChange={this.handleChange} value={this.state.email} />
  }

  handleChange = event => {
    this.setState({ email: event.target.value })
  };

  componentWillReceiveProps(nextProps) {
    // 问题1，在该生命周期函数中直接复制props到state
    // 这会覆盖所有组件内的state更新，不要这样做！
    // this.setState({ email: nextProps.email })
   
    // 问题2，在props变化后修改state
    // 只要props.email改变，就改变state
    if (nextProps.email !== this.props.email) {
      this.setState({
        email: nextProps.email
      });
    }
  }
}
```

解决上面的问题，有多种解决方案：

#### (1) 完全可控的组件

删除 state，input 中的 value 和 onChange 监听函数都来源于 props。

#### (2) 有 key 的非受控组件

使用 key 属性，重置内部所有的初始 state。

```jsx
class EmailInput extends Component {
  state = { email: this.props.defaultEmail }

  handleChange = event => {
    this.setState({ email: event.target.value })
  }

  render() {
    return <input onChange={this.handleChange} value={this.state.email} />
  }
}
```

```jsx
<EmailInput
  defaultEmail={this.props.user.email}
  key={this.props.user.id}
/>
```

让组件自己存储临时的 state，组件仍然可以从 props 接收初始值，但是更改之后的值就和 props 没关系了。

`EmailInput` 组件中的 key 是特殊的 React 属性，**当 key 变化时，React 会创建一个新的而不是更新一个既有的组件**。每次 id 变化，都会重新创建 `EmailInput`，并将其状态重置为最新的 defaultEmail 值。每次 key 变化，表单里的所有组件都会用新的**初始值**重新创建。

#### (3) 用 props 的 id 重置非受控组件

可能会因为组件初始化的开销太大，而导致 key 不起作用，可以在 `getDerivedStateFromProps` 中观察 `userID` 的变化：

```jsx
class EmailInput extends Component {
  state = {
    email: this.props.defaultEmail,
    prevPropsUserID: this.props.userID
  };

  static getDerivedStateFromProps(props, state) {
    // 只要当前user变化，重置所有跟user相关的状态，这个例子中，只有email和user相关
    if (props.userID !== state.prevPropsUserID) {
      return {
        prevPropsUserID: props.userID,
        email: props.defaultEmail
      };
    }
    return null;
  }

  // ...
}
```

#### (4) 使用实例方法重置非受控组件

更少见的情况是，即使没有合适的 `key`，我们也想重新创建组件，一种解决方案是把一个随机值或者递增的值当作 `key`，另一种解决方案是用**实例方法**强制重置内部状态：

```jsx
class EmailInput extends Component {
  state = {
    email: this.props.defaultEmail
  };
	// 父级组件可以使用ref调用这个方法（refs转发）
  resetEmailForNewUser(newEmail) {
    this.setState({ email: newEmail });
  }

  // ...
}
```

#### (5) memoization

```jsx
// PureComponents只会在state或者props的值修改时才会再次渲染，通过对state和props的key做浅比较来确定有没有变化
class Example extends PureComponent {
  // state只需要保存filter的值
  state = {
    filterText: ""
  };

  handleChange = event => {
    this.setState({ filterText: event.target.value });
  };

  render() {
    // PureComponent的render只有在props.list或state.filterText变化时才会调用
    const filteredList = this.props.list.filter(
      item => item.text.includes(this.state.filterText)
    )

    return (
      <Fragment>
        <input onChange={this.handleChange} value={this.state.filterText} />
        <ul>{filteredList.map(item => <li key={item.id}>{item.text}</li>)}</ul>
      </Fragment>
    );
  }
}
```

上面使用 PureComponent 的例子在过滤很大的列表时，效率不是很好，可以添加 memoization（仅在输入变化时，重新计算 `render` 需要使用的值，这个技术叫做 memoization）帮助函数来阻止非必要的过滤。

```jsx
import memoize from "memoize-one"

class Example extends Component {
  // state只需要保存当前的filter值
  state = { filterText: "" }

  // 在list或者filter变化时，重新运行filter
  filter = memoize(
    (list, filterText) => list.filter(item => item.text.includes(filterText))
  );

  handleChange = event => {
    this.setState({ filterText: event.target.value })
  }

  render() {
    // 计算最新的过滤后的list，如果和上次render参数一样，memoize-one会重复使用上一次的值
    const filteredList = this.filter(this.props.list, this.state.filterText)
    
    return (
      <Fragment>
        <input onChange={this.handleChange} value={this.state.filterText} />
        <ul>{filteredList.map(item => <li key={item.id}>{item.text}</li>)}</ul>
      </Fragment>
    )
  }
}
```





