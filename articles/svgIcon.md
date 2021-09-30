---
title: 在vue项目中更优雅的使用svg图标
date: 2021-08-26 16:21

categories:
- 大前端
tags:
- html5
- css3
- vue

---

**项目中的svg自定义图标可以在项目中构建字体图标，使用更为方便**

<br>

@[toc]

<br>

项目开发时我们会用到一些UI库，比如PC端常用的[elementUI](https://element.eleme.io/)，移动端常用的[vantUI](https://vant-contrib.gitee.io/vant)、[mintUI](http://mint-ui.github.io)等。一般很多UI库都会有icon组件，也就是组件库提供的图标，但是往往这些图标并不能满足我们饥渴的设计师，就会有很多自定义的图标。那前端怎么使用呢？可能你会希望设计师切图，也就是png，当然可以实现，但是不够优雅，而且麻烦，那么设计师给svg文件也是很常用的，如何更加优雅的使用svg文件呢？本文带你来看看。

