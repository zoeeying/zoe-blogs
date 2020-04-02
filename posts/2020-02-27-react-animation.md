---
title: React 动画
date: 2020-02-27
---

# React 动画

## 1 使用 CSS3

使用 CSS3 的 transition 和 keyframes 在 React 中实现简单的动画。

```react
import React, { Component } from 'react'
import './index.css'

class App extends Component {
  constructor(props) {
    super(props)
    this.state = {
      isShow: true
    }
  }
  render() {
    const { isShow } = this.state
    return (
      <>
        <div className={isShow ? 'show' : 'hide'} />
        <button onClick={this.onToggle}>toggle</button>
      </>
    )
  }

  onToggle = () => {
    this.setState(prev => ({ isShow: !prev.isShow }))
  }
}

export default App
```

#### (1) transition 动画

index.css 中代码如下：

```css
.show,
.hide {
  width: 200px;
  height: 200px;
  background-color: tomato;
}
.show {
  opacity: 1;
  transition: all 1.5s ease-in;
}
.hide {
  opacity: 0;
  transition: all 1.5s ease-in;
}
```

#### (2) keyframes 动画

keyframes 动画也叫关键帧动画。index.css 中代码如下：

```css
.show,
.hide {
  width: 200px;
  height: 200px;
  background-color: tomato;
}
.show {
  /* ease-in表示从慢到快 */
  /* forwards表示动画结束会停留在最后一帧，而不会跳到第一帧 */
  animation: show-key 2s ease-in forwards;
}
.hide {
  animation: hide-key 2s ease-in forwards;
}

@keyframes hide-key {
  0% {
    opacity: 1;
    border-radius: 0px;
  }
  50% {
    opacity: 0.5;
    border-radius: 50px;
  }
  100% {
    opacity: 0;
    border-radius: 100px;
  }
}

@keyframes show-key {
  0% {
    opacity: 0;
    border-radius: 100px;
  }
  50% {
    opacity: 0.5;
    border-radius: 50px;
  }
  100% {
    opacity: 1;
    border-radius: 0px;
  }
}
```

如果我们想在最后元素隐藏掉的时候，把 DOM 元素删掉，使用 CSS3 是无法实现的。

## 2 使用 react-transition-group 动画库

react-transition-group 动画库是由 Transition、CSSTransition、TransitionGroup 三个库（其实就是组件）组成的。

react-transition-group 动画库可以实现在元素隐藏掉的时候，把 DOM 元素也删掉。

TransitionGroup 可以实现多 DOM 动画：每个 DOM 都用 CSSTransition 包裹起来，最外边再用 TransitionGroup 包裹起来。

**步骤一：** 安装

```bash
npm install react-transition-group -S
```

**步骤二：** App.js

```react
import React, { Component } from 'react'
import './index.css'
import { CSSTransition } from 'react-transition-group'

class App extends Component {
  constructor(props) {
    super(props)
    this.state = {
      isShow: true
    }
  }
  render() {
    const { isShow } = this.state
    return (
      <>
        <CSSTransition
          in={isShow} // 相当于开关
          timeout={2000} // 动画持续时间
          classNames="avatar" // CSS类名的前缀
          unmountOnExit // 最后元素隐藏掉的时候，删掉DOM元素
          appear // 出现的时候就有动画效果
          >
          <div className="divBox" />
        </CSSTransition>
        <div>
          <button onClick={this.onToggle}>toggle</button>
        </div>
      </>
    )
  }

  onToggle = () => {
    this.setState(prev => ({ isShow: !prev.isShow }))
  }
}

export default App
```

**步骤三：** index.css

```css
.divBox {
  width: 200px;
  height: 200px;
  background-color: tomato;
}
/* avatar前缀是通过CSSTransition组件的classNames属性设置的 */
.avatar-enter {
  opacity: 0;
}
.avatar-enter-active {
  opacity: 1;
  transition: opacity 2000ms;
}
.avatar-enter-done {
  opacity: 1;
}
.avatar-exit {
  opacity: 1;
}
.avatar-exit-active {
  opacity: 0;
  transition: opacity 2000ms;
}
.avatar-exit-done {
  opacity: 0;
}
```
