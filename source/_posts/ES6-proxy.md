---
title: 锋利的ES6 — Proxy
date: 2020-03-23 12:11

categories:
- 大前端
tags:
- JavaScript

---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊Proxy。**

<br>

@[toc]

<br>

## Proxy由来

Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

首先，对于对象，ES委员会为对象定义了一个由14种方法组成的集合，它是适用于所有对象的通用接口，你可以调用、删除或覆写普通方法，但是无法操作内部方法。 下面让我们来看一下这14个方法，[[]]代表内部方法，在一般代码中不可见。



1. 获取对象的原型时调用，在执行obj[__proto__]或Object.getPrototypeOf(obj)时调用

   ```js
    [[GetPrototypeOf]]()
   ```

   <br>

2. 设置一个对象的原型时调用，在执行obj.prototype=otherObj或则Object.SetPrototypeOf(v)的时候调用

   ```js
   [[SetPrototypeOf]](V)  
   ```

   <br>

3. 获取对象的可扩展性时调用，执行Object.isExtensible(object)时被调用

   ```js
   [[IsExtensible]]()  
   ```

   <br>

4. 获取自有属性时调用

   ```js
   [[GetOwnProperty]](P)  
   ```

   <br>

5. 扩展一个不可扩展的对象时调用

   ```js
   [[PreventExtensions]]()
   ```

   <br>

6. 定义自有属性时调用

   ```js
   [[DefineOwnProperty]](P, Desc)
   ```

   <br>

7. 检测对象是否存在某个属性时调用，如key in obj

   ```js
   [[HasProperty]](P)
   ```

   <br>

8. 获取属性时调用，如obj.key，obj[key]

   ```js
   [[Get]](P, Receiver)
   ```

   <br>

9. 为对象的属性赋值时调用，如obj.key=value或obj[key]=value

   ```js
   [[Set]] ( P, V, Receiver)
   ```

   <br>

10. 删除某个属性时调用

    ```js
    [[Delete]](P)
    ```

    <br>

11. 列举对象的可枚举属性时调用，如for (var key in obj)

    ```js
    [[Enumerate]]()
    ```

    <br>

12. 列举对象的自有属性时调用

    ```js
    [[OwnPropertyKeys]]()
    ```

    <br>

13. 调用一个函数时被调用，functionObj()或者x.method()

    ```js
    functionObj.[[Call]](thisValue, arguments)
    ```

    <br>

14. 使用new操作的时候调用，如new Date()

    ```js
    constructorObj.[[Construct]](arguments, newTarget)  
    ```

    <br>



在整个 ES6 标准中，只要有可能，任何语法或对象相关的内建函数都是基于这14种内部方法构建的 。但是我们不必记住这些对象的内建属性，我们更应关注是handler与之相对应的方法。



## Proxy介绍

那么，什么是“代理”呢？我们可以这样说，代理Proxy是一个构造函数，它可以接受两个参数：目标对象（target） 与句柄对象（handler） ，返回一个代理对象Proxy，主要用于从外部控制对对象内部的访问。

```javascript
let p = new Proxy(target, handler)
```

>target：用`Proxy`包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）

> handler：一个对象，其属性是当执行一个操作时定义代理的行为的函数

举个例子：

```js
var person = {}
new Proxy(person, {
    get: function (target, key) {
        return target[key]
    },
    set: function (target, key, value) {
        target[key] = value
        console.log('target[key] is setted')
    }
})
```

其实细心的朋友可以发现，这样的用法跟`Object.defineProperty`很相似，因此想要实现响应式渲染用`Proxy`也会非常方便。



## Proxy用途

**vue3中的响应式原理就是使用Proxy。**

```javascript
var person = {
    name: 'Dary'
}
new Proxy(person, {
    get: function (target, key) {
        return target[key]
    },
    set: function (target, key, value) {
        target[key] = value
        renderMain()
    }
})
renderMain()
function renderMain () {
    document.querySelector('h1').innerHTML = v
}
```



而且`Proxy`除了能使用`get`和`set`以外，可以覆盖上面列举的14个所有方法。比如下面的例子可以实现私有变量：

```js
var api = {
	_secret: 'xxxx',
	_otherSec: 'bbb',
	ver: 'v0.0.1'
}

api = new Proxy(api, {
  get: function(target, key) {
    // 以 _ 下划线开头的都认为是 私有的
    if (key.startsWith('_')) {
      console.log('私有变量不能被访问')
      return undefined
    }
    return target[key]
  },
  set: function(target, key, value) {
    if (key.startsWith('_')) {
      console.log('私有变量不能被修改')
      return
    }
    target[key] = value
  },
  has: function(target, key) {
    return key.startsWith('_') ? false : (key in target)
  }
});

api._secret; // 私有变量不能被访问
console.log(api.ver); // v0.0.1
api._otherSec = 3; // 私有变量不能被修改
console.log('_secret' in api); // false
console.log('ver' in api); // true
```



**`Proxy`的用途还很多，比如**

* `handler.deleteProperty()` 方法用于拦截对对象属性的 [`delete`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/delete) 操作

  ```javascript
  var p = new Proxy(target, {
  	deleteProperty: function(target, key) {
          // name属性不能通过delete关键字删除
          if (key === 'name') return false
          return true
  	}
  })
  ```

  

* `handler.keys()` 用于拦截对对象的 [`Object.keys()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)操作

  ```javascript
  var p = new Proxy(target, {
  	ownKeys: function(target) {
          // 返回一个数组
  	}
  })
  ```

  

* handler.construct()用于来接new操作

  ```js
  var p = new Proxy(target, {
      construct: function(target, argumentsList, newTarget) {
          // 返回一个对象
  	}
  })
  ```

  

