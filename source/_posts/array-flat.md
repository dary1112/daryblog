---
title: javascript算法 — 数组扁平化
date: 2020-10-21 17:12

categories:
- 大前端
tags:
- JavaScript

---

**本文介绍js中数组扁平化的几种常用方式**

<br>

@[toc]

<br>

话不多说，直接上方案：

## 一、flat（ES6）

利用ES6的flat方法：

`flat()` 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。

```javascript
const arr1 = [0, 1, 2, [3, 4]]
console.log(arr1.flat()) // [0, 1, 2, 3, 4]

const arr2 = [0, 1, 2, [[[3, 4]]]];
console.log(arr2.flat(2)) // [0, 1, 2, [3, 4]]

//使用 Infinity，可展开任意深度的嵌套数组
var arr4 = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]]];
arr4.flat(Infinity) // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```



## 二、reduce + concat

```javascript
var arr = [1, 2, [3, 4]];

// 展开一层数组
arr.reduce((acc, val) => acc.concat(val), []) // [1, 2, 3, 4]
```



## 三、reduce + spread 

使用扩展运算符：

```javascript
var arr = [1, 2, [3, 4]];

// 展开一层数组
const flattened = arr => [].concat(...arr)
```



## 四、reduce + concat + isArray + recursivity

利用递归多层扁平化：

```javascript
// 使用 reduce、concat 和递归展开无限多层嵌套的数组
var arr1 = [1,2,3,[1,2,3,4, [2,3,4]]];

function flatDeep(arr, d = 1) {
   if (d > 0) {
       return arr.reduce((res, item) => {
           return res.concat(Array.isArray(item) ? flatDeep(item, d - 1) : item)
       }, [])
   } else {
       return arr.slice()
   }
}

flatDeep(arr1, Infinity) // [1, 2, 3, 1, 2, 3, 4, 2, 3, 4]
```



## 五、forEach+isArray+push+recursivity

```javascript
// forEach 遍历数组会自动跳过空元素
const eachFlat = (arr = [], depth = 1) => {
  const result = []; // 缓存递归结果
  // 开始递归
  (function flat(arr, depth) {
    // forEach 会自动去除数组空位
    arr.forEach((item) => {
      // 控制递归深度
      if (Array.isArray(item) && depth > 0) {
        // 递归数组
        flat(item, depth - 1)
      } else {
        // 缓存元素
        result.push(item)
      }
    })
  })(arr, depth)
  // 返回递归结果
  return result
}

var arr = [1, 2, [3, 4, [5, 6]]]
const flattened = eachFlat(arr) // [1, 2, 3, 4, [5, 6]]
const flattened2 = eachFlat(arr, Infinity) // [1, 2, 3, 4, 5, 6]
```



## 六、Generator function

使用迭代器，对于`Generator `还不熟悉的朋友可以参考我另外一篇文章**[锋利的ES6 — Generator](http://www.xiongdalin.com/2020/05/21/ES6-generator/)**。

```javascript
function* flatten(array) {
    for (const item of array) {
        if (Array.isArray(item)) {
            yield* flatten(item);
        } else {
            yield item;
        }
    }
}

var arr = [1, 2, [3, 4, [5, 6]]]
const flattened = [...flatten(arr)] // [1, 2, 3, 4, 5, 6]
```

注意，这里调用方法并不能直接得到结果，而是在迭代器里，使用`next()`可以一个个取出来，但是太麻烦，所以可以用扩展运算符展开之后放入一个新的额数组从而得到最终结果。