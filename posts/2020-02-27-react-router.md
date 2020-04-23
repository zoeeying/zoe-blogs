---
title: React Router
date: 2020-02-27
---

# React Router

React Router 是一个基于 React 之上的强大的路由库，可以让我们向 SPA 应用中快速添加**视图**和**数据流**，同时保持页面与 URL 间的同步。

React Router 是完整的 React 路由解决方案，它保持 UI 与 URL 同步，拥有简单的 API 与强大的功能，例如代码缓冲加载、动态路由匹配以及建立正确的位置过渡处理。

React Router 是官方维护的，事实上也是唯一可选的路由库，它通过管理 URL，实现组件的切换和状态的变化，开发复杂的应用几乎肯定会用到。

**什么是 SPA 应用？**

单页 web 应用（single page application，SPA），整个应用只有一个完整的页面；`<a></a>` 是页面跳转，页面刷新了，不属于 SPA；单页应用链接，也称为路由链接，不会刷新页面，只做页面的局部更新，本身也不会向服务器发请求；在组件内部，通过 ajax 请求获取数据，并在前端异步展现。

## 1 入门小案例

**步骤1：** 安装 create-react-app，使用 create-react-app 创建项目，安装 react-router-dom：

```bash
npm install create-react-app -g
npx create-react-app demo01 # 在某个空文件夹下使用该命令创建demo01项目
cd demo01
npm install react-router-dom -S
```

**步骤2：** 案例完整代码：

```react
import React from 'react'
import { BrowserRouter as Router, Route, Link } from 'react-router-dom'

const Index = () => <h2>首页页面</h2>
const List = () => <h2>列表页面</h2>

export default () => {
  return (
    <Router>
      <ul>
        <li>
          <Link to="/">首页</Link>
        </li>
        <li>
          <Link to="/list">列表</Link>
        </li>
      </ul>
      { /* exact表示精确匹配，一般项目中只需要首页加上exact */ }
      <Route path="/" exact component={Index} />
      <Route path="/list" component={List} />
    </Router>
  )
}
```

## 2 路由的理解

#### (1) route 路由

route 是一种映射关系，即 key-value，key 是 path（路由路径），value 是 func 回调函数（后台路由）或者 component 组件（前台路由），一个路由就是一个映射关系。

#### (2) router 路由器

路由器对象有两个方法：

```javascript
//url是完整路径，前面有http，path是url的一部分
//req是请求，res是响应
//注册路由
router.get(path, function(req, res))
router.post(path, function(req, res))
```

#### (3) 路由分类

**后台路由：** node 服务器端路由，value 是 function，用来处理客户端提交的请求并返回一个相应数据，当 node 接收到一个请求时，根据请求路径找到匹配的路由，调用路由中的函数处理请求，返回响应数据

```javascript
// 注册路由
router.get(path, function(req, res))
```

**前台路由：** 浏览器路由，value 是 component，当请求某个路由 path 时，浏览器端没有发送 http 请求，但界面会更新显示对应的组件。

```react
// 注册路由
// 当path变为"/about"时，当前路由组件就会变为About组件
<Route path="/about" component={About} />
```

## 3 动态传值

**步骤1：** 通过 Route 设置规则，`path="/list/:id"` 是一个路径匹配规则，路由跳转的时候必须传 id 值，否则会匹配失败

```react
<Route path="/list/:id" component={List} />
```

**步骤2：** 通过 Link 标签传递值

```react
<Link to="/list/4869">列表</Link>
```

**步骤3：** 在组件的 componentDidMount 生命周期函数中接收值

```react
componentDidMount() {
  console.log(this.props.match.params.id)
}
```

## 4 重定向

React Router 中的**重定向**的思想跟 vue-router 差距很大。React Router 中的重定向可以这样理解：比如路径 a 指向的是组件 A，我们需要在组件 A 中重定向到路径 b，路径 b 指向的是组件 B。

通过一个例子我们可以更清楚地理解 React Router 重定向的用法。我们需要从 Index 组件重定向到 Home 组件。

首先配置路由：

```react
<Route path="/" exact component={Index} />
<Route path="/home" component={Home} />
```

然后在 Index 组件中进行重定向，有两种方式：

#### (1) 标签重定向

不适用于复杂的场景。Redirect 标签只要写在 Index.js 的 return 中即可。

```react
<Redirect to="/home" />
```

#### (2) 编程式重定向

在构造函数中写重定向代码。

```react
constructor(props) {
  super(props)
  props.history.push('/home')
}
```

## 5 嵌套路由

我们直接通过一个案例来学习嵌套路由。案例地址：<https://github.com/zoeeying/react-router-learning>。

**步骤1：** App.js 文件写一级导航和一级导航对应的二级页面

```jsx
import React from 'react'
import { BrowserRouter as Router, Route, Link } from 'react-router-dom'
import Home from './components/Home'
import Blog from './components/Blog'
import Hobby from './components/Hobby'
import './index.css'

export default () => {
  return (
    <Router>
      <div className="mainDiv">
        <div className="leftNav">
          <h4>小畅叙的网站</h4>
          <ul>
            <li>
              <Link to="/">首页</Link>
            </li>
            <li>
              <Link to="/blog">博客</Link>
            </li>
            <li>
              <Link to="/hobby">爱好</Link>
            </li>
          </ul>
        </div>
        <div className="rightMain">
          <Route path="/" exact component={Home} />
          <Route path="/blog" component={Blog} />
          <Route path="/hobby" component={Hobby} />
        </div>
      </div>
    </Router>
  )
}
```

**步骤2：** components/Blog/index.js 文件中写二级导航和二级导航对应的三级页面

```react
import React from 'react'
import { Route, Link } from 'react-router-dom'
import FlutterBlog from './FlutterBlog'
import ReactBlog from './ReactBlog'
import VueBlog from './VueBlog'
import '../../index.css'

// App.js中，组件最外层已经包裹了Router，所以子路由配置直接用Route就可以
export default () => {
  return (
    <>
      <div className="topNav">
        <ul>
          <li>
            <Link to="/blog/reactblog">React</Link>
          </li>
          <li>
            <Link to="/blog/vueblog">Vue</Link>
          </li>
          <li>
            <Link to="/blog/flutterblog">Flutter</Link>
          </li>
        </ul>
      </div>
      <div>
        <h2>小畅叙的博客</h2>
        <Route path="/blog/reactblog" component={ReactBlog} />
        <Route path="/blog/vueblog" component={VueBlog} />
        <Route path="/blog/flutterblog" component={FlutterBlog} />
      </div>
    </>
  )
}
```

**步骤3：** 在 components/Blog 目录中写三个三级页面 FlutterBlog、ReactBlog、VueBlog。

## 6 动态获取路由

实际项目开发中，路由数据一般是从后台获取的（可以方便控制权限），所以 Link 和 Route 需要写成动态的。其实即使路由数据不是后端提供的，我们最好也循环遍历生成 Link 和 Route。

```react
import React from 'react'
import { BrowserRouter as Router, Route, Link } from 'react-router-dom'
import Home from './components/Home'
import Blog from './components/Blog'
import Hobby from './components/Hobby'
import './index.css'

export default () => {
  // routeConfig是从后台获取过来的数据
  const routeConfig = [
    { path: '/', component: Home, title: '首页', exact: true },
    { path: '/blog', component: Blog, title: '博客', exact: false },
    { path: '/hobby', component: Hobby, title: '爱好', exact: false }
  ]
  return (
    <Router>
      <div className="mainDiv">
        <div className="leftNav">
          <h4>小畅叙的网站</h4>
          <ul>
            {routeConfig.map(item => (
              <li key={item.path}>
                <Link to={item.path}>{item.title}</Link>
              </li>
            ))}
          </ul>
        </div>
        <div className="rightMain">
          {routeConfig.map(item => (
            <Route
              path={item.path}
              key={item.path}
              component={item.component}
              exact={item.exact}
            />
          ))}
        </div>
      </div>
    </Router>
  )
}
```
