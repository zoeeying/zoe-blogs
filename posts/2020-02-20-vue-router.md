---
title: vue-router
date: 2020-02-20
---

# vue-router

路由指的是 SPA 的路径管理器，vue-router 是 Web 应用的链接路径管理器。

## 1 入门

可以通过下面的命令来安装 vue-router。

```bash
cnpm install vue-router -S
```

1、在 components 中创建两个模板页面 HelloZoe.vue、HelloWorld.vue

2、修改 App.vue

```vue
<template>
  <div id="app">
    <img src="./assets/logo.png" />
    <div>
      <!-- router-link有点像a标签 -->
      <router-link to="/">首页</router-link>
      <span> | </span>
      <router-link to="/zoe">小畅叙</router-link>
    </div>
    <!-- 显示模板中的内容 -->
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<!-- 直接用style表示全局样式，可以加个scoped表示样式只对当前页面生效 -->
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

3、修改 router/index.js

```javascript
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import HelloZoe from '@/components/HelloZoe'
import ErrorPage from '@/components/ErrorPage'

Vue.use(Router)

export default new Router({
  // mode可以设置为history或者hash，默认是hash
  // 设置为hash，路径中会有#号
  // 设置为hash可以避免输入路径的时候导航不到
  mode: 'history',
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '/zoe',
      name: 'HelloZoe',
      component: HelloZoe
    },
    // 输入的地址不存在时，显示404页面
    {
      path: '*',
      component: ErrorPage
    }
  ]
})
```

4、main.js

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
  router
}).$mount('#app')
```

## 2 子路由

子路由即子菜单的路由方式。子路由一般用在一个页面有它的基础模板，它下面的页面都隶属于这个模板，只是部分改变样式。

1、在 components 中创建两个模板页面 Zoe1.vue、Zoe2.vue

2、修改 HelloZoe.vue，增加 router-link 标签和 router-view 标签，router-view 标签用于显示 Zoe1.vue 和 Zoe2.vue 中的内容。

```vue
<template>
  <div>
    <router-link to="/zoe/zoe1">Zoe1</router-link>
    <span> | </span>
    <router-link to="/zoe/zoe2">Zoe2</router-link>
    <router-view />
  </div>
</template>
```

3、修改 router/index.js

```javascript
// ...
export default new Router({
  routes: [
    // ...
    {
      path: '/zoe',
      component: HelloZoe,
      children: [
        {
          path: '/',
          name: 'zoe1',
          component: Zoe1
        },
        {
          path: 'zoe1',
          name: 'zoe1',
          component: Zoe1
        },
        {
          path: 'zoe2',
          name: 'zoe2',
          component: Zoe2
        }
      ]
    }
  ]
})
```

## 3 参数传递

#### (1) 用 name 传递参数

这种方法在实际开发中不常用。

1、路由配置文件 router/index.js 中，为需要传参的路由设置 name（如果一个路由有 children，那么就不能为其设置 name）

2、模板页面中通过 `$route.name` 接收参数

```vue
<p>{{ $route.name }}</p>
```

#### (2) 用 router-link 传递参数

1、传递参数

```vue
<router-link
  :to="{
    name: 'zoe2',
    params: { username: '叙叙', age: 28 }
  }">Zoe2</router-link>
```

2、模板页面中通过 `$route.params.xxxx` 接收参数

```vue
<div>
  <b>路由传递过来的参数：</b>
  <span>username：{{$route.params.username}}，age：{{$route.params.age}}</span>
</div>
```

#### (3) 通过 url 传参

1、路由配置文件

```javascript
// ...
export default new Router({
  routes: [
    // ...
    {
      // 在path中绑定userId和userName参数，(\\d+)是正则，限制userId只能传数字
      path: '/params/:userId(\\d+)/:userName',
      name: 'params',
      component: Params
    }
  ]
})
```

2、模板页面中通过 router-link 跳转页面并且传递参数，注意第一个参数必须是数字

```vue
<router-link to="/params/12/无敌小畅叙">传参</router-link>
```

## 4 单页面多路由区域操作

一个模板页面中有多个 router-view（多个路由区域）

1、模板页面，设置 router-view 标签的 name 属性（路由配置文件中需要用）

```vue
<div>
  <router-view />
  <router-view
    name="left"
    style="float:left;width:50%;background:pink;height:100px"
  />
  <router-view
    name="right"
    style="float:right;width:50%;background:lightblue;height:100px"
  />
</div>
```

2、路由配置文件 router/index.js

```javascript
// ...
export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      // 注意这里是components，是复数
      components: {
        default: HelloWorld,
        // left和right是router-view标签配置的name
        left: Zoe1,
        right: Zoe3
      }
    }
  ]
})
```

## 5 重定向

```javascript
// ...
export default new Router({
  routes: [
    // ...
    {
      path: '/goHome',
      redirect: '/'
    },
    // 需要传递参数的重定向
    {
      path: '/goHomeParams/:userId(\\d+)/:userName',
      redirect: '/params/:userId(\\d+)/:userName'
    }
  ]
})
```

```vue
<router-link to="/goHome">goHome</router-link>
<!-- 如果不传参数，链接过去的模板页面什么都不显示，也不报错 -->
<router-link to="/goHomeParams/28/小畅叙呀呀呀">goHomeParams</router-link>
```

## 6 alias 别名

不能给根路径加别名。

**别名**和**重定向**的区别：重定向的 component 和路径都是一样的；别名的 component 是一样的，但是路径是不一样的。

```javascript
// ...
export default new Router({
  routes: [
    // ..
    {
      path: '/zoe1',
      component: Zoe1,
      alias: '/zoe1Alias'
    }
  ]
})
```

## 7 路由过渡动画

路由切换的时候增加一些动效，只需要给 router-view 外边包上 transition 标签即可。

**CSS 过渡类名：**组件过渡过程中，会有用于切换的四个 CSS 类名，如果 transition 的 name 属性设置为 fade，就会有下面四个类名：

| 类名              | 描述                                                                                                                     |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------ |
| fade-enter        | **进入过程**的**开始状态**，元素被插入时生效，只应用一帧后立刻删除                                                       |
| fade-enter-active | **进入过程**的**结束状态**，元素被插入时就生效，进入过程完成之后移除，这个类可以被用来定义过渡的过程时间、延迟和曲线函数 |
| fade-leave        | **离开过程**的**开始状态**，元素被删除时生效，只应用一帧后立刻删除                                                       |
| fade-leave-active | **离开过程**的**结束状态**，元素被删除时就生效，离开过程完成之后移除，这个类可以被用来定义过渡的过程时间、延迟和曲线函数 |

fade-enter-active 和 fade-leave-active 在整个进入或离开过程中都有效，所以可以在这两个类下设置 CSS 的 transition 属性。

在 router-view 外边包上 transition 标签：

```vue
<transition
  name="fade"
  mode="out-in"
>
  <router-view />
</transtion>
```

在 style 标签中写 CSS 样式：

```vue
<style scoped>
.fade-enter {
  opacity: 0;
}
.fade-enter-active {
  opacity: 1;
  transition: opacity 0.2s;
}
.fade-leave {
  opacity: 1;
}
.fade-leave-active {
  opacity: 0;
  transition: opacity 0.2s;
}
</style>
```

**transition 的 mode 属性（过渡模式）：**

**in-out：** 新元素先进入，当前元素再离开

**out-in：** 当前元素先离开，新元素再进入

## 8 路由中的钩子函数

#### (1) 路由配置中的钩子函数

路由配置文件中只能写**进入之前**的钩子函数，即 beforeEnter。

```javascript
// ...
export default new Router({
  mode: 'history',
  routes: [
    // ...
    {
      path: '/params/:userId(\\d+)/:userName',
      name: 'params',
      component: Params,
      beforeEnter: (to, from, next) => {
        // to和from是对象，里面有一些path、params等信息
        console.log(to)
        console.log(from)
        // 如果不写next()的话，路由是不会跳转的
        // next函数中可以传入三个参数
        // false表示不会跳转；true表示可以跳转；还可以传入对象{path:'/'}，表示跳转到根路径
        next()
      }
    }
  ]
})
```

#### (2) 组件中的钩子函数

模板文件中可以写 beforeRouteEnter 和 beforeLeaveEnter 钩子函数，匿名函数中的 to、from、next 参数同上。

```vue
<template>
  <div>
    <h2>{{ msg }}</h2>
    <div>
      userId：{{ $route.params.userId }}，userName：{{ $route.params.userName }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'Params',
  data() {
    return {
      msg: '通过url传递参数'
    }
  },
  beforeRouteEnter: (to, from, next) => {
    console.log(to)
    console.log(from)
    next()
  },
  beforeRouteLeave: (to, from, next) => {
    console.log(to)
    console.log(from)
    next()
  }
}
</script>
```

## 9 编程式导航

上面我们都是通过 router-link 标签来实现路由跳转的，但是实际开发中，我们需要根据业务逻辑写 JS 来做路由跳转，即编程式导航。

```vue
<template>
  <div>
    <button @click="goBack">后退</button>
    <button @click="goForward">前进</button>
    <button @click="goHome">返回首页</button>
  </div>
</template>

<script>
export default {
  name: 'App',
  methods: {
    goBack() {
      // 路由后退
      this.$router.go(-1)
    },
    goForward() {
      // 路由前进
      this.$router.go(1)
    },
    goHome() {
      // 跳转到具体的某个路由
      this.$router.push('/')
    }
  }
}
</script>
```
