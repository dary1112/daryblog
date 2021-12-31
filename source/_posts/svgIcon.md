---
title: 在vue项目中更优雅的使用svg图标
date: 2021-12-31 17:32

categories:
- 大前端
tags:
- html5
- css3
- vue

---

**项目中的svg自定义图标可以在项目中构建字体图标，使用更为方便**

<br>

@[toc]

<br>

## 前言

项目开发时我们会用到一些UI库，比如PC端常用的[elementUI](https://element.eleme.io/)，移动端常用的[vantUI](https://vant-contrib.gitee.io/vant)、[mintUI](http://mint-ui.github.io)等。一般很多UI库都会有icon组件，也就是组件库提供的图标，但是往往这些图标并不能满足我们饥渴的设计师，就会有很多自定义的图标。那前端怎么使用呢？可能你会希望设计师切图，也就是png，当然可以实现，但是不够优雅，而且麻烦，如果同一个图标有不同的状态也就是不同的颜色还要UI设计师给多个文件，也会占用更多的空间。设计师给svg文件也是很常用的，而且svg文件可以更利于前端灵活使用，那么如何更加优雅的使用svg文件呢？本文带你来看看。



## 一、放置svg文件

在src目录下新建目录结构如下：

```
|-- src
|   |-- components
|       |-- SvgIcon
|           |-- src
|               |-- index.vue
|           |-- icons
|               |-- up.svg
|               |-- svg文件2
|           |-- index.js
```

将设计师给到的svg文件放入`icons`文件夹中，<font color="red">注意文件命名不要有中文和其他特殊字符，后面会用到。</font>找到外层有fill属性的g标签或者path标签将fill属性设置为"currentColor"，一般svg文件都会有多个嵌套的g标签或者path标签并且比较靠外层的会有个fill属性为这个svg文件的填充色值，如果没有的话就自己找到外层的添加上`fill="currentColor"`。

如下示例：

```html
<svg >
	<path d="M507.1......" p-id="2033" fill="currentColor"></path>
</svg>
```

或：

```html
<svg >
  <desc>Created with Sketch</desc>
	<g id="ola" stroke="none" fill="currentColor">
  	<g>
    	<path d="M6,0 L78...."></path>
    </g>
  </g>
</svg>
```



## 二、SvgIcon组件

书写SvgIcon组件代码：`/src/components/SvgIcon/src/index.vue`

```vue
<template>
  <svg :class="svgClass" aria-hidden="true" v-on="$listeners">
    <use :xlink:href="iconName" />
  </svg>
</template>
<script>
export default {
  name: 'SvgIcon',
  props: {
    iconClass: {
      type: String,
      required: true
    },
    className: {
      type: String,
      default: ''
    }
  },
  computed: {
    iconName () {
      return `#icon-${this.iconClass}`
    },
    svgClass () {
      return `svg-icon ${this.className || ''}`
    }
  }
}
</script>
<style scoped>
  .svg-icon {
    width: 1em;
    height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
  }
</style>
```



## 三、配置组件

在第一步中新建的`index.js`文件里

```js

import Vue from 'vue'
import SvgIcon from './src'// svg组件

// 全局注册
Vue.component('SvgIcon', SvgIcon)
 
const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./icons', false, /\.svg$/)
const iconMap = requireAll(req)
```



## 四、webpack配置

安装依赖包

```bash
npm i svg-sprite-loader
```

在`vue.config.js`中增加配置

```js
const path = require('path')
module.exports = {
  chainWebpack: config => {
    config.module
    	.rule('svg')
    	.exclude.add(path.resolve('src/components/SvgIcon'))
    	.end();
   	config.module
    	.rule('icons')
    	.test(/\.svg$/)
    	.include.add(path.resolve('src/components/SvgIcon'))
    	.end()
    	.use('svg-sprite-loader')
    	.options({
      	symbolId: 'icon-[name]'
    	})
    	.end();
  }
}
```



## 五、引入

在main.js中引入配置好的组件

```js
import '@/components/SvgIcon'
```



## 六、使用

```vue
<template>
	<div>
    <SvgIcon class="my-icon" icon-class="up" />
  </div>
</template>

<style>
  .my-class {
    font-size: 20px;
    color: #f00;
  }
</style>
```

<font color="red">`icon-class`属性值必须为对应svg文件的文件名。</font>

`class`属性为自定义，如需设置图标大小或颜色，则可如上示例自定义class通过`font-size`属性修改大小，`color`属性修改图标颜色。





