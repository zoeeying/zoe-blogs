# Vue 选项

代码地址：<https://github.com/zoeeying/vue-learning>

## 1 propsData 选项

propsData 选项用在全局扩展中传递数据。

```vue
<zoe></zoe>
<script type="text/javascript">
  var zoeExtend = Vue.extend({
    template: "<p>{{message}}! {{extra}}!</p>",
    data: function () {
      return ({
        message: 'Hello Zoe',
      })
    },
    props: ['extra']
  })
  new zoeExtend({ propsData: { extra: 'I am Tyler' } }).$mount('zoe')
</script>
```

## 2 computed 选项

computed 选项的作用主要是对原数据进行改造输出（包括格式的编辑、大小写转换、顺序重排、添加符号等），不污染原始数据。

```vue
<div id="app">
  <p>{{formatPrice}}</p>
  <ul>
    <li v-for="item in reversedNewsList">{{item.date}}-{{item.title}}</li>
  </ul>
</div>
<script type="text/javascript">
  var newsList = [
    { title: '周杰捐2万斤大米', date: '2020年2月3日' },
    { title: '女篮进军奥运会', date: '2020年2月4日' },
    { title: '穿山甲为中间宿主', date: '2020年2月5日' },
    { title: '浙江确诊病例破千', date: '2020年2月6日' },
    { title: '央视元宵特别节目', date: '2020年2月8日' },
  ]
  var app = new Vue({
    el: '#app',
    data: {
      price: 100,
      newsList,
    },
    computed: {
      formatPrice: function () {
        return '￥' + this.price + '元'
      },
      reversedNewsList: function () {
        return this.newsList.reverse()
      }
    },
  })
</script>
```

## 3 methods 选项

用来放一些事件监听函数。通过下面的例子，我们可以学到：如何给 methods 中的方法传参、Vue 内置的事件对象、自定义组件和作用域外部的组件如何调用 methods 中的方法。

```vue
<div id="app">
  <p>计数：{{count}}</p>
  <!-- 传递参数，$event是Vue内置的事件对象，包含所有与事件有关的信息 -->
  <button @click="add(5, $event)">按钮</button>
  <p>
    <!-- 自定义组件通过@click.native调用methods选项中的方法 -->
    <mybtn @click.native="add(2, $event)"></mybtn>
  </p>
</div>
<!-- Vue作用域外部按钮调用methods选项中的方法，注意没有$event参数 -->
<button onclick="app.add(3)">外部按钮</button>
<script type="text/javascript">
  var mybtn = {
    template: `<button>自定义按钮</button>`
  }
  var app = new Vue({
    el: '#app',
    data: {
      count: 0
    },
    components: {
      'mybtn': mybtn
    },
    methods: {
      add: function (num, e) {
        this.count += (typeof num === 'number' ? num : 1)
        console.log(e)
      }
    }
  })
</script>
```

## 4 watch 选项

watch 选项是用来监控数据变化的。下面的例子展示了两种 watch 写法：写在构造器内部、通过实例属性来写。

```vue

<div id="app">
  <p>今日温度：{{temperature}}℃</p>
  <p>穿衣建议：{{dressing}}</p>
  <p>
    <button @click="add">升高温度</button>
    <button @click="reduce">降低温度</button>
  </p>
</div>
<script type="text/javascript">
  var dressingArr = ['T恤短袖', '夹克长裤', '羽绒服']
  var app = new Vue({
    el: '#app',
    data: {
      temperature: 14,
      dressing: dressingArr[1],
    },
    methods: {
      add: function () {
        this.temperature += 5
      },
      reduce: function () {
        this.temperature -= 5
      },
    },
    // 构造器内部watch
    // watch: {
    //   temperature: function (newVal, oldVal) {
    //     if (newVal >= 26) {
    //       this.dressing = dressingArr[0]
    //     } else if (newVal >= 0) {
    //       this.dressing = dressingArr[1]
    //     } else {
    //       this.dressing = dressingArr[2]
    //     }
    //   }
    // }
  })
  // 实例属性watch
  app.$watch('temperature', function (newVal, oldVal) {
    if (newVal >= 26) {
      this.dressing = dressingArr[0]
    } else if (newVal >= 0) {
      this.dressing = dressingArr[1]
    } else {
      this.dressing = dressingArr[2]
    }
  })
</script>
```

## 5 mixins 选项

**混入选项有两种用途：**

a. 写好构造器后，如果这时需要增加方法时，可以使用 mixins 选项，减少源代码的污染。

b. 对于公用方法，可以使用 mixins 选项，减少代码量，实现代码重用。

如下代码，三个 updated 生命周期函数的执行顺序：Vue.mixin 中的 updated > addMixins 中的 updated > 构造器中的 updated。

**补充：** 注意 mixin 的单复数。

```vue
<div id="app">
  <p>{{num}}</p>
  <button @click="add">增加</button>
</div>
<script type="text/javascript">
  var addMixins = {
    // updated是生命周期函数
    updated: function () {
      console.log('数据发生了变化，变成了' + this.num)
    }
  }
  // 对于公共方法，可以使用全局混入
  Vue.mixin({
    updated: function () {
      console.log('全局混入')
    }
  })
  var app = new Vue({
    el: '#app',
    data: {
      num: 1
    },
    methods: {
      add: function () {
        this.num++
      }
    },
    updated: function () {
      console.log('构造器中的updated')
    },
    mixins: [addMixins]
  })
</script>
```

## 6 extends 选项

```vue
<div id="app">
  <!-- <p>{{num}}</p> -->
  <p>${num}</p>
  <button @click="add">增加</button>
</div>
<script type="text/javascript">
  var extendsObj = {
    // 扩展的updated执行顺序优于构造器中的updated
    updated: function () {
      console.log('扩展的updated')
    },
    methods: {
      // 扩展的方法如果与构造器中的方法名重复，是不会执行的
      add: function () {
        this.num++
        console.log('扩展的add方法')
      }
    },
  }
  var app = new Vue({
    el: '#app',
    data: {
      num: 1
    },
    methods: {
      add: function () {
        this.num++
        console.log('构造器中的add方法')
      }
    },
    updated: function () {
      console.log('构造器中的updated')
    },
    extends: extendsObj,
    delimiters: ['${', '}']
  })
</script>
```

`delimiters: ['${', '}']` 可以把 `{{}}` 插值方式换成 `${}`。
