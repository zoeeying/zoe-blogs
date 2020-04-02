# React 琐碎知识点

## 1 单向数据流

单向数据流可以用下面的图来示意。

![单向数据流](http://m.qpic.cn/psc?/V10mSDSc0CUQs4/6RAq0V9V8Td2AB7JS6C71MkutX8WeGlC3R95Mg1HG0Zd84HKKezla1rcstXxZK8bT*8iATyVHmfjG6aL6WhK*rpZnoTNVZzolB0h.j.1Ces!/b&bo=AAViAwAFYgMFByQ!&rf=viewer_4)

**State：** 驱动应用的数据源

**View：** 以声明方式将 State 映射到视图

**Actions：** 响应在 View  上的导致状态变化的动作

简单的**单向数据流**是指用户访问 View，View 发出用户交互的 Action，在 Action 中对 State 进行相应更新，State 更新后会触发 View 更新，这样数据总是清晰地单向流动，便于维护并且可以预测。

从应用层面来说，单向数据流指的是我们先把模板写好，然后把模板和数据（数据可能来自后台）整合到一起形成 HTML 代码，然后把这段 HTML 代码插入到文档流里面。 

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
cnpm install create-react-app -g
npx create-react-app demo01 # 在某个空文件夹下使用该命令创建demo01项目
cd demo01
npm start # 启动项目
```

## 5 非受控组件

```jsx
// 方法1
<select ref={select => this.select = select}>
  <option value="1">1</option>
  <option value="2">2</option>
  <option value="3">3</option>
</select>

const number = this.select.value * 1 // 获取非受控组件的value
```

```jsx
// 方法2
<select ref="numSelect">
  <option value="1">1</option>
  <option value="2">2</option>
  <option value="3">3</option>
</select>

const number = this.refs.numSelect.value * 1 // 获取非受控组件的value
```



















