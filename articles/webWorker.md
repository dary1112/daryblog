---
title: 文章系列 — 文章标题
date: 2020-10-23 12:11

categories:
- 大前端
tags:
- html5
- JavaScript

---

**文章介绍**

<br>

@[toc]

<br>





### 生成一个专用worker

创建一个新的worker很简单。你需要做的是调用[`Worker()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/Worker) 的构造器，指定一个脚本的URI来执行worker线程（main.js）：

```js
var myWorker = new Worker('worker.js');
```

### 专用worker中消息的接收和发送

你可以通过`postMessage()`方法和`onmessage`事件处理函数触发workers的魔法。当你想要向一个worker发送消息时，你只需要这样做（main.js）：

```js
first.onchange = function() {
  myWorker.postMessage([first.value,second.value]);
  console.log('Message posted to worker');
}

second.onchange = function() {
  myWorker.postMessage([first.value,second.value]);
  console.log('Message posted to worker');
}
```

这段代码中变量first和second代表2个[``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input)元素；它们当中任意一个的值发生改变时，myWorker.postMessage([first.value,second.value])会将这2个值组成数组发送给worker。你可以在消息中发送许多你想发送的东西。

在worker中接收到消息后，我们可以写这样一个事件处理函数代码作为响应（worker.js）：

```js
onmessage = function(e) {
  console.log('Message received from main script');
  var workerResult = 'Result: ' + (e.data[0] * e.data[1]);
  console.log('Posting message back to main script');
  postMessage(workerResult);
}
```

onmessage处理函数允许我们在任何时刻，一旦接收到消息就可以执行一些代码，代码中消息本身作为事件的data属性进行使用。这里我们简单的对这2个数字作乘法处理并再次使用postMessage()方法，将结果回传给主线程。

回到主线程，我们再次使用onmessage以响应worker回传的消息：

```js
myWorker.onmessage = function(e) {
  result.textContent = e.data;
  console.log('Message received from worker');
}
```

在这里我们获取消息事件的data，并且将它设置为result的textContent，所以用户可以直接看到运算的结果。