---
title: 锋利的ES6 — 数值扩展
date: 2020-03-17 12:32

categories:
- 大前端
tags:
- JavaScript

---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊ES6对于数值的一些扩展，虽然这些并不常用，但是了解一下还是很有意思的。**

<br>

@[toc]

<br>

## 二进制和八进制表示法

ES6 提供了二进制和八进制数值的新的写法，分别用前缀`0b`（或`0B`）和`0o`（或`0O`）表示

```javascript
var n = 0b1101
console.log(n === 13) // true

var m = 0o101
console.log(m === 65) // true
```

进制转换当然要用到`Number`啦

```javascript
Number(0b1101, 2) // 13
Number(0o101, 8) // 65
```



## isInteger（ES2015）

判断给定的参数是否为整数

```javascript
Number.isInteger(0);         // true
Number.isInteger(1);         // true

Number.isInteger(Math.PI);   // false

Number.isInteger(Infinity);  // false
Number.isInteger("10");      // false
```



## isNaN（ES2015）

判断一个是是否为`NaN`

和全局函数 `isNaN()`相比，`Number.isNaN()` 不会自行将参数转换成数字，只有在参数是值为 `NaN` 的数字时，才会返回 `true`，也就是说：**全局isNaN方法会隐式数据类型转换为数字，转换不成功也会得到true，但是Number.isNaN不会。**

```javascript
Number.isNaN(0 / 0)       // true

// 下面这几个如果使用全局的 isNaN() 时，会返回 true。
Number.isNaN("NaN");      // false，字符串 "NaN" 不会被隐式转换成数字 NaN。
Number.isNaN(undefined);  // false
Number.isNaN({});         // false
Number.isNaN("blabla");   // false
```



## isFinite（ES2015）

判断某个值是否是有穷的，即：不是infinity

和全局的 `isFinite()`函数相比，这个方法不会强制将一个非数值的参数转换成数值，这就意味着，只有数值类型的值，且是有穷的（finite），才返回 `true`

```javascript
Number.isFinite(Infinity);  // false
Number.isFinite(NaN);       // false
Number.isFinite(-Infinity); // false

Number.isFinite(0);         // true
Number.isFinite(2e64);      // true

Number.isFinite('0');       // false, 全局函数 isFinite('0') 会返回 true
```



## isSafeInteger（ES2015）

判断传入的参数值是否是一个“安全整数”（safe integer）

安全整数范围为 -(2<sup>53</sup> - 1)到2<sup>53</sup> - 1 之间的整数，包含 -(2<sup>53</sup> - 1) 和 2<sup>53</sup>- 1

```javascript
Number.isSafeInteger(3);                    // true
Number.isSafeInteger(Math.pow(2, 53))       // false
Number.isSafeInteger(Math.pow(2, 53) - 1)   // true
Number.isSafeInteger(NaN);                  // false
Number.isSafeInteger(Infinity);             // false
Number.isSafeInteger("3");                  // false
Number.isSafeInteger(3.1);                  // false
Number.isSafeInteger(3.0);                  // true
```



## MAX_SAFE_INTEGER（ES2015）

这是一个属性，代表最大安全数

```javascript
console.log(Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1) // true
console.log(Number.MAX_SAFE_INTEGER === 9007199254740991)    // true
```



## MIN_SAFE_INTEGER（ES2015）

这是一个属性，代表最小安全数

```javascript
console.log(Number.MIN_SAFE_INTEGER === 1 - Math.pow(2, 53)) // true
console.log(Number.MIN_SAFE_INTEGER === -9007199254740991)    // true
```



## Number.parseInt, Number.parseFloat（ES2015）

这两个方法与全局的 `parseInt`和`parseFloat`函数相同，这里不再赘述



## EPSILON（ES2015）

这是一个属性，表示 1 与`Number`可表示的大于 1 的最小的浮点数之间的差值

EPSILON 属性的值接近于 2.2204460492503130808472633361816 x 10<sup>-16</sup>，或者 2<sup>-52</sup>



## 指数运算符 ** （ES2015）

ES6新增了一个指数运算符两颗星号 **

```javascript
console.log(2 ** 3) // 8

var a = 2
a **= 3 // 等同于 a = a*a*a
console.log(a) // 8
```



## BigInt

第四阶段草案

`BigInt` 是一种内置对象，它提供了一种方法来表示大于 2<sup>53</sup> - 1的整数。这原本是 Javascript中可以用 `Number`表示的最大数字。`BigInt` 可以表示任意大的整数。

可以用在一个整数字面量后面加 `n` 的方式或者调用`BigInt`方法定义一个 `BigInt`

```javascript
var theBiggestInt = 9007199254740991n
var alsoHuge = BigInt(9007199254740991)
```

