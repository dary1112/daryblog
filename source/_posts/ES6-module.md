---
title: 锋利的ES6 — Module
date: 2020-06-16 22:11
categories:
- 大前端
tags:
- JavaScript
---

***锋利的ES6* 系列文章介绍ES6（ES5+）中新增的比较好用的新特性。本篇聊聊Module模块化。**

<br>

@[toc]

<br>

## 介绍

历史上，JavaScript 一直没有模块（module）体系，无法将一个大程序拆分成互相依赖的小文件，再用简单的方法拼装起来，这对开发大型的、复杂的项目形成了巨大障碍。

直到ES6定义了`export`和`import`的语法来导出和引入模块从而实现模块化开发。

> ES6 的模块自动采用严格模式，不管你有没有在模块头部加上`"use strict"`,此时顶层`this`指向`undefined`。



## export

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。下面是一个 JS 文件，里面使用`export`命令输出变量。比如我们可以在`person.js`里书写如下代码：

```javascript
export var username = 'Dary'
export var age = 19
```

这样就可以向外暴露两个变量`username`和`age`

也可以这样写：

```javascript
var username = 'Dary'
var age = 19
export { username, age }
```

上面代码在`export`命令后面，使用大括号指定所要输出的一组变量。它与前一种写法（直接放置在`var`语句前）是等价的，但是应该优先考虑使用这种写法。因为这样就可以在脚本尾部，一眼看清楚输出了哪些变量。

`export`除了导出普通变量也可以导出函数

```javascript
export function foo (a, b) {
    return a + b
}
```

或者导出一个class：

```javascript
export class Person {
    constructor (name) {
        this.name = name
    }
}
```

一般来讲，`export`导出的变量就是原变量名，但是我们也可以使用`as`来对其重命名

```javascript
var username = 'Dary'
var age = 19
export {
  username as myName,
  age as myAge
}
```



## import

我们使用`export`命令定义了模块的对外接口以后，其他 JS 文件就可以通过`import`命令加载这个模块，`import`引入的时候一般使用文件的相对路径，文件的后缀名可以省略（当然，也可以不省略）。

以`person.js`如下代码为例：

```javascript
// person.js
var username = 'Dary'
var age = 19
var attr = {
    height: 180,
  	weight: 70
}
export { username, age, attr }
```

我们可以在同级目录下的`main.js`这样引入使用：

```javascript
// main.js
import { username, age } from './person'
console.log(`My name is ${username}, I am ${age} years old`)
```

如果想为输入的变量重新取一个名字，`import`命令要使用`as`关键字，将输入的变量重命名

```javascript
// main.js
import { username as myName, age as myAge } from './person'
console.log(`My name is ${myName}, I am ${myAge} years old`)
```

通过`import`导入的变量是只读的，也就是说不允许在导入里修改，这里相当于`const`定义的常量，也就是说不能重新赋值，但是如果是引用类型（对象数组或函数），可以修改其属性，只要保证引用不被修改即可。

```javascript
// main.js
import { username, attr } from './person'
username = '熊大林' // Syntax Error : 'username' is read-only;
attr.height = 190 // 合法操作
```

> 注意，`import`命令具有提升效果，会提升到整个模块的头部，首先执行，但是一般我们都会将模块引入的代码放在文件的开头。

最后，`import`语句会执行所加载的模块，因此可以有下面的写法

```javascript
import 'lodash'
```

上面代码仅仅执行`lodash`模块，但是不输入任何值



### 模块整体引入

我们在通过`import`引入模块的时候可以决定引入模块的哪些部分变量，但是如果要整体全部引入需要使用到`* as`这样的语法

```javascript
// main.js
import * as person from './person'
console.log(`My name is ${person.username}, I am ${person.age} years old`)
console.log(`My weight is ${person.attr.weight}, and height is ${person.attr.height}`)
```



## export default

从前面的例子可以看出，使用`import`命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。但是，用户肯定希望快速上手，未必愿意阅读文档，去了解模块有哪些属性和方法。

为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到`export default`命令，为模块指定默认输出。

```javascript
// person.js
export default function (a, b) {
    return a + b
}
```

其他模块加载该模块时，`import`命令可以为该匿名函数指定任意名字

```javascript
// main.js
import myFn from './person'
console.log(myFn(2, 2))  // 4
```

`export default`命令用在非匿名函数前，也是可以的

```javascript
// person.js
function foo (a, b) {
    return a + b
}
export default foo
```



## export和import复合写法

```javascript
export { username, age } from './person'
```

相当于：

```javascript
import { username, age } from './person'
export { username, age }
```

上面代码中，`export`和`import`语句可以结合在一起，写成一行。但需要注意的是，写成一行以后，`username`和`age`实际上并没有被导入当前模块，只是相当于对外转发了这两个接口，导致当前模块不能直接使用`username`和`age`。

