# 2020 年前端面试题

> Q1：是否使用过 Redux？Redux 解决了什么问题？是如何解决的？

Redux 是为了解决 React 组件间通讯和组件间状态共享的问题，主要包括三个部分：store、action、reducer。



> Q2：是否使用过 React Hooks？主要用于解决什么问题？

aaa



> Q3：Vue 兄弟组件通讯有哪些方式？

aaa



> Q4：常见的网络安全攻击有哪些，如何预防？

aaa



> Q5：复杂动画场景中，为什么要提醒它们创建新图层？

aaa



> Q6：在 HTTPS 协议打开的 H5 页面内，有某些 JS 或 CSS 资源会被 block，这是什么原因？图片资源会吗？

aaa



> Q7：JS 异步优化历史、原理是什么？

aaa



> Q8：Cookie 和 Session 的区别？

aaa



> Q9：Node.js 进程间如何通讯？

aaa



> Q10：自己在技术上擅长哪一方面？

aaa



> Q11：Vue 如何实现数据双向绑定?

Vue 数据双向绑定是通过**数据劫持**结合**发布者-订阅者模式**的方式来实现的。

通过 `Object.defineProperty()` 来劫持各个属性的 setter/getter，在数据变动时发布消息给订阅者，触发相应监听回调。当把一个普通 Javascript 对象传给 Vue 实例来作为它的 data 选项时，Vue 将遍历它的属性，用 `Object.defineProperty()` 将它们转为 getter/setter。用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。

Vue 的数据双向绑定将 MVVM 作为数据绑定的入口，整合 Observer、Compile 和 Watcher 三者，通过 Observer 来监听自己的 model 的数据变化，通过 Compile 来解析编译模板指令（`{{}}`），最终利用 Watcher 搭起 Observer 和 Compile 之间的通信桥梁，达到数据变化 => 视图更新；视图交互变化（比如 input）=> 数据model 变更的双向绑定效果。

**补充：** `Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。









