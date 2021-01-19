---
title: javascript算法 — 数组乱序（洗牌算法）
date: 2021-01-19 21:32

categories:
- 大前端
tags:
- JavaScript

---

**本文介绍js中几种洗牌算法。**

<br>

@[toc]

<br>

洗牌算法是将原来的数组进行打散，使原数组的某个数在打散后的数组中的每个位置上等概率的出现，即为乱序算法。

## Fisher-Yates

> 先看最经典的 `Fisher-Yates`的洗牌算法

**其算法思想就是从原始数组中随机抽取一个新的元素到新数组中。**

1. 从还没处理的数组（假如还剩n个）中，产生一个[0, n]之间的随机数 random
2. 从剩下的n个元素中把第 random 个元素取出到新数组中
3. 删除原数组第random个元素
4. 重复第 2 3 步直到所有元素取完
5. 最终返回一个新的打乱的数组

按步骤一步一步来就很简单的实现。

```javascript
function shuffle(arr){
    var result = [], random;
    while(arr.length>0){
        random = Math.floor(Math.random() * arr.length);
        result.push(arr[random])
        arr.splice(random, 1)
    }
    return result;
}
```

这种算法要去除原数组 arr 中的元素，所以时间复杂度为 O(n2)



## Knuth-Durstenfeld ShuffleFisher-Yates 

> 洗牌算法的一个变种是 Knuth Shuffle

**每次从未处理的数组中随机取一个元素，然后把该元素放到数组的尾部，即数组的尾部放的就是已经处理过的元素**

这是一种原地打乱的算法，每个元素随机概率也相等，时间复杂度从 Fisher 算法的 O(n2)提升到了 O(n)

1. 选取数组(长度n)中最后一个元素(arr[length-1])，将其与n个元素中的任意一个交换，此时最后一个元素已经确定
2. 选取倒数第二个元素(arr[length-2])，将其与n-1个元素中的任意一个交换
3. 重复第 1 2 步，直到剩下1个元素为止

```javascript
function shuffle(arr){
    var length = arr.length,
        temp,
        random;
    while(0 != length){
        random = Math.floor(Math.random() * length)
        length--;
        // swap
        temp = arr[length];
        arr[length] = arr[random];
        arr[random] = temp;
    }
    return arr;
}
```

Durstenfeld Shuffle的算法是从数组第一个开始，和Knuth的区别是遍历的方向不同。



## sort

利用Array的sort方法可以更简洁的实现打乱，对于数量小的数组来说足够。因为随着数组元素增加，随机性会变差。

随机返回-0.5到0.5的值，数组顺序进行随机交换，就实现了洗牌。

```javascript
function shuffle(arr){
    retrun arr.sort(() => 0.5 - Math.random())
}
```



## ES6

Knuth-Durstenfeld shuffle 的 ES6 实现，代码更简洁

```javascript
function shuffle(arr){
    let n = arr.length, random;
    while(0!=n){
        random =  (Math.random() * n--) >>> 0; // 无符号右移位运算符向下取整
        [arr[n], arr[random]] = [arr[random], arr[n]] // ES6的结构赋值实现变量互换
    }
    return arr;
}
```



## lodash

可以使用[lodash](https://www.lodashjs.com/docs/lodash.shuffle#_shufflecollection)里面的`_.shuffle(collection)`方法

#### 参数

1. `collection` *(Array|Object)*: 要打乱的集合。

#### 返回

*(Array)*: 返回打乱的新数组。

#### 例子

```javascript
var _ = require('lodash');

_.shuffle([1, 2, 3, 4]);

*// => [4, 1, 3, 2]*
```

