---
layout: _post
title: JavaScript事件循环笔记
date: 2020-06-19 16:14:15
tags:
	- JavaScript
categories:
    - Web前端
cover: https://cdn.jsdelivr.net/gh/aizener/my-imgs@master/blog/bg2.jpg
---

> JavaScript最大的一个特点就是：异步。那么，JavaScript是怎么实现异步编程的呢，那这就不得不说道**事件循环**，这里我简单的说一下我所理解的JavaScript的事件循环机制。

#### 执行栈

JavaScript有一个**执行栈**，来执行我们的代码，当然这个执行栈是有目标的去执行的，例如：

```js
console.log(1)
console.log(2)
```

上面两句代码，通过执行栈是依次打印出：1和2，如果说当遇到`setTimeout`这种异步函数的话，执行顺序就不同了：

```js
console.log(1)
setTimeout(() => {
    console.log(3)
}, 0)
console.log(2)
```

这一次，就不会从上到下依次打印：1,2,3了，尽管`setTimeout`的延迟为0，也会出现在最后，其结果为：1,2,3。那么，这是为什么呢？说道这里了，就得介绍下面这个东西了。

#### 消息队列

当JavaScript的**执行栈**遇到诸如`setTimeout`异步函数时，会把他的**回调函数**放入消息队列当中去，等到执行栈的代码执行完毕后，再从**消息队列**放到**执行栈**里面去执行。

所以，上面执行的过程是：

1. 执行栈执行console.log(1)；
2. 执行栈遇到setTimeout，把setTimeout的回调函数放到消息队列里面去: () => {console.log(3)};
3. 执行栈执行console.log(2):
4. 执行栈执行完毕，消息队列的回调函数放到执行栈来执行，执行回调函数打印console.log(3)。

是不是就挺明了了？那么看一下下面这种情况：

```js
console.log(1)
setTimeout(() => {
    console.log(3)
}, 0)
new Promise((resolve, reject) => {
    resolve(4)
}).then((res) => {
    console.log(res)
})
setTimeout(() => {
    console.log(3)
    setTimeout(() => {
        console.log(4)
    })
}, 0)
console.log(2)
```

既然`Promise`也是异步函数，那么按道理是不是应该打印：1,2,3,4呢？答案是：否。

虽然`Promise`也是异步函数，但是异步分为两类：

**一类是宏任务（micro），另一类是微任务（macro）**

`setTimeout, setInterval`这类的便是宏任务，`async, Promise`这类的便是微任务，那么这有什么区别呢？

#### 微任务队列

我们知道JavaScript的**执行栈**执行完毕之后，会从**消息队列**里面弹出回调函数继续执行，但其实中间还有一步，那就是**微任务队列**。

执行栈遇到**微任务**时，会把**微任务**放到**微任务队列**里面去，每当执行栈执行完毕之后，会**立即**弹出**微任务**队列里面的回调函数来执行，所以上面的步骤应该是：

1.  打印console.log(1)；
2.  把console.log(3)；放到消息队列里面去；
3. 把console.log(res)；放到微任务队列里面去；
4. 打印console.log(4)；
5. 执行栈空了，立即执行console.log(res)；
6. 执行栈空了，执行消息队列里面的回调。

**事件循环**

综上所述，当**主程序**开始执行时，就作为一次**宏任务**开始，**执行栈**执行空时，再弹出**微任务队列**里面的执行完毕，这一次就是一个完整的**执行顺序**；

当再次弹出**消息队列**的待执行语句时，其实已经开始了新一回的执行了，即为**事件循环**。