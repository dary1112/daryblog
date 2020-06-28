---
title: 前端模块化之AMD — requirejs的使用
date: 2020-06-29 17:59

categories:
- 大前端
tags:
- JavaScript

---

**本篇聊聊前端模的块化规范，其中比较常用的AMD规范以及其代表作`requrie.js`**。

<br>

@[toc]

<br>



# 前端模块化

前端代码逻辑越来越复杂

需要把逻辑做一个合理拆分

function  --> class（高内聚低耦合） --> module

可以把需要重复使用的功能封装成模块，一个页面有一个统筹全局的对象把所有模块引入进来，最终形成一个产品，做成一个完整的项目

JS本身在ES6以前没有模块化的，ES6有模块化了，但是浏览器还不支持



## 前端模块化规范

#### AMD

​	依赖前置：提前引入，文件开头把需要的模块一次性全部引入，后面直接使用

​	前期消耗比较大，后期执行效率很高

​	代表作是 require.js

#### CMD

​	按需加载：在代码执行过程当中需要一个模块了才去加载

​	整个曲线比较平缓

​	代表作是sea.js，但是现在已经不用了

#### ES6 Module

 	浏览器都还不支持，但是可以借助像[webpack](https://www.webpackjs.com/  'https://www.webpackjs.com/')这样的打包工具来实现打包，从而使浏览器可以运行代码。详细用法可参照我的另一篇文章[锋利的ES6 — Module](http://www.xiongdalin.com/2020/06/16/ES6-module/ 'http://www.xiongdalin.com/2020/06/16/ES6-module/') 。



> 另外：后端Node遵循commonJS规范，module.exports 导出模块，require引入模块。

