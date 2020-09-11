---
title: JavaScript对象 — Date日期对象
date: 2020-09-08 12:11

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

以笔者写文章的时间：`2020-09-11 16:31:43`为例：

#### getTime

获取时间戳。

> 时间戳是指格林威治时间自1970年1月1日（00:00:00 GMT）至当前时间的总毫秒数。

```javascript
console.log(new Date().getTime()) // 1599813080292
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
console.log(new Date().getDate()) // 11
```

#### getDay

```javascript
console.log(new Date().getDay()) // 5
```

> day的取值范围为0-6分别代表星期天到星期六。

#### getHours

```javascript
console.log(new Date().getHours()) // 16
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

