---
title: 防抖和节流
date: 2020-07-31 19:11

categories:
- 大前端
tags:
- JavaScript

---

**防抖和节流是在前端开发中经常用到的技术，用于对频繁触发的行为进行频率或者次数的限制。**

<br>

@[toc]

<br>

## 简介

在前端开发的过程中，我们经常会需要绑定一些持续触发的事件，如 resize、scroll、mousemove 等等，但有些时候我们并不希望在事件持续触发的过程中那么频繁地去执行函数。

通常这种情况下我们怎么去解决的呢？一般来讲，**防抖**和**节流**是比较好的解决方案。

**防抖是控制次数，节流是控制频率。**



## 防抖

比如：清除定时器、input验证、搜索联想、window.onresize事件都可以使用防抖来避免频繁触发。

思路：首次运行时把定时器赋值给一个变量，第二次执行时，如果间隔没超过定时器设定的时间则会清除掉定时器，重新设定定时器，依次反复，当我们停止下来时，没有执行清除定时器，超过一定时间后触发回调函数。

* 封装运动函数的时候：在开启一个新的定时器之前先清除定时器，这样动画效果就不会叠加了

  ```javascript
  var timer = null
  function animate () {
    clearInterval(timer)
    timer = setInterval(() => {
      // ....
    }, 20)
  }
  ```

* 表单验证的时候：延迟验证，如果300ms以内再次触发keyup事件就清除定时器，只有最后一次输入才会触发验证。

  ```javascript
  input.onkeyup = function () {
    clearTimeout(this.timer)
    this.timer = setTimeout(() => {
      // ...验证逻辑...
    }, 300)
  }
  ```

* 还有如上所说搜索联想、window.onresize事件等都可以使用相同的思路完成防抖，这里就不一一赘述了。

## 节流

比如：scroll、mousemove

思路： 第一次先设定一个变量true，第二次执行这个函数时，会判断变量是否true，是则返回。当第一次的定时器执行完函数最后会设定变量为false。那么下次判断变量时则为false，函数会依次运行。

* 滚轮事件：预设`isScroll`变量代表滚动状态，`false`代表静止，`true`代表正在滚动，进入事件以后先判断是否滚动，如果`isScroll`为`false`，代表处于静止状态，就可以开始执行滚动逻辑，同时把`isScroll`修改为`true`，延迟100ms执行代码之前把定时器清除，在定时器里面也就是100ms以后滚动结束，`isScroll`重新改回为`false`，这样的话在滚动过程中`isScroll`为`true`，不会进入。

  ```javascript
  var isScroll = false
  window.onscroll = function () {
    if (!isScroll) {
      isScroll = true
      clearTimeout(this.timer)
      this.timer = setTimeout(() => {
        // ...逻辑代码...
        isScroll = false
      }, 100)
    }
  }
  ```

* mousemove和滚轮事件一样的逻辑，完成节流。

  ```javascript
  var isMove = false
  div.onmousemove = function () {
    if (!isMove) {
      isMove = true
      clearTimeout(this.timer)
      this.timer = setTimeout(() => {
        // ...逻辑代码...
        isMove = false
      }, 100)
    }
  }
  ```



## 综述

函数防抖：将几次操作合并为一此操作进行。原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话，就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。

函数节流：使得一定时间内只触发一次函数。原理是通过判断是否到达一定时间来触发函数。

区别： 函数节流不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数，而函数防抖只是在最后一次事件后才触发一次函数。 比如在页面的无限加载场景下，我们需要用户在滚动页面时，每隔一段时间发一次 Ajax 请求，而不是在用户停下滚动页面操作时才去请求数据。这样的场景，就适合用节流技术来实现。

## 使用throttle-debounce包

 如果在项目中有需要用到的，可以直接安装单个的NPM模块。[throttle-debounce](https://github.com/niksy/throttle-debounce/ 'https://github.com/niksy/throttle-debounce')





## 使用lodash

