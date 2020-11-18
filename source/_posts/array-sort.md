---
title:  Javascript算法 — 数组排序
date: 2020-11-18 15:43

categories:
- 大前端
tags:
- JavaScript
---

**本文介绍js中数组排序的几种常用方式。**

<br>

@[toc]

<br>

作为初级前端面试，一般算法问题考的不多，但是如果考算法的话，数组排序被问到的概率是非常大的，本文介绍几种排序方案。



## 一、冒泡排序

最基本也是最经典的排序算法。利用双层`for`循环，外层控制比较趟数，内层控制当前这一趟比较的次数，相邻的两个数互相比较，如果前一个数比后一个数大，那就直接交换，以此类推，每一趟比较都能确定当前的最大值，所以就会像冒泡一样将比较大的数冒泡到最后，从而排好序。

```javascript
function sort1 (arr) {
  var len = arr.length
  for (var i = 0; i < arr.length; i++) {
    for (var j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j+1]) {
        var temp = arr[j+1]
        arr[j+1] = arr[j]
        arr[j] = temp
      }
    }
  }
  return arr
}
console.log(sort1([2,4,32,4,6,9,8])) // [2, 4, 4, 6, 8, 9, 32]

```



## 二、选择排序

选择排序相较冒泡排序没有那么激进，选择排序在比较的过程中只是记录当前最小值所在的索引，一趟结束以后判断最小值索引如果不是一开始假设的值，那就将最小索引所在的值交换到这一趟的最前面去，从而完成排序。

```javascript
function sort2 (arr) {
  for (var i = 0; i < arr.length - 1; i++) {
    var minIndex = i
    for (var j = i + 1; j < arr.length; j++) {
      if (arr[j] < arr[minIndex]) {
        minIndex = j
      }
    }
    if (minIndex !== i) {
      var temp = arr[i]
      arr[i] = arr[minIndex]
      arr[minIndex] = temp
    }
  }
  return arr
}
console.log(sort2([2,4,32,4,6,9,8])) // [2, 4, 4, 6, 8, 9, 32]

```



## 三、快速排序

找一个基准值，将数组中比基准值小的放入左边，比基准值大的放入右边，然后将左右两部分继续找基准值再分成两部分，依次递归下去，直到不能再拆分为止（数组length为1），就排好序了。

```javascript
function sort3 (arr) {        
  if (arr.length <= 1) {            
    return arr
  } 
  var num = Math.floor(arr.length / 2)
  var centerVal = arr.splice(num, 1)
  var left = []  
  var right = []
  for (var i = 0; i < arr.length; i++) {
    if (arr[i] < centerVal[0]) {
      left.push(arr[i])
    }else{
      right.push(arr[i])
    }
  }
  return sort3(left).concat(centerVal, sort3(right)) 
}
console.log(sort3([2,4,32,4,6,9,8])) // [2, 4, 4, 6, 8, 9, 32]

```



## 四、插入排序

依次往后遍历，将遍历到的数和前面的数从后往前依次比较，如果当前数比前面的某个数要大，那就说明当前数应该在的位置就是这个数的后面。

```javascript
function sort4 (arr) {
  for (var i = 1; i < arr.length; i++) {
    var preIndex = i - 1
    var current = arr[i]
    while (preIndex >= 0 && arr[preIndex] > current) {
      arr[preIndex + 1] = arr[preIndex]
      preIndex--
    }
    arr[preIndex + 1] = current
  }
  return arr
}
console.log(sort4([2,4,32,4,6,9,8])) // [2, 4, 4, 6, 8, 9, 32]

```



## 五、希尔排序

插入排序的改进版，分组做插入排序。通过对增量`gap`的控制实现对分组的细化，最后完成排序。

```javascript
function sort5 (arr) {
  var gap = 1
  while (gap < arr.length / 5) {
    gap = gap * 5 + 1
  }
  for (; gap > 0; gap = Math.floor(gap / 5)) {
    for (var i = gap; i < arr.length; i++) {
      var temp = arr[i]
      for (var j = i - gap; j >= 0 && arr[j] > temp; j -= gap) {
        arr[j + gap] = arr[j]
      }
      arr[j + gap] = temp
    }
  }
  return arr
}
console.log(sort5([2,4,32,4,6,9,8])) // [2, 4, 4, 6, 8, 9, 32]

```



## 六、堆排序

利用堆（一棵顺序存储的完全二叉树）来完成堆数组的排序。

```javascript
function sort6 (arr) {
  // 初始化大顶堆，从第一个非叶子结点开始
  for (let i = Math.floor(arr.length / 2 - 1); i >= 0; i--) {
    adjustHeap(arr, i, arr.length)
  }
  // 排序，每一次for循环找出一个当前最大值，数组长度减一
  for(let i = Math.floor(arr.length - 1); i > 0; i--) {
    // 根节点与最后一个节点交换
    swap(arr, 0, i)
    // 从根节点开始调整，并且最后一个结点已经为当前最大值，不需要再参与比较，所以第三个参数为 i，即比较到最后一个结点前一个即可
    adjustHeap(arr, 0, i)
  }
  return arr
}

// 交换两个节点
function swap(arr, i, j) {
  let temp = arr[i]
  arr[i] = arr[j]
  arr[j] = temp
}

// 将 i 结点以下的堆整理为大顶堆，注意这一步实现的基础实际上是：
// 假设 结点 i 以下的子堆已经是一个大顶堆，adjustheap 函数实现的
// 功能是实际上是：找到 结点 i 在包括结点 i 的堆中的正确位置。后面
// 将写一个 for 循环，从第一个非叶子结点开始，对每一个非叶子结点
// 都执行 adjustheap 操作，所以就满足了结点 i 以下的子堆已经是一大
// 顶堆
function adjustHeap(arr, i, length) {
  // 当前父节点
  let temp = arr[i] 
  // j<length 的目的是对结点 i 以下的结点全部做顺序调整
  for(let j = 2 * i + 1; j < length; j = 2 * j + 1) {
    // 将 arr[i] 取出，整个过程相当于找到 arr[i] 应处于的位置
    temp = arr[i] 
    if(j+1 < length && arr[j] < arr[j+1]) {
      // 找到两个孩子中较大的一个，再与父节点比较
      j++
    }
    // 如果父节点小于子节点:交换；否则跳出
    if(temp < arr[j]) {
      swap(arr, i, j)
      // 交换后，temp 的下标变为 j
      i = j
    } else {
      break
    }
  }
}
console.log(sort6([2,4,32,4,6,9,8])) // [2, 4, 4, 6, 8, 9, 32]

```



## 七、sort

使用数组的API `sort`实现排序。

```javascript
function sort7 (arr) {
  return arr.sort((a, b) => a - b)
}
console.log(sort7([2,4,32,4,6,9,8])) // [2, 4, 4, 6, 8, 9, 32]

```

