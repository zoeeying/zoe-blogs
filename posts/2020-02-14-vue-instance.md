---
title: Vue 实例
date: 2020-02-14
---

# Vue 实例

代码地址：<https://github.com/zoeeying/vue-learning>

## 1 实例入门

通过实例我们可以在构造器外部操作构造器内部的属性和方法，这就给其它框架一个融合的机会，让 Vue 可以和其它框架一起使用。

下面通过一个例子来看一下 Vue 和 jQuery 是怎么结合的。

**步骤一：** 下载 jQuery，放在 assets/js 目录中

**步骤二：** 在 HTML 中引用一下 jQuery

```html
<script type="text/javascript" src="../assets/js/vue.js"></script>
<script type="text/javascript" src="../assets/js/jquery.min.js"></script>
```

**步骤三：** 在 Vue 的 mounted 生命周期中使用 jQuery。只能在 DOM 元素挂载到页面上后才能使用 jQuery 操作 DOM 元素，比如 Vue 的 mounted 和 updated 生命周期中。

```vue
<div id="app">
  {{message}}
</div>
<script type="text/javascript">
  var app = new Vue({
    el: '#app',
    data: {
      message: 'Hello World!'
    },
    mounted() {
      $('#app').html('Hello jQuery!')
    },
    methods: {
      add() {
        console.log('add方法被调用了')
      }
    },
  })
  // 在构造器外部调用构造器内部methods中的add方法
  app.add()
</script>
```

## 2 实例方法（生命周期）

| 方法         | 描述                                                                                                  |
| ------------ | ----------------------------------------------------------------------------------------------------- |
| $mount       | 用于挂载扩展                                                                                          |
| $destroy     | 用于销毁（卸载）                                                                                      |
| $forceUpdate | 用于更新                                                                                              |
| $nextTick    | 用于数据修改。当构造器中的 data 被修改后会调用这个方法，也相当于一个钩子函数，跟 updated 生命周期很像 |

```vue
<div id="app"></div>
<p>
  <button onclick="destroy()">销毁</button>
  <button onclick="reload()">刷新</button>
  <button onclick="tick()">修改数据</button>
</p>
<script type="text/javascript">
  var zoeExtend = Vue.extend({
    template: "<p><a :href='zoeUrl'>{{zoeName}}</a></p>",
    data() {
      return ({
        zoeUrl: 'https://zoeeying.github.io',
        zoeName: '小畅叙'
      })
    },
    // 扩展中的生命周期同构造器
    mounted() {
      console.log('扩展被挂载了')
    },
    destroyed() {
      console.log('扩展被销毁了')
    },
    updated() {
      console.log('扩展被更新了')
    },
  })
  var vm = new zoeExtend().$mount('#app')
  function destroy() {
    vm.$destroy()
  }
  function reload() {
    vm.$forceUpdate()
  }
  function tick() {
    vm.zoeName = '无敌小畅叙'
    vm.$nextTick(() => {
      console.log('数据修改后的回调')
    })
  }
</script>
```

## 3 实例方法（事件）

实例事件是指，在构造器外部增加的事件，可以调用构造器内部的数据。

| 事件  | 描述                                                                                                                                                                     |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| $on   | 监听当前实例上的自定义事件。事件可以由 `vm.$emit` 触发。接收两个参数，第一个参数是事件函数名称，第二个参数是一个回调函数，回调函数会接收所有传入事件触发函数的额外参数。 |
| $once | 监听一个自定义事件，但是只触发一次。一旦触发之后，监听器就会被移除。                                                                                                     |
| $off  | 移除自定义事件监听器。如果没有提供参数，则移除所有的事件监听器；如果只提供了事件，则移除该事件所有的监听器；如果同时提供了事件与回调，则只移除这个回调的监听器。         |
| $emit | 触发当前实例上的事件。附加参数都会传给监听器回调。                                                                                                                       |

```vue
<div id="app">
  <div>{{count}}</div>
  <p>
    <button @click="add">add</button>
    <!-- reduce是实例上的自定义事件，所以下面的方式会报错，必须写在onclick中 -->
    <!-- <button @click="reduce">reduce</button> -->
    <br />
    <br />
    <button onclick="reduce()">reduce</button>
    <button onclick="app.$emit('reduceOnce')">reduceOnce</button>
    <button onclick="off()">off</button>
  </p>
</div>

<script type="text/javascript">
  var app = new Vue({
    el: '#app',
    data: {
      count: 1
    },
    methods: {
      add: function() {
        this.count++
      }
    }
  })

  // 在构造器外部添加事件函数
  app.$on('reduce', function() {
    this.count--
  })

  // 只能执行一次的事件函数
  app.$once('reduceOnce', function() {
    this.count--
  })

  function reduce() {
    // app.reduce() // 报错
    // app.add()可以使用
    // 触发当前实例上的事件，必须使用$emit
    app.$emit('reduce')
  }

  // 可以不另外声明reduceOnce函数，直接在onclick中写
  // function reduceOnce() {
  //   app.$emit('reduceOnce')
  // }

  // 关闭reduce事件
  function off() {
    app.$off('reduce')
  }
</script>
```

## 4 组件间通讯

#### (1) props 和 $emit

在 VueCLI 项目中，我们可以通过**事件形式**实现子组件向父组件传值。使用 props 和 $emit。

```vue
<!-- 子组件 -->
<template>
  <div class="child">
    <button @click="changeNum">点我向父组件传值</button>
  </div>
</template>

<script>
  export default {
    name: 'Child',
    methods: {
      changeNum() {
        this.$emit('numChanged', 4869) // 自定义numChanged事件，传值4869
      }
    }
  }
</script>
```

```vue
<!-- 父组件 -->
<template>
	<div id="parent">
    <!-- numChanged是子组件自定义的事件 -->
    <Child v-on:numChanged="updateNum"></Child>
    <h3>{{num}}</h3>
  </div>
</template>

<script>
  import Child from '@/components/Child'
  export default {
    name: 'Parent',
    components: {
      Child
    },
    data() {
      return {
        num: 0
      }
    },
    methods: {
      // 子组件通过e给父组件发送消息，实际上就是子组件把自己的数据发送到父组件
      updateNum(e) {
        this.num = e
      }
    }
  }
</script>
```

#### (2) $emit 和 $on

通过一个空的 Vue 实例作为中央事件总线（事件中心），用它来触发事件和监听事件，巧妙而轻量地实现任何组件间的通讯，包括父子、兄弟、跨级。使用 $emit 和 $on 实例方法。

但是当我们的项目比较大时，最好使用 Vuex 来管理状态。

```vue
<h1>父子、兄弟、跨级组件传值</h1>
<div id="app">
  <aaa></aaa>
  <bbb></bbb>
  <ccc></ccc>
</div>
<template id="aaa">
  <div>
    <h4>我是aaa组件，我有name：{{name}}</h4>
    <button @click="send">把数据发送给ccc组件</button>
  </div>
</template>
<template id="bbb">
  <div>
    <h4>我是bbb组件，我有nickname：{{nickname}}</h4>
    <button @click="send">把数据发送给ccc组件</button>
  </div>
</template>
<template id="ccc">
  <div>
    <h4>我是ccc组件</h4>
    <p>我接收到了aaa组件传过来的name：{{name}}</p>
    <p>我接收到了bbb组件传过来的nickname：{{nickname}}</p>
  </div>
</template>
<script type="text/javascript">
  const Event = new Vue()
  const aaa = {
    template: '#aaa',
    data() {
      return {
        name: '周颖'
      }
    },
    methods: {
      send() {
        Event.$emit('aaaSend', this.name)
      }
    }
  }
  const bbb = {
    template: '#bbb',
    data() {
      return {
        nickname: '小畅叙'
      }
    },
    methods: {
      send() {
        Event.$emit('bbbSend', this.nickname)
      }
    }
  }
  const ccc = {
    template: '#ccc',
    data() {
      return {
        name: '',
        nickname: ''
      }
    },
    mounted() {
      Event.$on('aaaSend', name => {
        this.name = name
      })
      Event.$on('bbbSend', nickname => {
        this.nickname = nickname
      })
    }
  }
  var app = new Vue({
    el: '#app',
    components: { aaa, bbb, ccc }
  })
</script>
```

#### (3) $refs

`ref` 被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 `$refs` 对象上。如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向**组件实例**。

**补充：** $parent 用于访问**父实例**，如果当前实例有的话。$children 用于访问当前实例的直接子组件**（子实例）**，需要注意 `$children` 并不保证顺序，也不是响应式的。

因为 $refs、$parent、$children 可以直接得到组件实例，所以可以直接调用组件的方法或访问数据。

下面看一个例子，使用 $ref 来访问子组件实例：

```vue

<div id="app">
  <zoe ref="zoeRef">
    <p>我是小畅叙吖</p>
  </zoe>
</div>
<template id="zoe">
	<div>
    <slot></slot>
  </div>
</template>
<script type="text/javascript">
  const zoe = {
    template: '#zoe',
    data() {
      return {
        name: 'Zoe'
      }
    },
    methods: {
      sayHello() {
        alert('Hello Zoe')
      }
    }
  }

  var app = new Vue({
    el: '#app',
    components: { zoe },
    mounted() {
      const zoeRef = this.$refs.zoeRef
      console.log(zoeRef.name)
      zoeRef.sayHello()
    }
  })
</script>
```

但是这个方法无法在跨级或兄弟组件间通讯。

