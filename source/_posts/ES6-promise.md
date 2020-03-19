---
title: 锋利的ES6 — Promise
date: 2020-03-19 16:23

categories:
- 大前端
tags:
- JavaScript

---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊Promise。**

<br>

@[toc]

<br>

## 处理异步

在Javascript的世界里，所有代码都是单线程执行的。

由于这个“缺陷”，导致JavaScript的所有网络操作，浏览器事件，都必须是异步执行。异步执行可以用回调函数实现，但是如果代码中异步代码比较多，也就意味着需要写很多回调函数，这样的代码难以读懂，维护困难，而且不利于复用。

我们希望不用写太多回调，而是也可以像同步代码一样链式执行，而Promise就是为了这种情况而生的。



## Promise介绍

古人云：“君子一诺千金”，这种“承诺将来会执行”的对象在JavaScript中称为Promise对象，**Promise是对将来会执行的代码的处理**。

Promise有各种开源实现，在ES6中被统一规范，由浏览器直接支持。

Promise本身并不能实现任何业务，我们之所以要使用Promise只是为了不写回调，为了把本来异步的代码写成同步的形式，我们先来看一个简单的Promise处理异步的例子：承诺一秒之后生成的随机数大于0.5

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

变量`pro`是一个Promise对象，它负责执行参数里的函数，函数带有 `resolve` 和 `reject` 两个参数，`resolve` 和 `reject` 函数被调用时，分别将promise的状态改为*fulfilled（*完成）或rejected（失败）。

> 注意：resolve或reject调用的时候自定义参数只能传一个，如果要传递多个则使用对象的方式传递。



**Promise内部只负责判断和修改状态，并不需要执行具体的逻辑，承诺兑现的逻辑在`then`里执行，承诺失信的逻辑在`catch`里执行。**



## Promise详解

创建Promise对象

```javascript
var pro = new Promise( executor )
```

> executor: 是一个函数，该函数在创建Promise对象的同时被调用执行。

> executor语法：function(resolve, reject) {...}

> * resolve：将Promise对象状态修改为 fulfilled，可以传递参数到then方法的第一个函数中
> * reject：将Promise对象状态修改为 rejected，可以传递参数到 then 方法的第二个函数或catch方法的第一个函数中



Promise有三种状态

* Pending（进行中，初始状态，既不是成功，也不是失败状态）
* Resolved（又称 Fulfilled，意味着操作成功完成）
* Rejected（意味着操作失败）

只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是 Promise 这个名字的由来，它的英语意思就是「承诺」，表示其他手段无法改变。

因为 `Promise.prototype.then` 和 `Promise.prototype.catch` 方法返回promise 对象， 所以它们可以被**链式调用。**

![promise](/img/article/promise.png 'promise')



## Promise改造ajax

Promise更多的时候都是用于处理ajax请求

```javascript
new Promise((resolve, reject) => {
    var xhr = new XMLHttpRequest()
    xhr.open('get', url)
    xhr.send()
    xhr.onreadystatechange = function () {
        if (xhr.readyState === 4) {
            if (xhr.status === 200) {
                resolve(xhr.responseText)
            } else {
                reject()
            }
        }
    }
}).then(resp => {
    console.log(resp)
}).catch(() => {
    console.log('fail')
})
```

这样就可以在`then`里处理请求成功之后的逻辑，不需要写回调函数了。



## 嵌套Promise展开

我们再来看一个例子

```javascript
new Promise((resolve, reject) => {
	console.log('one')
    setTimeout(() => {
        resolve(100)
    }, 1000)
}).then(value => {
    new Promise((resolve, reject) => {
        console.log('two')
        setTimeout(() => {
            resolve(value + 10)
        }, 1000)
    }).then(value => {
        console.log('three', value)
    })
})
```

此时最好将其展开，也是一样的结果，而且会更好读

```javascript
new Promise((resolve, reject) => {
	console.log('one')
    setTimeout(() => {
        resolve(100)
    }, 1000)
}).then(value => {
    return new Promise((resolve, reject) => {
        console.log('two')
        setTimeout(() => {
            resolve(value + 10)
        }, 1000)
    })
}).then(value => {
    console.log('three', value)
})
```

第一个then返回的promise实例承诺兑现可以在第二个then里继续处理，这样的链式操作更加清爽



## Promise.all

这个方法返回一个新的promise对象，该promise对象在数组里所有的promise对象都成功的时候才会触发成功，一旦有任何一个iterable里面的promise对象失败则立即触发该promise对象的失败。

```javascript
var promise1 = Promise.resolve(3);
var promise2 = 42;
var promise3 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 100, 'foo');
});

Promise.all([promise1, promise2, promise3]).then(function(values) {
    // values就是这三个promise resolve的时候传递过来的参数构成的数组
    console.log(values);
});
```



## Promise.race

Promise.race() 类似于Promise.all() ，区别在于它有任意一个完成就算完成。

```javascript
let p1 = new Promise(resolve => {
    setTimeout(() => {
        resolve('I\`m p1 ')
    }, 1000)
})
let p2 = new Promise(resolve => {
    setTimeout(() => {
        resolve('I\`m p2 ')
    }, 2000)
})
Promise.race([p1, p2]).then(value => {
    // 这里的value就是第一个resolve的参数
    console.log(value) // I`m p1
})
```



## Promise.resolve

```javascript
const pro = Promise.resolve(123);

pro.then(function(value) {
  console.log(value) // 123
})
```



## Promise.reject

```javascript
const pro = Promise.reject(new Error('abc'));

pro.then(() => {
  // 不会走这里
}).catch(err => {
    console.log(err) // Error: abc
})
```





## 微任务和宏任务

我们先来看这样一道题

```javascript
setTimeout(function(){
    console.log('1')
}, 0)

new Promise(function(resolve){
    console.log('2')
    resolve()
}).then(function(){
    console.log('3')
})
```

先不说答案，先说概念

- macro-task(宏任务)：包括整体代码script，setTimeout，setInterval
- micro-task(微任务)：Promise，process.nextTick

进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。这句话读起来有些生涩，让我们来分析一下上面代码的运行顺序

1. 这段代码作为宏任务，进入主线程

2. 先遇到`setTimeout`，那么将其回调函数注册后分发到宏任务异步队列里

3. 接下来遇到了`Promise`，`new Promise`立即执行，`then`函数分发到微任务异步队列里

4. 遇到`console.log('2')`，立即执行

5. 整体代码script作为第一个宏任务执行结束，看看有哪些微任务？我们发现了`then`在微任务队列里面，所以这时`then`里的`console.log('3')`执行

6. ok，第一轮`Event Loop`结束了，我们开始第二轮，当然要从宏任务队列开始。我们发现了宏任务中`setTimeout`对应的回调函数，立即执行`console.log('1')`。

7. **所以，这道题的最终执行结果为 2、3、1**

   

> **总结一下：js代码分为宏任务和微任务，宏任务包括整体代码script，setTimeout，setInterval，微任务包括Promise，process.nextTick。代码首先进入整体宏任务，整体宏任务执行完成以后开始执行微任务，当所有微任务完成以后再次查找宏任务，然后依次循环执行。**

