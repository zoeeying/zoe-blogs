# Vue 指令

Vue 是一套用于构建用户界面的渐进式 JavaScript 框架。与其它大型框架不同的是，Vue 被设计为可以自底向上的逐层应用。Vue 的核心库只关注视图层，方便与第三方库或既有项目整合。

代码地址：<https://github.com/zoeeying/vue-learning>

## 1 入门

新建项目目录，在目录下新建文件夹 assets（assets 文件夹在 Linux 和 Unix 系统中不会被编译），在 assets 文件夹下新建文件夹 js 和 css，把官网下载的 vue.js 和 vue.min.js 拷贝到 js 文件夹下。使用命令 `npm init` 在项目中初始化一个 package.json 文件。

下载安装 live-server，它是一个开发级的 Web 服务器。我们也可以使用 VSCode 的 Live Server 插件来实现同样的效果。

```bash
cnpm install -g live-server
live-server # 命令，用于启动一个服务，并且打开当前目录下的index.html
```

```vue
<!-- 在html中使用Vue -->
<!DOCTYPE html>
<html lang="en">

  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Hello world</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
  </head>

  <body>
    <h1>Hello world</h1>
    <hr>
    <div id="app">
      {{message}}
    </div>
    <script type="text/javascript">
      // 构造器
      var app = new Vue({
        el: '#app', // 是上面的div的id
        data: {
          message: 'Hello World!'
        }
      })
    </script>
  </body>

</html>
```

## 2 v-if、v-else、v-show

**v-if：** 判断是否加载，可以减轻服务器的压力，在需要时加载。

**v-show：** 设置 CSS 的 display属性，可以使客户端操作更加流畅。

```vue
<div id="app">
  <!-- v-if和v-else必须成对出现 -->
  <!-- v-if里面的变量如果为false，是不会加载这个dom的，而v-show里面的变量如果为false，dom还是加载的，只是设置样式中display为none了 -->
  <div v-if="isLogin">你好，小畅叙！</div>
  <div v-else>请登录！</div>
  <div v-show="isLogin">小畅叙有没有显示？</div>
</div>
<script type="text/javascript">
  var app = new Vue({
    el: '#app',
    data: {
      isLogin: true
    }
  })
</script>
```

## 3 v-for

让哪个标签循环，就把 v-for 指令写在那个标签的属性上。

```vue
<div id="app">
  <ul>
    <li v-for="item in sortItems">{{item}}</li>
  </ul>
  <hr>
  <ul>
    <li v-for="(student,index) in sortStudents">
      {{index+1}}：{{student.name}}-{{student.age}}
    </li>
  </ul>
</div>
<script type="text/javascript">
  var app = new Vue({
    el: '#app',
    data: {
      items: [1, 32, 4, 23, 76, 92, 12, 29],
      students: [
        { name: '小畅叙', age: 28 },
        { name: '夏日星', age: 18 },
        { name: '畅叙', age: 22 },
        { name: 'Zoe', age: 25 },
      ],
    },
    computed: {
      // 如果想给items排序的话，不能在data中排序
      // computed表示在输出items之前做的操作
      // 这里不能直接用items作为属性名，会报错，需要重新取个名字sortItems
      sortItems: function () {
        return this.items.sort(sortNumber)
      },
      sortStudents: function () {
        return sortArrByKey(this.students, 'age')
      }
    },
  })
  function sortNumber(a, b) {
    return a - b
  }
  // 对象数组排序方法
  function sortArrByKey(arr, key) {
    return arr.sort(function (a, b) {
      var x = a[key]
      var y = b[key]
      return ((x < y) ? -1 : ((x > y) ? 1 : 0))
    })
  }
</script>
```

## 4 v-text、v-html

`{{}}` 是有弊端的，比如在网速很差或者 JS 代码出错的时候，会在网页中显示 `{{}}`，这样很不友好，我们可以使用 `v-text` 指令代替 `{{}}`。

如果 message 中有 h2 等 html 标签，使用 `{{}}` 会直接把 h2 当作字符串显示出来，但是使用 `v-html` 指令就可以把 message 中的 h2 渲染成 html 标签。工作中尽量少用这种方式，可能会引起黑客的 xss 攻击，特别是不要在表单中使用。

```vue
<div id="app">
  <span v-text="message"></span>
  <span v-html="h2Message"></span>
</div>
<script type="text/javascript">
  // 生成器
  var app = new Vue({
    el: '#app', 
    data: {
      message: '显示信息',
      h2Message: '<h2>h2显示信息</h2>'
    }
  })
</script>
```

## 5 v-on

`v-on` 指令用于绑定事件并监听，`v-on` 可以简写成 `@`。

```vue
<button v-on:click="add">加分</button>
<button @click="minus">减分</button>
```

`v-on` 还可以绑定键盘事件，如下，keyup 表示键盘弹起，enter 表示回车键。enter 也可以换成**键盘码**，回车键的键盘码是 13，所以也可以写成 `v-on:keyup.13`。

```vue
<input type="text" v-on:keyup.enter="addMore" v-model="inputScores">
```

上面的 add、minus 和 addMore 方法都在构造器的 methods 属性中。

## 6 v-model

`v-model` 用于绑定数据源，实现双向数据绑定。

#### (1) 不同表单的数据绑定

**文本框、文本域：**

```vue
<p> {{message}}</p>
<input type="text" v-model="message">
<textarea cols="30" rows="10" v-model="message"></textarea>
```

**多选框：**

```vue
<!-- 一个多选框，绑定一个值 -->
<input type="checkbox" id="isAgree" v-model="isAgreeValue">
<label for="isAgree">{{isAgreeValue}}</label>
```

```vue
<!-- 一组多选框，绑定数组 -->
<input type="checkbox" id="name1" value="name1" v-model="names">
<label for="name1">name1</label>
<input type="checkbox" id="name2" value="name2" v-model="names">
<label for="name2">name2</label>
<input type="checkbox" id="name3" value="name3" v-model="names">
<label for="name3">name3</label>
<p>{{names}}</p>
```

**单选框：**

```vue
<!-- 单选框 -->
<input type="radio" id="male" value="男" v-model="sex">
<label for="male">男</label>
<input type="radio" id="female" value="女" v-model="sex">
<label for="female">女</label>
<p>{{sex}}</p>
```

#### (2) 修饰符

**lazy：** 在文本框中输入内容的时候，文本框的值不会绑定到 message 上，文本框失去焦点后，文本框中的值才会绑定到 message 上。

```vue
<input type="text" v-model.lazy="message">
```

**number：** 如果先输入数字再输入非数字，message 只会绑定前面的数字，后面的非数字无法绑定到 message 上；如果先输入非数字，那么 number 修饰符会失效。

```vue
<input type="text" v-model.number="message">
```

**trim：** 去掉前后的空格。

```vue
<input type="text" v-model.trim="message">
```

## 7 v-bind

在开发的时候，有时候标签的属性不是写死的，而是根据一些数据动态决定的，那么就要使用 `v-bind` 来动态绑定属性。

如下 `v-bind:src="imgSrc"` 可以简写成 `:src="imgSrc"`。

```vue
<img v-bind:src="imgSrc" width="500px">
<!-- _blank表示在新标签页打开 -->
<a :href="blogUrl" target="_blank">小畅叙的博客</a>
```

一般使用 `v-bind` 绑定标签的 class 属性和 style 属性。

```vue
<div id="app">
  <p>
    <img v-bind:src="imgSrc" width="500px">
  </p>
  <p>
    <a :href="blogUrl" target="_blank">小畅叙的博客</a>
  </p>
  <!-- :class="className"跟class="classNameA"效果一样，但是第一种方法可以使用判断 -->
  <div :class="className">绑定一个class</div>
  <div :class="[classNameA, classNameB]">绑定class数组</div>
  <div :class="hasClass?classNameA:classNameB">三元运算符绑定class</div>
  <div :style="style">绑定style对象</div>
  <div :style="{color:color,fontSize:fontSize}">单个绑定style</div>
  <div>
    <input type="checkbox" id="hasClass" v-model="hasClass">
    <label for="hasClass">默认/绿色</label>
  </div>
  <div :class="{classNameA: hasClass}">通过判断绑定class</div>
</div>
<style>
  .classNameA {
    color: lightseagreen;
  }

  .classNameB {
    font-size: 18px;
    color: lightsalmon;
  }
</style>
<script type="text/javascript">
  var app = new Vue({
    el: '#app',
    data: {
      imgSrc: 'https://xxx.jpg',
      blogUrl: 'http://zoeeying.github.io',
      className: 'classNameA',
      hasClass: false,
      classNameA: 'classNameA',
      classNameB: 'classNameB',
      style: {
        color: 'blue',
        fontSize: '30px'
      },
      color: 'purple',
      fontSize: '40px'
    }
  })
</script>
```

## 8 其它指令

`v-pre` 指令用于原样输出，如下，表示会把 `{{messag}}` 显示在页面上，而不是把 message 的值渲染出来。

```vue
<div v-pre>{{message}}</div>
```

`v-cloak` 指令表示页面渲染完成才显示标签里面的内容。

```vue
<div v-cloak>页面渲染完成才会显示</div>
```

`v-once` 指令表示只渲染一次。

```vue
<div v-once>{{message}}</div>
<input type="text" v-model="message">
```