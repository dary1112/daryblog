---
title: JavaScript内置对象 — Math数学对象
date: 2020-08-20 19:16

categories:
- 大前端
tags:
- JavaScript

---

**本文主要介绍标题所示内容。**

<br>

@[toc]

<br>

## 内置对象

`JavaScript`内置对象**与宿主无关，独立于宿主环境的ECMAScript实现提供的对象，在ECMAScript 程序开始执行时出现**。在 `ECMAScript` 程序开始执行前就存在，本身就是实例化内置对象，开发者**无需再去实例化**。

内置对象包含：**Global和Math**，`ECMAScript5`中增添了 **JSON** 这个存在于全局的内置对象。



## Math 对象属性

| 属性           | 描述                           |
| ------------ | ---------------------------- |
| **Math.PI**  | **返回圆周率（约等于3.14159）。**       |
| Math.E       | 返回算术常量 e，即自然对数的底数（约等于2.718）。 |
| Math.LN2     | 返回 2 的自然对数（约等于0.693）。        |
| Math.LN10    | 返回 10 的自然对数（约等于2.302）。       |
| Math.LOG2E   | 返回以 2 为底的 e 的对数（约等于 1.414）。  |
| Math.LOG10E  | 返回以 10 为底的 e 的对数（约等于0.434）。  |
| Math.SQRT1_2 | 返回 2 的平方根的倒数（约等于 0.707）。     |
| Math.SQRT2   | 返回 2 的平方根（约等于 1.414）。        |



## Math 对象方法

| 方法           | 描述                 |
| ------------ | ------------------ |
| abs(x)       | 返回数的绝对值。           |
| ceil(x)      | 对数进行上舍入。           |
| floor(x)     | 对数进行下舍入。           |
| round(x)     | 把数四舍五入为最接近的整数。     |
| max(x,y)     | 返回 x 和 y 中的最高值。    |
| min(x,y)     | 返回 x 和 y 中的最低值。    |
| pow(x,y)     | 返回 x 的 y 次幂。       |
| sqrt(x)      | 返回数的算术平方根。         |
| sin(x)       | 返回数的正弦。            |
| cos(x)       | 返回数的余弦。            |
| tan(x)       | 返回角的正切。            |
| **random()** | **返回 0 ~ 1 之间的随机小数 |



Math.random()生成一个从0-1的随机小数
生成 min ~ max （min < max）的随机数公式：

```javascript
Math.random()*(max - min) + min
```

结果一般会取整，使用`Math.floor()`或者`Math.ceil()`，具体用哪一个就要看具体需求了。



### 随机案例

点击div随机变颜色：

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div {
            width: 200px;
            height: 200px;
            background-color: red;
        }
    </style>
</head>
<body>
    <div id="box"></div>
    <script>
        var str = '0123456789abcdef'
        document.getElementById('box').onclick = function () {
            var color = '#'
            for (var i = 0; i < 6; i++) {
                // 生成随机索引
                var index = Math.floor(Math.random() * str.length)
                color += str[index]
            }
            // this就是当前元素box，style是所有样式，这里就是把背景赋值为拼接好的颜色字符串
            this.style.background = color
        }
    </script>
</body>
</html>
```

