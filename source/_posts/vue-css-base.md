---
title: 全局前置less或sass配置
date: 2021-09-30 17:05

categories:
- 大前端
tags:
- html5
- css3
- less
- vue

---

**vue项目中全局前置样式的设置**

<br>

@[toc]

<br>

在写样式时我们用到[less](http://lesscss.cn/)或者[sass](https://www.sass.hk/)，会让样式写起来更加工程化，既然用到了可编程的css，当然就会用到变量了或者一些全局的样式希望能提前定义好，但是每个vue文件里都要引入全局样式又太麻烦，所以我们需要做一个配置，提前load全局样式文件，就可以在项目中任何地方放心使用变量和样式啦。这里我就一less为例，sass也是一样的用法。

我们需要借助`style-resources-loader`这个包

```bash
npm i style-resources-loader -D
```

然后我们需要写一个全局的less文件，如`src/base.less`，可以像这样把常用的样式写进变量里，全局样式可以写成mixin。

```less
/* 行为相关颜色 */
$color-primary: #007aff;
$color-success: #4cd964;
$color-warning: #f0ad4e;
$color-error: #dd524d;

/* 文字基本颜色 */
$text-color: #121212;
$text-color-able: #333333;
$text-color-highlight: #EF8A06;

/* 背景颜色 */
$bg-color: #FFFFFF;
$bg-color-grey: #D8D8D8;

/* 边框 */
$border: 1px solid #D8D8D8;

/* 尺寸变量 */

/* 文字尺寸 */
$font-size-sm: 12px;
$font-size-base: 14px;
$font-size-big: 18px;


/* Border Radius */
$border-radius: 4px;
$border-radius-circle: 50%;

/* 水平间距 */
$spacing-row-sm: 8px;

/* 垂直间距 */
$spacing-col-sm: 6px;

/* 透明度 */
$opacity-disabled: 0.3; // 组件禁用态的透明度

@mixin textOneLine ($max-width: 100%) {
	max-width: $max-width;
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap;
}
```

最后在`vue.config.js`里配置如下：

```js
import path from 'path';

module.exports = {
  pluginOptions: {
    'style-resources-loader': {
      preProcessor: 'less',
      patterns: [
        path.resolve(__dirname, './src/bass.less')
      ]
    }
  }
}
```

>  patterns是个数组，如果你的全局样式比较多，可能需要分为多个文件，那就继续加上就行，另外要注意这里必须是绝对路径，所以我用到了`path.resolve`。

最后，在项目中任何组件里，都可以直接使用base里定义的变量或者样式啦。

```vue
<script lang="less">
  .desc {
		font-size: $font-size-base;
		color: $text-color;
		line-height: 3;
    @include textOneLine(250px);
	}
</script>
```

