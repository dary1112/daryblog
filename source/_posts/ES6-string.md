---
title: 锋利的ES6 — 字符串API
date: 2020-03-15 12:13

categories:
- 大前端
tags:
- JavaScript

---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊字符串API。**

<br>

@[toc]

<br>

## startsWith（ES6）

判断一个字符串是否以某个子字符串开头

```javascript
var str = '代码不是万能的，但不写代码是万万不能的'
str.startsWith('代码')    // true
str.startsWith('不写代码')    // false
str.startsWith('万能的', 4) // true
```



## endsWith（ES6）

判断一个字符串是否以某个子字符串结尾

```javascript
var str = '代码不是万能的，但不写代码是万万不能的'
str.endsWith('不能的')     // true
str.endsWith('真实')     // false
str.endsWith('万万', 16) // true
```



## includes（ES6）

比数组的includes早一年标准化，功能基本一致，判断字符串中是否存在某个子字符串得到布尔值

**根据笔者多年经验，所有字符串新增API里，这个用的最频繁**



## padStart（ES2017）

在字符串开头填充指定字符，不传第二个参数就填充空格

```javascript
'abc'.padStart(10);         // "       abc"
'abc'.padStart(10, "foo");  // "foofoofabc"
'abc'.padStart(6,"123465"); // "123abc"
'abc'.padStart(8, "0");     // "00000abc"
'abc'.padStart(1);          // "abc"
```



## padEnd（ES2017）

在字符串末尾填充指定字符，不传第二个参数就填充空格

```javascript
'abc'.padEnd(10);          // "abc       "
'abc'.padEnd(10, "foo");   // "abcfoofoof"
'abc'.padEnd(6, "123456"); // "abc123"
'abc'.padEnd(1);           // "abc"
```



## repeat（ES6）

返回一个将源字符串重复多次以后的新字符串

```javascript
"abc".repeat(-1)     // RangeError: repeat count must be positive and less than inifinity
"abc".repeat(0)      // ""
"abc".repeat(1)      // "abc"
"abc".repeat(2)      // "abcabc"
"abc".repeat(3.5)    // "abcabcabc" 参数count将会被自动转换成整数.
"abc".repeat(1/0)    // RangeError: repeat count must be positive and less than inifinity
```



## raw（ES6）

String对象本身的方法，模板字符串的标签函数，可以把模板字符串解析为普通字符串，也可以指定字符串里嵌入的表达式从而得到一个解析之后的字符串。

有两种用法，第一种不必作为函数调用，而是直接后面紧跟模板字符串：

```javascript
var username = 'dary'
var str = String.raw`hello ${username}!`
console.log(str) // hello dary!
```

第二种用法就是作为函数来调用，第一个参数是属性为`raw`的对象，后面的参数是要往`raw`属性值里嵌入的表达式，可以写很多个：

```javascript
var username = 'dary'
var age = 19
var str = String.raw({ raw: 'Hello' }, username, age)
console.log(str) // Hdarye19llo
```

这个时候你会发现他并不是完整的显示完 Hello再去解析后面的表达式，而是按照字符来的，我们可以这么改造

```javascript
var username = 'dary'
var age = 19
var str = String.raw({ raw: ['Hello ', ',you are ', ' years old'] }, username, age)
console.log(str) // Hello dary,you are 19 years old
```

> 注意：raw属性字符串或者数组的长度要大于后面表达式的个数，否则最后一个表达式不会解析



## normalize（ES6）

将unicode字符串转换为普通字符串（正规化）

```javascript
str = '\u718a\u5927\u6797'

str.normalize() // 熊大林

```



## trim（ES5）

去掉字符串前后空白字符，包括space, tab, no-break space以及所有行终止符字符LF，CR等空白字符，一般用于处理用户输入

```javascript
'   Hello world!   '.trim())  // 'Hello world!'
```



## *trimLeft、trimRight*

实验性API，去除左边或者右边的空格