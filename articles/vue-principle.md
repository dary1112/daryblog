---
title: Vue原理
date: 2020-03-23 12:11

categories:
- 大前端
tags:
- JavaScript
- vue

---

**文章介绍**

<br>

@[toc]

<br>

核心点 object.defineProperty

在 Vue 初始化数据的时候，会对 data 中的属性使用 object.defineProperty 重新定义，当页面获取到相应的属性时，会进行依赖收集(收集当前组件的 watcher)，如果属性发生变化则会通知相关依赖进行更新操作

Vue 初始化时，调用 initData ()方法，初始化用户传入的 data 数据

拿到 data 数据之后，对数据进行观测，new Observe 创建一个观测类

如果数据是个非数组数据，会调用 this.walk(value)方法，进行对象的处理

内部会把数据进行遍历，用 defineReactive 重新定义，循环对象属性响应式变化

最后使用 object.defineProperty 重新定义数据

如果数据是个数组 

使用函数劫持的方式重写数组的方法

Vue 将 data 中的数组 进行了原型链重写

![vue原理](/img/article/vue-principle.png 'vue原理')





首先是观察者，observer他利用obj defineproperty去拿到data依赖，然后遍历子集依赖，set拿到所有子依赖，就告诉订阅者，watcher，每收集一个子依赖就new一个订阅者，最后订阅者被收集起来，dep就是个收集器，是个集合或者数组。然后通过编译器compile去拿组件里所有我们定义的temeplate dom这里需要区分nodetype，因为vue 的模板或者是指令都是自己定义好的，如v-text双大括号这些，然后和dep里的收集做一个匹配，render到我们index.html定义的app里去。

总结一下就是收集数据依赖，然后装到订阅器里，匹配dom中的指令，进行赋值。
这是双向绑定，然后每次修改数据呢？会有一个dom diff的过程。当我们第一次渲染dom的时候，会把dom转成一个vdom对象，是个js对象。当修改数据的时候，会走vue的update钩子，首先通过拿到修改后的数据依赖，生成一份新的vdom对象，和旧的vdom比较，比较是一个逐层比较的过程，走patch方法，相同不管，不同直接新生成一个，把旧的移除，把新的放进去。然后去比较下一层，会有一个updatechildren的过程。children可能会是多个，所以我们给每个孩子定义索引，新旧比较，相同不管，不同新的孩子插入到旧孩子前一个索引下标处，旧孩子移除。前面比较的同时后面也开始比较，一直到startindex大于等于endindex表示比较完了。
然后我们就知道哪里变了，只把变了的vdom render成真正的dom就可以了。
为什么要搞这么复杂呢？原来jq时代也没看出啥问题啊，非说影响效率了。

浏览器渲染呢，先从定义的doctype知道浏览器用哪种格式编译文档，然后把我们写的html语义化标签编译成一个dom树 然后再拿到css组成样式树，这样就可以计算一些宽高，距离，定义一些颜色，最后由上到下渲染我们的html内容。所以老说少用table iframe由于之前jq最爱操作dom，每次js操作dom都会有一个连桥的过程，会影响性能，每次操作dom都需要访问dom又影响性能，dom改变了浏览器直接回流，就是页面再从body从上到下render一遍，如果修改一些宽高样式，还会完成页面重绘，所以就要搞虚拟dom了呗。