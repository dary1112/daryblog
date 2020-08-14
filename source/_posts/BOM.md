---
title: Javascript中BOM的介绍和用法
date: 2020-08-14 16:39

categories:
- 大前端
tags:
- JavaScript

---

**本文介绍BOM的定义以及window对象的一些详细介绍。**

<br>

@[toc]

<br>

## 前言

浏览器对象模型（Browser Object Model, BOM）被广泛应用于 Web 开发之中，主要用于客户端浏览器的管理。

BOM 概念比较古老，但是一直没有被标准化，不过各主流浏览器均支持 BOM，都遵守最基本的规则和用法，W3C 也将 BOM 主要内容纳入了 HTML5 规范之中。



![寄居蟹](/img/article/BOM-寄居蟹.jpg '寄居蟹')

Javascript是一门脚本语言，它的运行需要依赖浏览器，所以我们也称它为宿主语言，浏览器就是它的寄宿对象，就跟寄居蟹一样。那么作为寄居蟹他是不能独立生存的，需要依赖螺壳为寄体，那对于寄居蟹而言它就需要明确知道它需要的螺壳有多大，什么形状，是否坚硬等信息。作为Javascript来讲，同样的道理，要在浏览器里运行，就需要知道浏览器相关的一些信息，所以，寄居蟹之于螺壳就等同于Javascript之于浏览器。



## 定义

那么Javascript怎么访问浏览器相关的一些信息呢？自然要通过对象了，因为Javascript是一门**面向对象**的语言。因此，**Javascript就把跟浏览器相关的信息抽象成对象模型，就叫浏览器对象模型（Browser Object Model），就是BOM了。BOM只是一个概念，BOM的核心也是顶级对象是window，因此，使用BOM就是真正使用的是window。**



## window

1. window是全局浏览器内置顶级对象
2. 全局变量和全局函数默认是挂在window下的
3. window上的各种属性，比如：name、length、top，一般不要用作全局变量
4. window.innerWidth 获取浏览器内容宽度
5. window.innerHeight  获取浏览器内容高度

### window下的子对象

1. location：地址栏

   location对象里有很多属性，这些属性想要明白意思就直接在控制台打印就行

   location.reload() 刷新，参数传true是强制刷新（清除缓存）

   location.replace()  replace是替换的意思，页面跳转

2. navigator 这个对象里可以获取浏览器相关的信息，但是以前的各种属性已经在逐渐被抛弃了

   navigator.userAgent  返回浏览器信息（可用此属性判断当前浏览器）

   判断当前浏览器类型的代码：这段代码拿去用就行

   ```javascript
   function isBrowser() {
       var userAgent = navigator.userAgent;
       //微信内置浏览器
       if(userAgent.match(/MicroMessenger/i) == 'MicroMessenger') {
           return "MicroMessenger";
       }
       //QQ内置浏览器
       else if(userAgent.match(/QQ/i) == 'QQ') {
           return "QQ";
       }
       //Chrome
       else if(userAgent.match(/Chrome/i) == 'Chrome') {
           return "Chrome";
       }
       //Opera
       else if(userAgent.match(/Opera/i) == 'Opera') {
           return "Opera";
       }
       //Firefox
       else if(userAgent.match(/Firefox/i) == 'Firefox') {
           return "Firefox";
       }
       //Safari
       else if(userAgent.match(/Safari/i) == 'Safari') {
           return "Safari";
       }
       //IE
       else if(!!window.ActiveXObject || "ActiveXObject" in window) {
           return "IE";
       }
       else {
           return "未定义:"+userAgent;
       }
   }
   ```

3. history（页面必须有历史纪录才能使用这些方法）

   history.go(1)  参数可写任意整数，正数前进，负数后退

   history.back()  后退一步

   history.forward() 前进一步

4. screen: 屏幕，用的很少

   window.screen.width 返回当前屏幕宽度(分辨率值)

   window.screen.height 返回当前屏幕高度(分辨率值)

### window下的弹框方法

- alert() 警告框
- prompt() 输入框
- confirm() 确认框

### 定时器

- 超时定时器

  setTimeout 打开定时器

  clearTimeout  清除定时器

- 间隔定时器   

  setInterval 打开定时器

  clearInterval 清除定时器

不管是哪种定时器都可以用一个变量接收定时器的id，清除的时候用id来清除

### window的各种事件

- window.onload 页面加载完成

- window.onscroll 页面滚动

  var scrolltop=document.documentElement.scrollTop||document.body.scrollTop; //兼容

  window.scrollTo(x, y)  // 设置滚动条位置，x代表横坐标，y代表纵坐标

- window.onresize 窗口大小改变