---
title: sass使用方式和常见语法总结
date: 2019-12-03 13:37

categories:
- 大前端
tags:
- html5
- css3
---

**本文主要介绍sass的语法**
@[toc]
<br>

## Sass介绍

[Sass]( https://www.sass.hk/  'https://www.sass.hk/ ')是世界上最成熟、稳定和强大的专业级CSS扩展语言😎。

Sass是一个将脚本解析成CSS的脚本语言，即SassScript，有很多好用的语法可以帮助我们减少css代码的冗余。而且他完全兼容所有版本的css，也就是说在sass里可以直接使用css。对于使用熟练的人来说它可以极大地😍提高编程效率。

sass是一个基于ruby环境的可编程的css，不能直接用于页面，他需要编译成css以后在html文件中引入使用。

sass有两种后缀名文件：一种后缀名为sass，不使用大括号和分号；另一种就是scss文件，这种和我们平时写的css文件格式差不多，使用大括号和分号。本篇文章所有sass文件都指后缀名为scss的文件。在此也建议使用后缀名为scss的文件，以避免sass后缀名的严格格式要求报错。

## Sass语法

  ### 运算

sass可进行简单的加减乘除运算等 , 当我们拿到一张需要转换成百分比布局的设计稿， 这时候我们有福了

```scss
// scss
.container{
  width: 100%;
}
.aside{
    width:600px/960px*100%;
}
article{
    width:300px/960px*100%;
}

// css
.container{
  width: 100%;
}
.aside{
    width:62.5%;
}
article{
    width:31.25%;
}
```

### 变量

**必须以$开头**

```scss
// scss
$font-size:16px;
div{
    font-size: $font-size;
}

// css
div{
    font-size: 16px;
}
```

**复杂变量的使用**

```scss
// scss
$linkColor:#ffffff #6fb6fd;
div{
    color: nth($linkColor,1);
    background-color:nth($linkColor,2);
}

// css
div{
    color: #ffffff;
    background-color:#6fb6fd;
}
```

  一般我们都将变量当做属性值来使用， 但是也有极特殊情况下我们会**将变量当做选择器或者属性名**使用。 这时候， 我们必须以**#{$name}**的方式来使用变量名 

```scss
// scss
$name:top;
.header-#{$name}{
	border-#{name}:1px solid #b6b6b6;
}

// css
.header-top{
    border-top:1px solid #b6b6b6;
}
```

 **多值变量**：代表的是多维数据的存储方式，换句话说，list相当于js中的数组。 list数据一般用空格分割， 但是也可以用 逗号 或者小括号分割多个值   

```scss
// scss
$list : (20px 40px)(30px 20px 10)(4px 3px 2px 5px);//相当于多维数组
.main {
    margin: nth($list,1);
    padding: nth($list,2);
    border-radius: nth($list,3);
}

// css
.main {
	margin: 20px 40px;
    padding: 30px 20px 10;
    border-radius:4px 3px 2px 5px;
}
```

**map**的数据是以键值对形式出现的，相当于js中的对象，可以结合**each**完成循环渲染

```scss
// scss
$headers:(h1:20px,h2:30px,h3:40px);
@each $key, $value in $headers{
    #{$key}{
        font-size: $value;
    }
}

// css
h1 {
   font-size: 20px; 
}
h2 {
   font-size: 30px; 
}
h3 {
   font-size: 40px; 
}
```

### 嵌套

sass可以进行**选择器的嵌套**，表示层级关系，看起来很有*格

```scss
// scss
ul{
    list-style: none;
    li{
        float: left;
        >a {
            color:#f00;
        }
    }
}

// css
ul{
    list-style: none;
}
ul li{
    float: left;
}
ul li>a {
    color:#f00;
}
```

**属性也可以嵌套**

```scss
// scss
.main{
    border:{
        style:solid;
        left:none;
        right:1px;
        color:#b6b6b6;
    }
}

// css
.main{
    border-style:solid;
    border-left:none;
    border-right:1px;
    border-color:#b6b6b6;
}
```

嵌套的时候 **& 代表上一层选择器**

```scss
// scss
$color: #ddd #e00;
a {
    color: nth($color,1);
    &:hover {
        color: nth($color,2);
    }
}

// css
a {
    color: #ddd;
}
a:hover {
    color: #e00;
}
```

&还可以这么用

```scss
// scss
.header {
    height: 100px;
    &-top {
        height: 40px;
    }
}

// css
.header {
    height: 100px;
}
.header-top {
    height: 40px;
}
```

###   **mixin（混合）**   

sass中使用@mixin声明混合，可以传递参数，参数名以$符号开始，多个参数以逗号分开，也可以给参数设置默认值。声明的@mixin通过@include来调用。sass中可用mixin定义一些代码片段，且可传参数，方便日后根据需求调用。从此处理css3的前缀兼容轻松便捷。

**无参数 mixin**

```scss
// scss
@mixin marginCenter{
    margin-left:auto;
    margin-right:auto;
}
.container{
    width: 1000px;
    @include marginCenter;
}

// css
.container{
    width: 1000px;
    margin-left:auto;
    margin-right:auto;
}
```

**有参数 minxin** 

```scss
// scss
// 1000px为参数默认值
@mixin marginCenter ($width:1000px){
    width: $width;
    margin-left:auto;
    margin-right:auto;
}

.container{
    @include marginCenter;
}
.inner{
    @include marginCenter(800px);
}

// css
.container{
    width: 1000px;
    margin-left:auto;
    margin-right:auto;
}
.inner{
    width: 800px;
    margin-left:auto;
    margin-right:auto;
}
```

###   扩展/继承

sass可通过@extend来实现代码组合声明，使代码更加优越简洁   

```scss
// scss
.active{
    border:1px solid #b6b6b6;
    padding:10px;
    color: #333;
}
.success{
    @extend .active;
    width:100px;
}

// css
.active, .success{
    border:1px solid #b6b6b6;
    padding:10px;
    color: #333;
}
.success{
    width:100px;
}
```

### 函数
sass定义了很多函数可供使用，当然你也可以自己定义函数，以**@fuction**开始。实际项目中我们使用最多的应该是颜色函数，而颜色函数中又以lighten减淡和darken加深为最，其调用方法为lighten($color,$amount)和darken($color,$amount)，它们的第一个参数都是颜色值，第二个参数都是百分比。

```scss
// scss
$baseFontSize:10px;
$gray:#ccc;
@function pxToRem($px){
    @return $px/$baseFontSize*1rem;
}
html {
    font-size:$baseFontSize;
}
body{
    color:lighten($gray,10%);
}
.test{
    font-size:pxToRem(16px);
    color:darken($gray,10%);
}

// css
html {
    font-size:10px;
}
body{
    color:#e6e6e6;
}
.test{
    font-size:1.6rem;
    color:#b3b3b3;
}
```

### 导入
sass中如导入其他sass文件，最后编译为一个css文件，优于纯css的@import

```scss
@import "reset";
```

<br>

**sass还有一些不太常用的语法请参考官网文档： [https://www.sass.hk/docs/](https://www.sass.hk/docs/ 'sass中文文档')**