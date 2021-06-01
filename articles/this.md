---
title: 详解javascript中的this指向问题
date: 2021-03-23 12:11

categories:
- 大前端
tags:
- JavaScript

---

**javascript中的this有很多种不同场景，其实也只有一个场景。**

<br>

@[toc]

<br>

先说结论：**this指向当前环境栈调用对象。**

再来展开：

## 一、全局this

```js
console.log(this) // window
```

直接打印全局this，当然就是`window`啦。



## 二、全局函数this

```js
function foo () {
  console.log(this)
}
foo() // window
```

全局函数里的this也是`window`。





