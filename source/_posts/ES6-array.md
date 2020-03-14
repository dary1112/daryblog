---
title: 锋利的ES6 — 数组API
date: 2020-03-14 10:04

categories:
- 大前端
tags:
- JavaScript
---

**锋利的ES6系列文章介绍ES6（ES5+）中新增的比较好用的新特性，本篇先介绍数组API以及使用这些API来改造todolist（待办事项列表）。**

@[toc]

<br>

## forEach（ES5）

遍历数组，没有返回值，稀疏数组中的`undefined`被过滤掉，但是索引还是正常值。

```javascript
var arr = ['Dary', 'Tom', 'Jim', , 'Lom']
arr.forEach(function (item, index) {
    console.log(item, index)
    // Dary 0
    // Tom 1
    // Jim 2
    // Lom 4
})
```

需要指出的是：

* 在遍历的回调函数里的第三个参数array指的就是当前正在遍历的数组

  ```javascript
  var arr = ['Dary', 'Tom', 'Jim', , 'Lom']
  arr.forEach(function (item, index, array) {
      // array就是arr本身
  })
  ```

* forEach除了传一个回调函数以外，后面还有可选的第二个参数，传递一个对象，这样在遍历的时候this就指向这个对象

  ```javascript
  var arr = ['Dary', 'Tom', 'Jim', , 'Lom']
  arr.forEach(function (item, index) {
      // 这里的this就指向document
  }, document)
  ```

**但是一般不会在这里对数组进行操作，forEach就单纯的负责遍历数组，如果要对数组做操作可以使用下面介绍的一些方法**



## map（ES5）

遍历的同时返回一个新的数组，由每趟遍历的回调函数里返回值组成，一般可以用于对数组里的数据做修改。需要特别指出的是每一次遍历都要返回一个结果，否则最后你会得到一个稀疏数组。

```javascript
var arr = [1, 4, 9, 16]
var arr1 = arr.map(function (item, index) {
    if (item % 2) return item * 2
    return item
})
console.log(arr1) // [2, 4, 18, 16]
```

*当然，这里的回调里也是有第三个参数指当前数组本身，回调后面也有第二个可选参数绑定this指向的，这里就不赘述了。*

**map方法一般用来修改数组，可以修改整个数组也可以根据一些条件修改数组中的某个值**



## filter（ES5）

过滤，遍历的同时返回一个新的数组。在回调函数里返回一个条件，满足条件的数据会被保留，不满足的被过滤掉。

```javascript
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present']
const result = words.filter(function (word, index) {
    return word.length > 6
})
console.log(result)  // ["exuberant", "destruction", "present"]
```

*毫无疑问，这个方法的回调里也有第三个参数以及回调后面的参数。*

**这个方法一般用于删除数组元素**



## some（ES5）

返回一个布尔值，在回调函数里返回一个条件，只要在遍历的过程中有任意一条数据满足条件，就终止遍历，并且最终得到的值就是true，否则会一直遍历到结束，最终得到false

```javascript
const arr = [1, 2, 3, 4, 5]
const even = arr.some(function (element, index) {
    return element % 2 === 0
})
console.log(even) // true
```

*这个方法也有上述方法提到过的可选参数*

## every（ES5）

返回一个布尔值，在回调函数里返回一个条件，在遍历的过程中只要有一条数据不满足条件，就终止遍历，并且最终得到false，否则会一直遍历到结束，最终得到true

```javascript
const arr = [1, 30, 39, 29, 10, 13];
const isBelowThreshold = arr.every(function (currentValue) {
    return currentValue < 40
})
console.log(isBelowThreshold) // true
```

*这个方法也有上述方法提到过的可选参数*



## reduce、reduceRight（ES5）

归并，返回一个新值，在每一趟遍历时返回一个值，这个值会继续作为下一次遍历的初始值来使用，最终遍历完成得到最后返回的值。一般在处理整个数组需要最终得到一个值的情况下使用，比如计算总价，合并数组等。

> reduce是从左往右归并，而reduceRight是从右往左，只是顺序不一样并不影响结果，reduceRight一般用的很少。

```javascript
var arr = [1, 2, 3, 4]
// acc是累计器，他的值是每一次回调里的返回值
// curr是当前正在处理的数组元素
// 1 + 2 + 3 + 4
var count = arr.reduce(function (acc, curr) {
    return acc + curr
})
console.log(count) // 10

// 在回调函数的后面可以给一个初始值，这样初次acc的值就是这个初始值
// 5 + 1 + 2 + 3 + 4
console.log(array1.reduce(function (acc, curr) {
    return acc + curr
}, 5)) // 15
```



## from

这是Array对象本身的方法，所以调用方式是`Array.from()`，它可以将一个可迭代对象，例如NodeList、classList等转换成一个数字

```javascript
Array.from(document.querySelesctorAll('.delete'))
Array.from(document.querySelesctor('.delete').classList)
```



## includes（ES2016）

实验性的API，包含，返回一个布尔值，判断一个数组里是否包含某个值，比indexOf更好用的是直接得到布尔值而不用去判断返回值是否为-1（注：其实indexOf也是属于ES5的语法，但是由于平时用的非常频繁，这里就不单独提出来讲了）

```javascript
// 结合Array.from判断元素是否含有某个class非常方便
Array.from(document.querySelesctor('.delete').classList).includes('myClass')
```



## *copyWithin（ES6）*

实验性的API，复制数组中的一部分值，返回一个新的数组，新数组的长度和原数组一致

```javascript
[1, 2, 3, 4, 5].copyWithin(-2)         // [1, 2, 3, 1, 2]
[1, 2, 3, 4, 5].copyWithin(0, 3)       // [4, 5, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)    // [4, 2, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin(-2, -3, -1) // [1, 2, 3, 3, 4]
```



## *entries*

实验性的API，返回一个新的**Array Iterator**对象，该对象包含数组中每个索引的键/值对

```javascript
const array1 = ['a', 'b', 'c'];

const iterator1 = array1.entries();

console.log(iterator1.next().value);  // [0, "a"]
console.log(iterator1.next().value);  // [1, "b"]
```



## *fill（ES6）*

实验性的API，指定值和索引来填充数组

```javascript
const array1 = [1, 2, 3, 4];

console.log(array1.fill(0, 2, 4)); // [1, 2, 0, 0]
console.log(array1.fill(5, 1));    // [1, 5, 5, 5]
console.log(array1.fill(6));       // [6, 6, 6, 6]
```



## *flat（ES2019）*

将多维数组扁平化

```javascript
var arr1 = [1, 2, [3, 4]];
arr1.flat();   // [1, 2, 3, 4]

var arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat();   // [1, 2, 3, 4, [5, 6]]

var arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);  // [1, 2, 3, 4, 5, 6]

//使用 Infinity，可展开任意深度的嵌套数组
var arr4 = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]]];
arr4.flat(Infinity); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// 移除数组中的空项
var arr5 = [1, 2, , 4, 5];
arr5.flat(); // [1, 2, 4, 5]
```



## *flatMap*

草案，还没有标准化，使用方式跟map方法类似，不过这个方法回调里需要返回数组，最终会把每一趟返回的数组concat器来作为这个方法的结果。在一趟遍历需要得到两个值的时候比较有用

```javascript
var arr1 = [1, 2, 3, 4];

arr1.map(x => x * 2);         // [2, 4, 6, 8]
arr1.flatMap(x => [x * 2]);   // [2, 4, 6, 8]
flatMap(x => [x * 2, x * 3]); // [2, 3, 4, 6, 6, 9, 8, 12]
```



## 写一个todolist

**接下来我们就使用刚学的这些API来改造todolist**

1. 首先，从所有的todos中筛选出已完成和未完成我们可以使用**filter**

   改造前：

   ```javascript
   var hasFinishedTodos = []
   for (var i = 0; i < todos.length; i++) {
       if (todos[i].hasFinished) hasFinishedTodos.push(todos[i])
   }
   var unFinishedTodos = []
   for (var i = 0; i < todos.length; i++) {
       if (!todos[i].hasFinished) unFinishedTodos.push(todos[i])
   }
   
   ```

   改造后：

   ```javascript
   var hasFinishedTodos = todos.filter(function (todo) {
       return todo.hasFinished
   })
   var unFinishedTodos = todos.filter(function (todo) {
       return !todo.hasFinished
   })
   ```

2. 计算未完成事项的总时间，可以使用**reduce**

   改造前：

   ```javascript
   var hours = 0
   for (var i = 0; i < unFinishedTodos.length; i++) {
       hours += unFinishedTodos[i].time
   }
   ```

   改造前：

   ```javascript
   var hours = unFinishedTodos.reduce(function (time, todo) {
       return time + todo.time
   }, 0)
   ```

3. 循环数组拼接html字符串可以使用**reduce**

   改造前：

   ```javascript
   var hasFinishHTML = ''
   for (var i = 0; i < hasFinishedTodos.length; i++) {
       hasFinishHTML += '<div class="column is-one-quarter" data-id="${todo.id}">'+
           '...'+
       '</div>'
   }
   document.querySelector('#has-finish-wrap').innerHTML = hasFinishHTML
   ```

   改造后：

   ```javascript
   document.querySelector('#has-finish-wrap').innerHTML = 
       hasFinishedTodos.reduce(function (html, todo) {
       	return html + '<div class="column is-one-quarter" data-id="${todo.id}">'+
           	'...'+
       	'</div>'
   }, '')
   ```

4. 事件委托通过class判断事件源可以使用**Array.from**和**includes**配合

   改造前：

   ```javascript
   var arrClass = e.target.className.split(/\s+/)
   if (arrClass.indexOf('btn-toggle') !== -1) {
       // ...
   }
   ```

   改造后：

   ```javascript
   if (Array.from(e.target.classList).includes('btn-toggle')) {
       // ...
   }
   ```

5. 切换完成状态修改数组可以使用**map**

   改造前：

   ```javascript
   for (var i = 0; i < todos.length; i++) {
       if (todos[i].id === id) todos[i].hasFinished = !todos[i].hasFinished
   }
   ```

   改造后：

   ```javascript
   todos = todos.map(function (todo) {
       if (todo.id === id) todo.hasFinished = !todo.hasFinished
       return todo
   })
   ```

6. 删除待办事项可以使用**filter**

   改造前：

   ```javascript
   for (var i = 0; i < todos.length; i++) {
       if (todos[i].id === id) {
           todos.splice(i, 1)
           i--
       }
   }
   ```

   改造后：

   ```javascript
   todos = todos.filter(function (todo) {
       return todo.id !== id
   })
   ```



> todolist（待办事项列表）案例将在我的github上持续更新

> 地址：https://github.com/dary1112/todolist.git

> 欢迎star！！！



