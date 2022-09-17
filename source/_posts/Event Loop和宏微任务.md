---
title: JS运行机制
top_img: '/CDN/wallpaper/cover_8.jpg'
cover: '/CDN/wallpaper/cover_8.jpg'
tags:
  - JavaScrip
categories:
  - 前端
abbrlink: 43184
---
## 概念1： JS是单线程执行
”JS是单线程的”指的是JS 引擎线程。

> 在浏览器环境中，有JS 引擎线程和渲染线程，且两个线程互斥。
> Node环境中，只有JS 线程。

## 概念2：宿主
JS运行的环境。一般为浏览器或者Node。

## 概念3：Event Loop
![](/CDN/post/eventloop.webp)
> JS引擎常驻于内存中，等待宿主将JS代码或函数传递给它。
> 也就是等待宿主环境分配宏观任务，反复等待 - 执行即为事件循环。

Event Loop中，每一次循环称为tick，每一次tick的任务如下：

- 执行栈选择最先进入队列的宏任务（一般都是script），执行其同步代码直至结束；
- 检查是否存在微任务，有则会执行至微任务队列为空；
- 如果宿主为浏览器，可能会渲染页面；
- 开始下一轮tick，执行宏任务中的异步代码（setTimeout等回调）。

## 概念4：宏任务和微任务
> ES6 规范中，microtask 称为 jobs，macrotask 称为 task
> 宏任务是由宿主发起的，而微任务由JavaScript自身发起。

在ES3以及以前的版本中，JavaScript本身没有发起异步请求的能力，也就没有微任务的存在。在ES5之后，JavaScript引入了Promise，这样，不需要浏览器，JavaScript引擎自身也能够发起异步任务了。

所以，总结一下，两者区别为：
![](/CDN/post/task.png)

## 拓展
### setTimeout，setImmediate谁先执行？
`setImmediate`和`process.nextTick`为Node环境下常用的方法（IE11支持`setImmediate`），所以，后续的分析都基于Node宿主。

Node.js是运行在服务端的js，虽然用到也是V8引擎，但由于服务目的和环境不同，导致了它的API与原生JS有些区别，其Event Loop还要处理一些I/O，比如新的网络连接等，所以与浏览器Event Loop不太一样。

执行顺序如下：

> timers: 执行setTimeout和setInterval的回调
> pending callbacks: 执行延迟到下一个循环迭代的 I/O 回调
> idle, prepare: 仅系统内部使用
> poll: 检索新的 I/O 事件;执行与 I/O 相关的回调。事实上除了其他几个阶段处理的事情，其他几乎所有的异步都在这个阶段处理。
> check: setImmediate在这里执行
> close callbacks: 一些关闭的回调函数，如：socket.on('close', ...)
一般来说，`setImmediate`会在`setTimeout`之前执行，如下：
```js
console.log('outer');
setTimeout(() => {
  setTimeout(() => {
    console.log('setTimeout');
  }, 0);
  setImmediate(() => {
    console.log('setImmediate');
  });
}, 0);
```
其执行顺序为：

- 外层是一个`setTimeout`，所以执行它的回调的时候已经在timers阶段了
- 处理里面的`setTimeout`，因为本次循环的timers正在执行，所以其回调其实加到了下个timers阶段
- 处理里面的`setImmediate`，将它的回调加入check阶段的队列
- 外层timers阶段执行完，进入pending callbacks，idle, prepare，poll，这几个队列都是空的，所以继续往下
- 到了check阶段，发现了`setImmediate`的回调，拿出来执行
- 然后是close callbacks，队列是空的，跳过
- 又是timers阶段，执行console.log('`setTimeout`')

但是，如果当前执行环境不是timers阶段，就不一定了。。。。顺便科普一下Node里面对`setTimeout`的特殊处理：`setTimeout`(fn, 0)会被强制改为`setTimeout`(fn, 1)。

看看下面的例子：
```js
setTimeout(() => {
  console.log('setTimeout');
}, 0);

setImmediate(() => {
  console.log('setImmediate');
});
```
其执行顺序为：

- 遇到`setTimeout`，虽然设置的是0毫秒触发，但是被node.js强制改为1毫秒，塞入times阶段
- 遇到`setImmediate`塞入check阶段
- 同步代码执行完毕，进入Event Loop
- 先进入times阶段，检查当前时间过去了1毫秒没有，如果过了1毫秒，满足`setTimeout`条件，执行回调，如果没过1毫秒，跳过
- 跳过空的阶段，进入check阶段，执行`setImmediate`回调

可见，1毫秒是个关键点，所以在上面的例子中，`setImmediate`不一定在`setTimeout`之前执行了。

{% note info flat %}
因为process.nextTick为Node环境下的方法，所以后续的分析依旧基于Node。
process.nextTick() 是一个特殊的异步API，其不属于任何的Event Loop阶段。事实上Node在遇到这个API时，Event Loop根本就不会继续进行，会马上停下来执行process.nextTick()，这个执行完后才会继续Event Loop。
所以，nextTick和Promise同时出现时，肯定是nextTick先执行，原因是nextTick的队列比Promise队列优先级更高。
{% endnote %}

{% note info flat %}
promise的内部既包含宏任务也包含微任务，promise内部执行为宏任务，then执行为微任务
{% endnote %}

{% note info flat %}
第一轮宏任务打印：script start 、async2 end、 Promise 、script end
第一轮微任务打印：async1 end 、promise1、promise2
（此时微任务队列清空，且存在其他宏任务，进入下一轮事件循环）
第二轮宏任务： setTimeout等
{% endnote %}
