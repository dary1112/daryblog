---
title: 前端模块化之AMD — requirejs的使用
date: 2020-07-13 23:35

categories:
- 大前端
tags:
- JavaScript

---

**本篇聊聊前端模的块化规范，其中比较常用的AMD规范以及其代表作`requrie.js`**。

<br>

@[toc]

<br>



## 前言

随着Ajax技术的兴起，前后端分离的开发模式逐渐占领了几乎整个市场，现在的服务器带宽也足够完成大量数据交互，于是很多以前在服务器端的逻辑也可以移植到前端来处理了，从而减轻服务器的压力，当然，这样的话Javascript代码就会越来越复杂。而Javascript作为一门松散的弱类型语言，在一个大型项目面前如果没有合理的拆分与规划，将会变得很难维护，这个时候我们就需要把前端逻辑做一个**模块化**的处理。

所谓模块化就是把需要重复使用的功能封装成模块，一个页面有一个统筹全局的对象把所有模块引入进来，最终形成一个产品，做成一个完整的项目。

JS本身在ES6以前没有模块化的，ES6有模块化了，但是主流浏览器对于ES6模块化的支持度不足，所以一般不能直接使用，所以我们今天讨论第三方的模块化实现。



## 模块化的好处

就好比要生产一台挖掘机，是由各个厂商提供的配件组装出来的，而不是由一家公司从头到尾生产，这样的好处是各个零部件各司其职，如果有一个功能坏掉了只需要找到对应的零部件解决问题即可，从而避免将来维修的麻烦。

作为代码也是同样的道理，一个完整的项目可以由很多个模块组成，如果某部分功能需要修改或者升级只需要找到对应模块的代码进行维护即可，大大提高了代码的可读性以及节约了维护成本。



## 模块化规范

模块的使用一般分为**导入**和**导出**，定义一个模块需要导出出去在需要使用的地方导入。所谓模块化规范就是规定了模块的使用方式，不同的规范制定了不同的导入和导出的方式。常见的模块化规范有如下几种：

### AMD

​	依赖前置：提前引入，文件开头把需要的模块一次性全部引入，后面直接使用

​	前期消耗比较大，后期执行效率很高

​	代表作是 require.js

### CMD

​	按需加载：在代码执行过程当中需要一个模块了才去加载

​	整个曲线比较平缓

​	代表作是sea.js，但是现在已经很少使用了

### ES6 Module

 	浏览器都还不支持，但是可以借助像[webpack](https://www.webpackjs.com 'https://www.webpackjs.com')这样的打包工具来实现打包，从而使浏览器可以运行代码。详细用法可参照我的另一篇文章[锋利的ES6 — Module](http://www.xiongdalin.com/2020/06/16/ES6-module 'http://www.xiongdalin.com/2020/06/16/ES6-module')。

[百度](http://www.baidu.com)

> 另外：服务器端Node.js遵循commonJS规范，module.exports 导出模块，require引入模块。



## require.js

我们今天以[require.js](https://requirejs.org 'https://requirejs.org')为例来说说前端模块化的使用。



### 一、初探

第一步我们需要引入`require.js`文件

```javascript
<script src="https://requirejs.org/docs/release/2.3.6/minified/require.js"></script>
```

我们可以稍微查看一下文件的源代码：

![](/img/article/require01.png)

出乎我们意料的是require一上来二话不说先声明了三个全局变量：`require`，`requirejs`以及`define`，但是这样我们的方向也就很明确了，我们可以打印一下这三个值：

![](/img/article/require02.jpg)

我们可以看到这是三个函数，而且`require`和`requirejs`看起来非常相似，所以我们可以尝试打印一下：

```javascript
console.log(require === requirejs) // true
```

这个结果毫无疑问是`true`，也就是说`require`和`requirejs`是同一个函数，那么我们肯定喜欢用`require` 🤓🤓🤓

另外一个就是`define`，所以我们用`requirejs`其实用的就是`require`和`define`这两个函数，而且结合咱们之前说过的模块化就是**定义模块**和**使用模块**，所以`define`就是用来定义模块而`require`是用来引入模块的。



### 二、基本使用

比如我们定义一个`a`模块，`a.js`的代码可以如下：

```javascript
// module a
define(() => {
    class A {
        constructor (name) {
            this.name = name
        }
        say () {
            console.log(this.name)
        }
    }
    return A
})
```

再定义一个`b`模块，`b.js`代码如下：

```javascript
// module b
define(() => {
    class B {
        constructor () {
            this.name = 'module b'
        }
        say () {
            console.log(this.name)
        }
    }
    return new B()
})
```

这里定义了一个类并分别`return`了这个类或者它的实例，这样我们在需要的时候就可以在需要使用的页面，比如与之同级的`index.js`中使用`require`函数来引入了：

```javascript
require(['./a', './b'], (ModuleA, mB) => {
  console.log('index page code')
  new ModuleA('a').say()
  mB.say()
})
```

我们通过`require`可以依次引入我们所需要的模块，并且在回调函数里**按顺序**接收各自模块的返回值来使用，这样就使得代码可以非常方便的完成服用，实现模块化了。



### 三、简化

现在我们在页面中需要写两个`script`标签，一个用来引入`require.js`，另外一个用来引入当前页面所需要的js，这样还是比较麻烦，`require.js`给我们提供了更简单的方式，我们可以在引入`require.js`的`script`中写一个`data-main`属性，如下：

```javascript
<script src="./js/require.min.js" data-main="./js/index"></script>
```

这样就可以少写一个`script`标签，因为`require.js`会帮助我们找到当前`script`标签上的自定义属性`data-main`并且根据属性值找到文件运行。



### 四、配置

现在模块化已经具有一定规模了，但是在正式项目中文件路径通常是比较复杂的，所以每次要依赖某个模块都写路径的方式比较麻烦，因此我们可以将所有模块做一个简单的配置，我们可以新建一个`config.js`，假设我们的项目路径如下：

![](/img/article/require03.png)

在这个文件里我们可以集中将所有模块以及其路径做配置如下：

```javascript
require.config({
    baseUrl: '/', // 当前项目跟路径，可能需要视具体情况稍做调整
    paths: {
        // 模块名： '路径' // 注意：路径一定不能有后缀
        a: 'js/a',
        b: 'js/b'
    }
})
```

这样我们可以修改一下`index.js`

```javascript
require(['./config'], () => {
  require(['a', 'b'], (ModuleA, mB) => {
  	console.log('index page code')
  	new ModuleA('a').say()
  	mB.say()
	})
})
```

这样做的好处是现在引入模块不用写路径了，而是直接使用在`config.js`里配置好的模块名即可，简单高效。

