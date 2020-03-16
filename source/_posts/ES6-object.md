---
title: 锋利的ES6 — 对象API
date: 2020-03-16 14:56

categories:
- 大前端
tags:
- JavaScript

---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊对象的API并对todolist做一个重大更新，文末附有Git地址。**

<br>

@[toc]

<br>



## assign（ES6）

合并多个对象

```javascript
const target = { a: 1, b: 2 }
const source = { b: 4, c: 5 }

const returnedTarget = Object.assign(target, source)

console.log(target)  //  { a: 1, b: 4, c: 5 }

console.log(returnedTarget)  // { a: 1, b: 4, c: 5 }
```



## is（ES6）

判断两个值是否相等，甚至可以直接判断两个NaN

```javascript
Object.is('foo', 'foo')     // true
Object.is(window, window)   // true

Object.is('foo', 'bar')     // false
Object.is([], [])           // false

var foo = { a: 1 }
var bar = { a: 1 }
Object.is(foo, foo)         // true
Object.is(foo, bar)         // false

Object.is(null, null)       // true

// 特例
Object.is(0, -0)            // false
Object.is(+0, -0)           // false
Object.is(0, +0)            // true
Object.is(-0, -0)           // true
Object.is(NaN, 0/0)         // true
```



## entries（ES2017）

返回键值对数组，跟for...in的顺序一致

```javascript
const obj = { foo: 'bar', baz: 42 }
console.log(Object.entries(obj)) // [ ['foo', 'bar'], ['baz', 42] ]
```



## fromEntries（ES2019草案）

将键值对数组转换成对象

```javascript
Object.fromEntries([ ['foo', 'bar'], ['baz', 42] ]) // { foo: 'bar', baz: 42 }
```



## defineProperty、defineProperties（ES6）

给对象定义新属性或者修改现有属性

```javascript
var obj = {}
Object.defineProperty(obj, "name", {
  enumerable: false, // 配置不可枚举，name属性就不能通过 for...in 遍历到
  configurable: false, // 配置不可被删除，name不能通过delete运算符删除
  writable: false, // 配置不可写，属性不能被重新赋值或修改
  value: "dary" // 属性值为dary
})
```

**defineProperties** 跟defineProperty的作用是一样的，它可以一次定义多个属性，每个属性里的配置项都一样使用即可

```javascript
var obj = {};
Object.defineProperties(obj, {
  'name': {
    value: 'dary',
    writable: true
  },
  'age': {
    value: 19,
    writable: false
  }
});
```

但是，这里要说的不仅是这个用法，还有**defineProperty** 的另外一个更高级的用法，那就是`get`和`set`

```javascript
var obj = {}
var v = undefined
Object.defineProperty(obj, "name", {
    get: function () {
        return v
    },
    set: function (value) {
        v = value
    }
})
```

我们可以在第三个参数里写`get`和`set`，这两个方法分别会在我们获取obj的name属性值和设置obj的name属性时被调用。

* 当我们获取`obj.name`的时候实际上取到的是`get`的返回值
* 当我们给`obj.name`属性赋值的时候，赋的值就是`set`方法的参数`value`，我们在`set`方法里把`value`赋值给`v`，这样再次获取`obj.name`的时候得到的就是我们刚刚赋的值了

那么，这个东西有什么用呢？其实非常有用，我们可以在`set`方法里面写一些自己的代码，比如：

```java
var obj = {}
var v = undefined
Object.defineProperty(obj, "name", {
    get: function () {
        return v
    },
    set: function (value) {
        v = value
        console.log('obj.name is setted')
    }
})
```

![defineProperty](/img/article/defineproperty.png 'defineProperty')

这个时候我们可以看到，每当我们给`obj.name`赋值的时候都会打印`obj.name is setted`，也就是说我们可以把`obj.name` 属性值发生变化时要做的操作放在`set`方法里，这样就会变得非常方便，比如我要把`obj.name`渲染到页面上，代码可以这么写了

HTML

```html
<h1></h1>
```

JavaScript

```javascript
var obj = {}
var v = 'dary'
Object.defineProperty(obj, "name", {
    get: function () {
        return v
    },
    set: function (value) {
        v = value
        renderMain()
    }
})
renderMain()
function renderMain () {
    document.querySelector('h1').innerHTML = v
}
```

![控制台运行截图](/img/article/defineproperty2.png '控制台运行截图')

我们再尝试修改`obj.name`的属性值

![修改之后的截图](/img/article/defineproperty3.png '修改之后的截图')

我们可以看到，只需要修改属性值页面会自动完成渲染，十分方便。

这样的做法我们可以移植到**todolist**案例中

```javascript
var obj = {}
Object.defineProperty(obj, 'todos', {
    get () {
        return todos
    },
    set (value) {
        todos = value
        // 每一次todos被修改都调用render方法
        render()
    }
})
```

至此，我们把代码中的`todos`都改成`obj.todos`，并且只需要在页面初始调用一次`render()`，其他地方的渲染都会在`set`方法里直接运行，不需要每次调用。

**这样我们就可以集中精力在我们的业务逻辑上而不用担心页面渲染了**

但是，现在我们会发现一个问题，就是添加事项添加不上了，因为我们原本用的是

```javascript
todos.push(todo)
```

而`push`修改的是源数组，这样并不会触发`obj`的`set`方法，解决方案如下：

```javascript
obj.todos = obj.todos.concat([todo])
```



> todolist（待办事项列表）案例将在我的github上持续更新

> 地址：https://github.com/dary1112/todolist.git

> 欢迎star！！！

