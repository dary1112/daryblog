title: Javascript算法 — 数组去重
date: 2020-03-23 12:11
top: 1 // 置顶就留着

categories:
- 大前端
tags:
- JavaScript

**本文介绍js中数组去重的几种常用方式**

<br>

@[toc]

<br>

作为初级前端面试，一般算法问题考的不多，但是如果考算法的话，数组去重被问到的概率是非常大的，本文介绍几种去重方案。

## 一、双重for

定义一个新数组，并存放原数组的第0个元素，然后将元素组一一和新数组的元素对比，若不同则存放在新数组中。这里面用到了标志位的思想，先假设不重复`var isRepeat = false`，如果遇到重复的，就修改为`isRepeat = true`并且结束循环，循环结束后再判断`isRepeat`的状态从而决定是否放入新数组。

```javascript
function unique (arr) {
  var newArr = [arr[0]]
  for (var i = 1; i < arr.length; i++) {
    var isRepeat = false
    for (var j = 0; j < newArr.length; j++) {
      if (arr[i] === newArr[j]) {
        isRepeat = true
        break
      }
    }
    if (!isRepeat) {
      newArr.push(arr[i])
    }
  }
  return newArr
}
console.log(unique([1, 2, 2, 2, 3, 1, 12, 7, 12])) // [1, 2, 3, 12, 7]

```



## 二、排序 + 去重

先将原数组排序（无论按数字大小排序还是ASCII码排序均可），再与相邻的进行比较，如果不同则存入新数组。

```javascript
function unique2 (arr) {
  var sortArr = arr.sort()
  var newArr = [sortArr[0]]
  for (let i = 1; i < sortArr.length; i++) {
    if (sortArr[i] !== sortArr[i-1]) {
      newArr.push(sortArr[i])
    }
  }
  return newArr
}
console.log(unique2([1, 2, 2, 2, 3, 1, 12, 7, 12])) // [1, 12, 2, 3, 7]

```



## 三、利用对象

利用对象属性名不重复的特性，将数组元素作为对象属性名来取值，如果没有取到值则代表此元素是第一次被遍历到也就是第一次出现，那就把他作为对象属性名存一个值并`push`进新数组，下次如果再遍历到相同的元素则`if`不成立，不再`push`。

```javascript
function unique3 (arr) {
  var obj = {}
  var newArr = []
  for (let i = 0; i < arr.length; i++) {
    if (!obj[arr[i]]) {
      obj[arr[i]] = 1
      newArr.push(arr[i])
    }   
  }
  return newArr
}
console.log(unique3([1, 2, 2, 2, 3, 1, 12, 7, 12])) //  [1, 2, 3, 12, 7]

```



## 四、indexOf

 利用数组的indexOf下标属性来查询。

```javascript
function unique4 (arr) {
  var newArr = []
  for (var i = 0; i < arr.length; i++) {
    if (newArr.indexOf(arr[i]) === -1) {
      newArr.push(arr[i])
    }
  }
  return newArr
}
console.log(unique4([1, 2, 2, 2, 3, 1, 12, 7, 12])) // [1, 2, 3, 12, 7]

```



## 五、includes

利用数组原型对象上的includes方法。

```javascript
function unique5 (arr) {
  var newArr = []
  for (var i = 0; i < arr.length; i++) {
    if (!newArr.includes(arr[i])) {
      newArr.push(arr[i])
    }
  }
  return newArr
}
console.log(unique5([1, 2, 2, 2, 3, 1, 12, 7, 12])) // [1, 2, 3, 12, 7]

```



## 六、filter + includes

利用数组原型对象上的 `filter` 和`includes`方法，如果`newArr`已存在该元素，`filter`里就`return false`，否则就`push`并返回一个`true`（我这里返回的是`push`的返回值即数组新的`length`）。

```javascript
function unique6(arr) {
  var newArr = []
  newArr = arr.filter(function (item) {
    return newArr.includes(item) ? false : newArr.push(item)
  })
  return newArr
}
console.log(unique6([1, 2, 2, 2, 3, 1, 12, 7, 12])) // [1, 2, 3, 12, 7]

```



## 七、forEach + includes

利用数组原型对象上的 forEach 和 includes方法，和方法六几乎相同。

```javascript
function unique7 (arr) {
  var newArr = []
  arr.forEach(item => {
    return newArr.includes(item) ? '' : newArr.push(item)
  })
  return newArr
}
console.log(unique7([1, 2, 2, 2, 3, 1, 12, 7, 12])) // [1, 2, 3, 12, 7]

```



## 八、splice

利用数组原型对象上的 splice 方法。

```javascript
function unique8 (arr) {
  for (var i = 0; i < arr.length; i++) {
    for (var j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) {
        arr.splice(j, 1)
        j--
      }
    }
  }
  return arr
}
console.log(unique8([1, 2, 2, 2, 3, 1, 12, 7, 12])) // [1, 2, 3, 12, 7]

```

**Methods 9**: 利用数组原型对象上的 lastIndexOf 方法。

```
        function unique9(arr) {



            var res = [];



            for (var i = 0; i < arr.length; i++) {



                res.lastIndexOf(arr[i]) !== -1 ? '' : res.push(arr[i]);



            }



            return res;



        }



        console.log(unique9([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));



        // 结果是[1, 2, 3, 5, 6, 7, 4]
```

**Methods 10**: 利用 ES6的set 方法。

```
        function unique10(arr) {



            //Set数据结构，它类似于数组，其成员的值都是唯一的



            return Array.from(new Set(arr)); // 利用Array.from将Set结构转换成数组



        }



        console.log(unique10([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));



        // 结果是[1, 2, 3, 5, 6, 7, 4]
```

 