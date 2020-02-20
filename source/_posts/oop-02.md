---

title: 面向对象 — 原型（一）
date: 2019-11-20 21:42

categories:

- 大前端

tags:

-  JavaScript

---

**本文主要介绍JavaScript中的原型和原型链的基本概念和作用**
@[toc]
<br>

通过前一篇文章[面向对象 — 从基本介绍到构造函数](/2019/11/06/oop-01/ "面向对象 — 从基本介绍到构造函数")的阅读，我们知道了JavaScript是一门面向对象的语言，但是，作者[Brendan Eich](https://baike.baidu.com/item/Brendan%20Eich/561441 "https://baike.baidu.com/item/Brendan Eich/561441")在设计Javascript的时候并没有打算引入 “类”的概念，因为Javascript在当时作为一门简易的脚本语言，并不需要那么正式。但是最后，Brendan还是为其设计了一套完整的面向对象的机制，包括继承（关于Javascript中继承的实现可移步我另一篇文章：[面向对象 — 继承]()）。

在这一套机制里，首先，借鉴了Java和C++的语法引入了new运算符，同时也借鉴了这些语言里在使用new运算符的时候就会调用类的constructor（构造函数），所以干脆就设计成了new一个构造函数来实现实例化。所以在JavaScript里，new运算符后面跟的不是类，而是构造函数。

```javascript
function Person (name, age) {
    this.name = name
    this.age = age
    this.intro = function () {
        console.log("My name is " + this.name + ', I am ' + this.age + ' years old')
    }
}
var dary = new Person('Dary', 18)
var brendan = new Person('Brendan', 58)
dary.intro() // My name is Dary, I am 18 years old
brendan.intro() // My name is Brendan, I am 58 years old
```

这样的话我们可以使用一个构造函数new多个实例，而且这些实例都拥有在构造函数里定义的属性和方法，但是如果我们尝试输出一下：

```javascript
console.log(dary.intro === brendan.intro) // false
```

你会发现，这会输出 `false`，因为每个实例都拥有自己的属性和方法的副本，它们互相独立互不干扰。这样不仅无法做到数据共享，更是极大的浪费资源（两个intro方法体一模一样，但却存了两份）。考虑到这一点，Brendan决定为每一个函数设置一个prototype属性，即原型。

**原型是函数的伴生体**，什么是伴生体？看过红楼梦的都知道，贾宝玉之所以名叫贾宝玉是因为他出生的时候嘴里就含着一块玉，那么原型之于函数就相当于那块玉之于贾宝玉。

每个函数在被创建的时候就会有一个 `prototype`属性，这个属性是一个指针，指向一个对象，而这个对象就是这个函数的原型对象，它是用来共享所有实例的属性和方法的地方。换句话说，写在这个对象里的属性和方法是所有实例公用的，这样的好处主要有两点：一、可以实现数据共享，多个实例共享相同的数据；二、节约资源，不需要每个实例存一份副本了。所以，我们的代码可以这样改写：

```javascript
function Person (name, age) {
    this.name = name
    this.age = age
}
Person.prototype.intro = function () {
    console.log("My name is " + this.name + ', I am ' + this.age + ' years old')
}
var dary = new Person('Dary', 18)
var brendan = new Person('Brendan', 58)
dary.intro() // My name is Dary, I am 18 years old
brendan.intro() // My name is Brendan, I am 58 years old
console.log(dary.intro === brendan.intro) // true
```

当我们把intro方法写在构造函数的原型上的时候，我们看到每一个通过Person实例化出来的对象都会调用同一个原型上的intro方法。也就是说，intro方法并不属于某一个实例，dary和brendan这两个对象本身都应该没有intro方法，我们在浏览器控制台打印了这两个对象也发现了这一点：

![](/img/article/原型console截图.png)

但是我们看到，实例对象除了自己本身有的属性name个age以外，还有一个`__proto__`属性，这个属性是不可枚举的，但是大部分浏览器都可以直接输出并且使用它。从这实例对象的这个属性里我们都发现了intro，所以处于好奇，我们输出一下它们俩：

```javascript
console.log(dary.__proto__.intro === brendan.__proto__.intro) // true
```

我们会惊奇的发现，得到的结果为`true`，说明它俩是同一个方法，而这个方法我们是在Person的原型上定义的，所以我们有一个大胆的猜测：

```javascript
console.log(dary.__proto__ === Person.prototype) // true
console.log(brendan.__proto__ === Person.prototype) // true
console.log(brendan.__proto__ === dary.__proto__) // true
```

我们发现，这三句表达式输出结果均为`true`，这下就说得通了，**实例对象有一个不可枚举的属性 \_\_proto\_\_，这个属性是一个指针，指向了其构造函数的prototype也就是原型对象，实例可以通过 \_\_proto\_\_ 访问到构造函数的原型上的方法。**

<font color="#bf1827 " size=4>**简单讲：实例的 \_\_proto\_\_ 指向构造函数的prototype。**</font>

而当我们打开Person.prototype的时候还会发现里面有一个不可枚举的`constructor`属性，这个属性指回当前构造函数本身，也就是说：

```javascript
console.log(Person.prototype.constructor === Person) // true
```

由此，我们可以得到如下图所示的关系：

![](/img/article/原型示意图.png)

由此图我们可以看出：当dary要访问某个属性或者方法的是，应该先从自己身上查找，如果有，则直接使用，如果没有，则从 \_\_proto\_\_ 属性找到了Person.prototype，可以调用Person.prototype上的方法。

这时我突发奇想，尝试着 `dary.toString()`发现可以成功调用，但是我在dary本身以及Person.prototype上都没有定义这个方法，我又试着把Person.prototype输出一下，结果惊讶到我了：

![](/img/article/原型console截图2.png)

仔细思考一下也就明白了，既然Person.prototype是一个对象，就是说它也应该是一个实例，那么他的构造函数是谁？作为一个普通对象来讲，应该都是 `new Object()` 吧？所以我试着输出：

```javascript
console.log(Person.prototype.__proto__ === Object.prototype) // true
```

当我看到这个结果为 `true` 的时候我就彻底明白了，**函数的原型本质就是一个普通对象，所以他是来自Object的实例，因此，原型对象的 \_\_proto\_\_ 属性指向Object.prototype。**

由此，我们可以得到下图：

![](/img/article/原型链示意图.png)

那Object.prototype不也是一个普通对象么？那它的\_\_proto\_\_ 呢？

我试着console了一下`Object.prototype.__proto__ `，结果得到了`null`，我想这应该是Javascript故意这么设计的吧。

所以，**原型（prototype）**是函数的伴生体，实例的 \_\_proto\_\_  指向构造函数的prototype，Object.prototype.\_\_proto\_\_ === null。记住这两句，原型你就明白了一大半了。

并且，文章读到这里，我们也就能明白**Javascript一切皆为对象**这句话的真正含义了，因为Javascript中任意数据都能沿着自己的原型链最终找到`Object.prototype` ，任意数据都能调用Object.prototype上的方法。



### 附上几个与原型相关的常用属性和方法列表

+ `prototype` &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;构造函数的原型

+ `__proto__`也叫 `[[prototype]]`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 隐式原型，实例对象上的属性，指向构造函数的prototype

+ `instanceof` &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;运算符，判断一个对象是否是构造函数的实例

  ```javascript
  console.log(dary instanceof Person) // true
  console.log(dary instanceof Object) // true
  ```

+   `hasOwnProperty`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  判断对象上是否存在某个属性，并且这个方法会过滤到原型上的属性

  ```javascript
  console.log(dary.hasOwnProperty('name')) // true
  console.log(dary.hasOwnProperty('intro')) // false
  console.log(dary.hasOwnProperty('abc')) // false
  ```

+   `isPrototypeOf`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;检查一个对象是否存在于另一个对象的原型链上

  ```javascript
  console.log(Person.prototype.isPrototypeOf(dary)) // true
  console.log(Object.prototype.isPrototypeOf(dary)) // true
  ```

  