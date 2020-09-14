---
title: JavaScript对象 — Date日期对象
date: 2020-09-14 17:31

categories:
- 大前端
tags:
- JavaScript

---

**本文介绍Javascript中日期的操作 — Date对象**

<br>

@[toc]

<br>

## 介绍

Date 对象用于处理日期与时间。可以通过 new 关键词来定义 Date 对象，也通过使用针对日期对象的方法，我们可以很容易地对日期进行操作。项目中很多跟时间相关的业务都需要使用Date对象，比如：限时抢购、整点秒杀等。



## 创建

创建日期对象通过new运算符：

```javascript
var date = new Date() // 获取当前日期对象
```

也可以传入参数获取指定时间的日期对象：

```javascript
var date = new Date(2020,8,9) // 2020年9月9日(时分秒为当前时分秒)
var date = new Date(2020,8,9,12,13,14) // 2020年9月9日12点13分14秒
```

> 注意：传入月份参数Date对象支持0-11代表1-12月，因此上面传入8实际是9月。



## 操作

### 获取

Date对象提供了一系列获取时间信息的API。

以笔者写文章的时间：`2020-09-14 17:31:43`为例：

#### getTime

获取时间戳。

> 时间戳是指格林威治时间自1970年1月1日（00:00:00 GMT）至当前时间的总毫秒数。

```javascript
console.log(new Date().getTime()) // 1600075903652
```

#### <font color="#999">getYear</font>

```javascript
console.log(new Date().getYear()) // 120
```

> getYear是20世纪发布的方法，获取年份后两位，但是这个方法到21世纪以后明显不实用了，所以现在不用它来获取年份，而是使用getFullYear获取完整的四位的年份。

#### getFullYear

```javascript
console.log(new Date().getFullYear()) // 2020
```

#### getMonth

```javascript
console.log(new Date().getMonth()) // 8
```

> month的取值范围是0-11，分别代表1-12月，所以9月获取到的是8。

#### getDate

```javascript
console.log(new Date().getDate()) // 14
```

#### getDay

```javascript
console.log(new Date().getDay()) // 1
```

> day的取值范围为0-6分别代表星期天到星期六。

#### getHours

```javascript
console.log(new Date().getHours()) // 17
```

#### getMinutes

```javascript
console.log(new Date().getMinutes()) // 31
```

#### getSeconds

```javascript
console.log(new Date().getSeconds()) // 43
```

#### getMilliseconds

```javascript
console.log(new Date().getMilliseconds()) // 652
```

> 获取当前毫秒，取值范围0-999



### 设置

上面set系列的API除了Day以外其他的都可以set：

`setTime`，`setFullYear`，`setMonth`，`setDate`，`setHours`，`setMinutes`，`setSeconds`，`setMilliseconds`。

1. 星期几是不能设置的，否则把今天设置为星期六是不是就可以不上班了？哈哈，星期几是根据日期来自动计算的而不能设置。
2. 使用set系列API的时候传入的参数可以超出正常范围，此时js会自动前后推算，比如：`date.setMonth(12)`会自动推算到下一年的一月，`date.setDate(0)`会自动推算到上个月的最后一天等。
3. 调用set方法的时候可以传入多个参数设置同级别更精细的时间，比如：
   * `date.setFullYear(2000,2,2)`指的是设置为2000年3月2日
   * `date.setMonth(2,2)`指的是设置为3月2日
   * `date.setHours(2,2,2)`指的是设置为凌晨2点零2分2秒



#### 设置API的使用小技巧

* 日期设置为明天

  ```javascript
  var date = new Date()
  date.setDate(date.getDate() + 1)
  ```

* 获取这个月总天数

  ```javascript
  var date = new Date()
  date.setMonth(date.getMonth()+1, 0) // 设置为下个月0号也就是这个月最后一天
  var allDays = date.getDate() // 最后一天是几号这个月就有几天
  console.log(allDays)
  ```

  

## 转换

以笔者写文章的时间：`2020-09-14 17:31:43`为例：

#### 日期转为字符串

```javascript
var date = new Date()

console.log(date.toString()) // Mon Sep 14 2020 17:31:43 GMT+0800 (中国标准时间)
console.log(date.toDateString()) // Mon Sep 14 2020
console.log(date.toTimeString()) // 17:31:43 GMT+0800 (中国标准时间)

console.log(date.toLocaleString()) // 2020/9/14 下午5:31:43
console.log(date.toLocaleDateString()) // 2020/9/14
console.log(date.toLocaleTimeString()) // 下午5:31:43
```

#### 时区转换

**toUTCString**：转为标准时区的时间。

> 由于地球是球形而且会自转，每个地区时间都会有所差异，地理学家把地球划分了24个时区，相邻时区之间间隔1小时，其中，本初子午线所在的一区叫中区或零时区，中国面积广大，东西横跨经度64°，分布在从东五区到东九区的五个时区内。为了便于东西间的联系，现在全国都采用东八区的标准时间，也就是“北京时间”，作为全国统一的时刻。所以北京时间比标准时间提前8个小时。

```javascript
console.log(date.toUTCString()) // Mon, 14 Sep 2020 09:31:43 GMT
```



## moment.js

对于项目中的日期处理，可以引入`moment.js`库：[http://momentjs.cn/](http://momentjs.cn/ 'http://momentjs.cn/')。

这个库里封装了很多日期操作的API，比如：

* `moment()` 可以获取和设置日期信息
* `moment().dayOfYear(100)` 可以用于获取第100天的日期
* `moment().dayOfYear()` 可以用于获取当前是这一年的第几天
* `moment.utc()` 转为标准时区显示
* `moment().add()`可以在当前日期基础上加上一些时间
* `moment().format()` 可以格式化日期对象，按照需要的格式来显示
* `moment.locale()`设置语言环境（全球化）

这里只是列举了一小部分，更多的可以参阅官网文档[http://momentjs.cn/docs/](http://momentjs.cn/docs/ 'http://momentjs.cn/docs/')。

