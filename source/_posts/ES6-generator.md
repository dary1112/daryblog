---
title: 锋利的ES6 — Generator
date: 2020-05-21 17:55

categories:
- 大前端
tags:
- JavaScript

---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊Generator 函数生成器。**

<br>

@[toc]

<br>

首先，在阅读本篇文章之前你需要非常了解**Promise**，如果不了解，可以先移步我的另外一篇**[锋利的ES6 — Promise](http://www.xiongdalin.com/2020/03/19/ES6-promise/)**。

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。



## 概念

Generator 函数有多种理解角度。语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

形式上，Generator 函数是一个普通函数，但是有两个特征

1.  `function`关键字与函数名之间有一个星号，* 前后的空格都是可有可无的，ES6并没有对此做限制，也就是说下面的写法都是正确的。

   ```javascript
   function* foo () {}
   function *foo () {}
   function * foo () {}
   function*foo () {}
   ```

2.  函数体内部使用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”）



## 案例

来一起看看下面代码：

```javascript
function* foo() {
    yield 1
  	yield 2
  	return 3
}
var bar = foo()
```

上面函数定义了一个Generator函数`foo`，它内部有两个`yield`表达式（1和2），即该函数有三个状态：1，2和 return 3（结束执行）。

然后，Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。**但是，这里调用 foo后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，现在`bar`就是这个对象。**

下一步，必须调用遍历器对象的`next`方法，使得指针移向下一个状态。也就是说，每次调用`next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`表达式（或`return`语句）为止。换言之，Generator 函数是分段执行的，`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行。

调用如下：

```javascript
bar.next()  // { value: 1, done: false }
bar.next()  // { value: 2, done: false }
bar.next()  // { value: 3, done: true }
bar.next()  // { value: undefined, done: true }
```

这里调用了四次`next`，每一次调用都会得到`yield`表达式的值以及完成状态构成的对象，此时对象里的`done`属性是`false`，直到最后一次得到返回值，调用结束`done`变为`true`，如果此时继续调用`next`，那么会得到`value`为`undefined`。



## yield详解

`yield`表达式其实是暂停标志，当函数运行到`yield`表达式的时候函数就会就会暂停，并将表达式后面的值作为将来`next`返回值对象里的`value`值，当下一次调用`next`的时候函数才会继续往下执行，直到遇见下一个`yield`，如果没有`yield`了，就会把`return`返回值作为返回对象的`value`。函数没有返回值的话`value`就是`undefined`。

需要注意的是，`yield`表达式后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行，因此等于为 JavaScript 提供了手动的“惰性求值”（Lazy Evaluation）的语法功能

```javascript
function* foo () {
    yield 2 + 3
}
```

这里`yield`表达式后面的`2+3`并不会在调用foo的时候执行，而是将来调用`next`的时候指针移到这里才会执行。

## next详解

`yield`表达式本身没有返回值，也就是`undefined`，但是当`next`执行的时候可以传递过来一个参数，这个参数就会作为这个`yield`的返回值。

```javascript
function* foo () {
  var n = 0
  while (true) {
    n++
    var isEnd = yield n
    if (isEnd) break
  }
}
var bar = foo()
bar.next() // { value: 1, done: false }
bar.next() // { value: 2, done: false }
bar.next(true) // { value: undefined, done: true }
```

上面定义了一个循环，如果`next`不传参数，那么`isEnd`永远都是`undefined`，循环就会成为死循环，每次`next`得到的value值都会加1，当`next`方法带一个参数`true`时，变量`isEnd`就被赋值为`true`，这个时候进入if，循环结束，函数没有返回值，所以`done`也为true。

## 处理Ajax

接下来我们康康如何用`Generator`处理异步的`ajax`

```javascript
function* gen (url) {
  yield fetch(url)
}
var g = gen('https://jsonplaceholder.typicode.com/todos/1')
// result拿到的就是fetch的返回值对象，value属性就是一个Promise
var result = g.next()
result.value.then(function(data){
  return data.json()
}).then(function(data){
  console.log(data) // 在这里就可以得到接口响应的值
})
```

看起来有些鸡肋，所以后面ES2017 标准引入了 async 函数，使得异步操作变得更加方便。async 其实就是`Generator`的语法糖。敬请移步[锋利的ES6 — Async Await](http://www.xiongdalin.com/2020/05/28/ES6-async-await/)。





