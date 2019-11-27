---

title: 面向对象 — 从基本介绍到构造函数
date: 2019-11-10 00:45

categories:

- 大前端

tags:

-  JavaScript

---

**本文旨在介绍面向对象， 描述 JavaScript 当中面向对象编程的一些概念**

<br>

在说JavaScript的面向对象之前，我们先来介绍一下面向对象（Object  Oriented）。首先，对象的含义是指具体的某一个事物，即在现实生活中能够看得见摸得着的事物。在面向对象程序设计中，对象包含两个含义，其中一个是数据（属性），另外一个是动作（方法）。对象则是数据和动作的结合体，也就是说，**对象的本质就是属性和方法的集合。**

大家都知道， JavaScript的核心是支持面向对象的，它也提供了强大灵活的面向对象编程语言能力。

但是JavaScript从本质上讲，我们只能说它支持面向对象，但是这并不意味着JavaScript本身就是一门面向对象的语言，从一个非常简单的角度来理解这个问题：JavaScript设计之初是Netscape公司搭载在 Navigator 2.0里面用于处理简单的数据验证的客户端脚本语言，ECMAScript在2015以前甚至都没有class类的概念（虽然ECMAScript2015新增了class的概念，但它也只是一个语法糖）。

在面向对象的世界里，我们可以通过类来实例化一个对象，这里有两个概念：**类是对象的抽象，对象是类的实例**。举个例子：当我们说“人”的时候并不能指定哪一个具体的人，所以“人”是一个抽象，是一个类，但是当我们说到 “Dary”的时候，就知道这是谁了，所以Dary是一个实例，是对“人”这个类的具体实现，这个过程我们称之为实例化。

但是比较遗憾的是，JavaScript在ECMAScript2015以前没有“类”的概念，我们创建对象可以通过另外两种方式去实现。第一种：字面量的方式直接创建：

```javascript
var dary = {
    name: 'Dary',
    age: 18,
    intro: function () {
        console.log("My name is " + this.name + ', I am ' + this.age + ' years old')
    }
}
```

另外，我们还可以通过构造函数的方式创建：

```javascript
function Person (name, age) {
    this.name = name
    this.age = age
    this.intro = function () {
        console.log("My name is " + this.name + ', I am ' + this.age + ' years old')
    }
}
var dary = new Person('Dary', 18)
```

所以，当我们愁于父母压力很难找到对象的时候，多new几个就ok了。

![](/img/article/收旧对象.jpg)

现在我们来说说构造函数。上面的Person就是构造函数，构造函数是一个用来构造对象的函数，跟普通函数在申明的时候没有任何区别，主要在于这个函数如何被调用。


```javascript
function foo() {
   // ...
}
function Bar () {
    // ...
}
foo()
new Bar()
```

foo函数直接被调用，那么他就是一个普通函数，而Bar由于是通过 `new` 运算符来调用，所以他是一个构造函数，构造函数首字母一般大写。当一个函数通过`new` 运算符被调用的时候我们就可以得到一个对象，参考上面代码我们通过 `new Person()` 得到了 `dary` 对象。

当我们使用`new`来创建`dary`的时候，其实这个运算符帮我们干了三件事：

1. `var obj = {}`    创建了一个对象。
2. 将构造函数里的`this` 指向obj，也就是给obj新增了name、age属性赋值为传递进来的参数name和age，以及新增intro方法，当然，方法里的`this`仍然指向obj。**这里要着重记住一点：构造函数里的this指向将来new出来的那个对象。**
3. obj会被return出来，被dary接收，也就是说dary就是new出来的这个对象了。

在JavaScript中，一切皆为对象，我们学过的，Object、String、Number等其实都是构造函数，都可以通过new的方式来得到实例对象。而JavaScript中的面向对象都是基于**原型**来实现的，关于原型的介绍，请移步我的另一篇文章：[面向对象 — 原型（一）]( /2019/11/09/oop-02/ "面向对象 — 原型（一）")

