---
title: 锋利的ES6 — class语法糖
date: 2020-04-25 21:55

categories:
- 大前端
tags:
- JavaScript

---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊类的语法糖class。**

<br>

@[toc]

<br>

## 语法糖

JavaScript中，生成实例对象的传统方法是通过构造函数。由于这种语法与c++，java相差有点大，故ES6引入了class这个语法糖。class的功能在ES5中大部分都能做到，新的class写法让对象原型的写法更加清晰，更像面向对象的编程。

但是由于JavaScript的面向对象都是基于原型的，所以虽然ES6新增了class但是并不是意味着JavaScript就支持class了，而是一种语法糖，也就是说。class的本质还是构造函数+原型。

class的语法是这样的

```javascript
class Dog {
    constructor (name, age) {
        this.name = name
        this.age = age
    }
    say () {
        console.log(`My name is ${this.name},I am ${this.age} years old`)
    }
    eat (food) {
        console.log(`${this.name} is eating ${food}`)
    }
}
```

1. 定义`class`的语法时，不需要加`function`，方法之间也不需要逗号隔开
2. **定义一个类，它的本质就是一个函数，类本身就指向一个构造函数**
3. `constructor`就是构造函数，构造函数里的`this`指向将来`new`的实例，一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加
4. 类的所有方法都定义在类的`prototype`属性上，也就是说上面的`say`和`eat`方法都在`Dog`的原型上
5. 在类的实例上调用方法，实际上是调用原型上的方法。由于类的方法都在`prototype`对象上，所以类的新方法可以添加到这个对象上
6. 类的内部所有定义的方法，都是不可枚举的（non-enumerable）
7. 所有的`class`都有`name`属性，属性值为类的名称，`Dog.name`将会得到字符串`Dog`

## new实例

基于上面的类这样我们就可以new一个对象了：

```javascript
var snoopy = new Dog('Snoopy', 18)
snoopy.say()
snoopy.eat('meat')
```

> class是没有变量提升的，也就是说必须在class定义之后去new实例。

## 静态方法：

class是支持静态方法的：

```javascript
class Dog {
    constructor (name, age) {
        this.name = name
        this.age = age
    }
    say () {
        console.log(`My name is ${this.name},I am ${this.age} years old`)
    }
    eat (food) {
        console.log(`${this.name} is eating ${food}`)
    }
    static walk () {
        console.log(`${this.name} is walking`)
    }
}
```

1. 静态方法里的this指向Dog本身，而不是实例
2. 静态方法不在原型链上，而在构造函数本身上面
3. 实例不能调用静态方法，而是通过class本身来调用：`Dog.walk()`
4. 父类的静态方法，可以被子类继承

## 静态属性

静态属性指的是 Class 本身的属性，即`Class.propName`，而不是定义在实例对象（`this`）上的属性

```javascript
class Dog {
    
}
Dog.legs = 4
```

上面这个写法就为`Dog`类定义了一个`legs`静态属性

目前，只有这种写法可行，因为 ES6 明确规定，Class 内部只有静态方法，没有静态属性。现在有一个[提案](https://github.com/tc39/proposal-class-fields)提供了类的静态属性，写法是在实例属性法的前面，加上`static`关键字

```javascript
class Dog {
    static legs = 4
}
```

## 私有方法和私有属性

### 私有属性

目前，有一个[提案](https://github.com/tc39/proposal-private-methods)，为`class`加了私有属性。方法是在属性名之前，使用`#`表示

```javascript
class Dog {
    #legs = 4
    run () {
        console.log(this.#legs)
    }
}
```

上面代码中，`#count`就是私有属性，只能在类的内部使用（`this.#count`）。如果在类的外部使用，就会报错：

```javascript
var orange = new Dog()
orange.run() // 4
console.log(orange.#legs) // Uncaught SyntaxError: Private field '#legs' must be declared in an enclosing class
```

之所以要引入一个新的前缀`#`表示私有属性，而没有采用`private`关键字，是因为 JavaScript 是一门动态语言，没有类型声明，使用独立的符号似乎是唯一的比较方便可靠的方法，能够准确地区分一种属性是否为私有属性。另外，Ruby 语言使用`@`表示私有属性，ES6 没有用这个符号而使用`#`，是因为`@`已经被留给了 Decorator。

### 私有方法

`#`这种写法不仅可以写私有属性，还可以用来写私有方法

```javascript
class Dog {
    #legs
    constructor (legs) {
        this.#legs = legs
    }
    #jump () {
        console.log(this.#legs)
    }
}
```

> 上面代码中，`#jump`就是一个私有方法。

<font color="red">**但是，注意：私有方法目前浏览器都还没有实现，私有属性新版chrome亲测可以使用，但是私有方法不行！！！这还只是一个提案！！！**</font>



## 继承

ES6的继承非常简单，直接来看例子

```javascript
// 父类
class Animal {
    #eyes = 2
    constructor (name, age) {
        this.name = name
        this.age = age
    }
    say () {
        console.log(`My name is ${this.name},I am ${this.age} years old`)
    }
    static walk () {
        console.log(`${this.name} is walking`)
    }
    jump () {
        console.log(this.#eyes)
    }
}

// 子类
class Dog extends Animal {
    constructor (name, age) {
       super(name, age)
    }
}

var snoopy = new Dog('Snoopy', 18)
snoopy.say() // My name is Snoopy,I am 18 years old
snoopy.jump() // 2
snoopy.walk() // Uncaught TypeError: snoopy.walk is not a function
console.log(snoopy.#eyes) // Uncaught SyntaxError: Private field '#eyes' must be declared in an enclosing class
```

> 静态方法不能被继承

> 私有属性不能在外部使用，更别说继承了