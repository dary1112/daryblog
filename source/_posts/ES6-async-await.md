---
title: 锋利的ES6 — async await
date: 2020-05-28 18:51

categories:
- 大前端
tags:
- JavaScript

---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊async await。**

<br>

@[toc]

<br>

首先，在阅读本篇文章之前你需要非常了解**Promise**，如果不了解，可以先移步我的另外一篇**[锋利的ES6 — Promise](http://www.xiongdalin.com/2020/03/19/ES6-promise/)**。



## 概念

async函数其实是`Generator`的语法糖，详情可以参考**[锋利的ES6 — Generator](http://www.xiongdalin.com/2020/05/21/ES6-generator/)**。`async`函数就是将 Generator 函数的星号（`*`）替换成`async`，将`yield`替换成`await`，仅此而已。`async`函数可以看作多个异步操作，包装成的一个 Promise 对象，而`await`命令就是内部`then`命令的语法糖。



## 优点

1. 内置执行器

   `Generator`的执行是分两步的，必须要先执行函数本身得到一个遍历器对象再通过`yield`来执行，而`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样，只需要直接调用即可。

2. 更好的语义化

   `async`和`await`，比起星号和`yield`，语义更清楚了。`async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果。

3. 更广的适用性

   `async`函数的`await`命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即 resolved 的 Promise 对象）。

4. 返回值是 Promise

   `async`函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用`then`方法指定下一步的操作。




## 用法

先来看看一个简单的`Promise`的例子：

```javascript
const pro = new Promise((resolve, reject) => {
	setTimeout(() => {
		const num = Math.random()
		if (num > 0.5) resolve(num)
		else reject(num)
	}, 1000)
})

pro.then((n) => {
	console.log(n + '大于0.5，承诺兑现')
}).catch((n) => {
	console.log(n + '不大于0.5，承诺失信')
})
```

这种写法虽然不用写回调了，但是仍然需要在`then`和`catch`里面传入函数来处理，倒不是说`Promise`不好，只是`async`的写法更加舒服，简直跟同步写法没有区别。改造如下：

```javascript
function foo () {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const num = Math.random()
      if (num > 0.5) resolve(num)
      else reject(num)
    }, 1000)
  })
}
async function bar () {
  var num = await foo()
  console.log(num + '大于0.5，承诺兑现')
  
    
}
bar()
```

1. **`async`修饰的函数内部才能使用`await`关键字**
2. **`await`表达式后面的函数需要返回一个`Promise`对象**
3. **`await`表达式的结果就是`resolve`传递过来的参数**

但是，当你运行上面的代码的时候你会发现如果`Promise`走了`reject`，代码会报错，因为`await`只接收`resolve`的值不接收`reject`，因此我们可以把代码放进`try...catch`语句中，改造如下：

```javascript
async function bar () {
  try {
      var num = await foo()
      console.log(num + '大于0.5，承诺兑现')
  } catch (n) {
      console.log(n + '不大于0.5，承诺失信')
  }
    
}
```



## 处理Ajax

```javascript
function get (url, query, isJson = true) {
  if (query) {
    url += '?'
    for (var key in query) {
      url += `${key}=${query[key]}&`
    }
    url = url.slice(0, -1)
  }
  return new Promise((resolve, reject) => {
    var xhr = new XMLHttpRequest()
    xhr.open('get', url)
    xhr.send()
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status === 200) {
          resolve(isJson ? JSON.parse(xhr.responseText) : xhr.responseText)
        } else {
          reject()
        }
      }
    }
  })
}

async function getTodos () {
    try {
        const res = await get('https://jsonplaceholder.typicode.com/todos')
        console.log(res)
    } catch (e) {
        throw(e)
    }
}
getTodos()
```

也可以在一个`async`函数里使用多个`await`

```javascript
async function getTodos () {
    try {
        const res1 = await get('https://jsonplaceholder.typicode.com/todos')
        const res2 = await get('https://jsonplaceholder.typicode.com/todos')
        console.log({ res1, res2 })
    } catch (e) {
        throw(e)
    }
}
getTodos()
```

多个`await`会等到第一个`await`异步完成以后才会执行第二个`await`。任意一个异步失败都会进入`catch`。

但是，这种写法的话两个异步代码会存在前后顺序关系，第一个异步`resolve`之后第二个才会开始发送，为了节约时间我们可以换一种写法：

```javascript
async function getTodos () {
  const [ res1, res2 ] = await Promise.all(
    get('https://jsonplaceholder.typicode.com/todos'),
    get('https://jsonplaceholder.typicode.com/todos')
  )
  console.log({ res1, res2 })  
}
getTodos()
```

或者：

```javascript
async function getTodos () {
    try {
        const pro1 = get('https://jsonplaceholder.typicode.com/todos')
        const pro2 = get('https://jsonplaceholder.typicode.com/todos')
        const res1 = await pro1
        const res2 = await pro2
        console.log({ res1, res2 })
    } catch (e) {
        throw(e)
    }
}
getTodos()
```

**这两种写法都是并行执行多个异步，这样就会缩短程序的执行时间。**









