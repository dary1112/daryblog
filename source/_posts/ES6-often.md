---
title: 锋利的ES6 — 常用新特性
date: 2020-03-18 16:21

categories:
- 大前端
tags:
- JavaScript

---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊ES6中非常常用而且好用的新特性。**

<br>

@[toc]

<br>

## 箭头函数

**箭头函数表达式**的语法比函数表达式更简洁，并且没有自己的`this`，`arguments`，`super`或`new.target`。箭头函数表达式更适用于那些本来需要匿名函数的地方，并且它不能用作构造函数。

箭头函数的语法是

```javascript
(param1, param2, …, paramN) => { statements }

// 当函数体只有一句而且这一句就是返回值的时候，函数体的{}以及return关键字都可以省略
// 以下相当于：(param1, param2, …, paramN) =>{ return expression; }
(param1, param2, …, paramN) => expression

// 但是如果返回值是对象，则要像下面这么写
params => ({foo: bar})

// 当只有一个参数时，圆括号是可选的：
(singleParam) => { statements }
singleParam => { statements }

// 没有参数的函数应该写成一对圆括号。
() => { statements }
```

箭头函数不会创建自己的`this`,它只会从自己的作用域链的上一层继承`this`。因此，在下面的代码中，传递给`setInterval`的函数内的`this`与封闭函数中的`this`值相同

下面我们来看一个例子：

```javascript
// 使用箭头函数前
function Person () {
	this.age = 0
	var _this = this
	setInterval(function () {
        // 这里this指向window，所以我们用_this
		_this.age++
	}, 1000)
}
var p = new Person()

// 使用箭头函数
function Person () {
	this.age = 0
	setInterval(() => {
		this.age++; // |this| 正确地指向 p 实例
	}, 1000)
}
var p = new Person()
```

在项目当中，箭头函数使用非常广泛，因为箭头函数非常简洁，而且没有自己的this，他的this指向外层

* 如果希望this指向内层，就用普通函数
* 如果希望this指向外层，就用箭头函数
* 如果两层this都需要，那就用普通函数并且在外层 `var _this = this`



## 解构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为`解构（Destructuring）`

以前，要把数组中的值提取出来，得这么写

```javascript
var arr = [2, 5, 7]
var a = arr[0], b = arr[1], c = arr[2]
```

ES6可以这么写

```javascript
var arr = [2, 5, 7]
var [a, b, c] = arr
```

上面代码表示，可以从数组中提取值，按照对应位置，对变量赋值。

如果解构不成功，变量的值就等于`undefined`

```javascript
var arr = [2, 5]
var [a, b, c] = arr
```

这样的话c就是`undefined`

但是，也可以给默认值

```javascript
var arr = [2, 5]
var [a, b, c = 1] = arr
```

当c可以被解构的时候就等于解构出来的值，没有的话c的值就默认为1

**对象也是可以解构赋值的。**

但是对象的解构赋值跟数组是有区别的，数组是按顺序解构，对象是按属性名解构，也就是说定义的变量名对应对象的属性名。

```javascript
var person = {
	username: 'Dary',
    age: 19,
    gender: 'male'
}
var { username, gender, age } = person
```

同样的，如果结构失败会得到`undefined`，同时也可以给默认值

```javascript
var person = {
	username: 'Dary',
    age: 19,
    gender: 'male'
}
var { username, gender, age, height } = person // 这样写 height 就是 undefined
var { username, gender, age, height = 180 } = person // 这样写 sex 就是 180
```

而且解构赋值还可以嵌套使用

```javascript
var person = {
	username: 'Dary',
    age: 19,
    gender: 'male',
    likes: ['吃饭', '睡觉']
}
var { likes: [ like1 ]} = person
console.log(like1) // 吃饭
```



## let

`let`命令，用来声明变量。它的用法类似于`var`，但是所声明的变量，只在`let`命令所在的代码块内有效。

什么叫代码块？简单地说：使用{}括起来的代码被称为**代码块**。比如：if、for、try...catch这些都是代码块

```javascript
var a = 20
if (a % 2 === 0) {
    let b = 5
}
console.log(b) // ReferenceError: b is not defined.
```

我们可以看到，在if块里用let定义的b变量在这个块级外面是不可使用的。

**let还有一个特点就是没有变量提升**

```javascript
console.log(a) // undefined
console.log(b) // ReferenceError: b is not defined.
var a = 10
let b = 20
```

**暂时性死区**

只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

```javascript
var a = 20
if (a % 2 === 0) {
    console.log(a) // ReferenceError: Cannot access 'a' before initialization
    let a = 10
}
```

面代码中，存在全局变量a，但是块级作用域内`let`又声明了一个局部变量a，导致后者绑定这个块级作用域，所以在`let`声明变量前，打印a会报错。这种情况被称为`暂时性死区`



## const

`const`声明一个只读的常量。一旦声明，常量的值就不能改变

```javascript
const a = 10
a++ // TypeError: Assignment to constant variable.
```

`const`一旦声明变量，就必须立即初始化，不能留到以后赋值

```javascript
const a // SyntaxError: Missing initializer in const declaration
```

`const`命令声明的常量也是不提升

```javascript
console.log(a) // ReferenceError: Cannot access 'a' before initialization
const a = 10
```

`const`命令同样存在暂时性死区

```javascript
const a = 20
if (a % 2 === 0) {
    console.log(a) // ReferenceError: Cannot access 'a' before initialization
    const a = 10
}
```



但是，我们来看看下面的代码

```javascript
const arr = [ 2, 5, 6]
arr.push(7)
console.log(arr) // [ 2, 5, 6, 7 ]
arr = [ 2, 5, 6, 7 ] // TypeError: Assignment to constant variable.

const obj = {
    key1: 'value1',
    key2: 'value2'
}
obj.key3 = 'value2'
console.log(obj) // { key1: 'value1', key2: 'value2', key3: 'value3' }
obj = { key1: 'value1', key2: 'value2', key3: 'value3' } // TypeError: Assignment to constant variable.
```

我们可以看到，用`const`申明的数组和对象是可以被修改的，但是不能被重新赋值，这说明了什么问题？

**`const`实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，`const`只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了**



## 扩展运算符

扩展运算符（spread）是三个点（`...`）。它好比 rest 参数的逆运算，将一个数组转为一系列用逗号分隔的值。

```javascript
function sum(x, y, z) {
  return x + y + z;
}
const numbers = [1, 2, 3];
console.log(sum(...numbers)); // 6
```

也可以拷贝数组

```javascript
var arr = [3, 4, 5]
var arr1 = [...arr]
```

甚至在拷贝的同时追加元素

```javascript
var arr = [3, 4, 5]
var arr1 = [2, ...arr, 6, 7, 8]
```

**在ES2018的标准里，扩展也可以用于对象**

```javascript
var obj = {
    key1: 'value1',
    key2: 'value2'
}
var obj2 = {
    ...obj,
    key3: 'value3'
}
```



## 模板字符串

模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量

```javascript
// 普通字符串
`In JavaScript '\n' is a line-feed.`

// 多行字符串
`In JavaScript this is
 not legal.`

console.log(`string text line 1
string text line 2`);

// 字符串中嵌入变量
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```



## 函数默认参数

ES6 之前，不能直接为函数的参数指定默认值，只能采用变通的方法。

```javascript
function log(x, y) {
  y = y === undefined ? 'World' : y
  console.log(x, y)
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
```

ES6允许在定义参数的时候给默认值

```javascript
function log(x, y = 'World') {
  console.log(x, y)
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
```

