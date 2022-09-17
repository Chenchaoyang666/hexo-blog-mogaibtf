---
title: JavaScript中的全局对象
top_img: '/CDN/post/15.jpg'
cover: '/CDN/post/15.jpg'
tags:
  - JavaScrip
categories:
  - 前端
abbrlink: 43529
date: 2020-11-04 00:00:00
---

JavaScript 中有一个特殊的对象，称为全局对象（Global Object），它及其所有属性都可以在程序的任何地方访问，即全局变量。
<!-- more -->
## 浏览器中它被命名为 window
window 及 window 对象上的所有属性都是全局变量。

### 例1
```js
var env = 'Browser';
console.log(env);
// Browser
console.log(window.env);
// Browser
```

### 例2
```js
env = 'Browser';
console.log(env);
// Browser
console.log(window.env);
// Browser
```
+ 在全局作用域下使用或不使用 var 关键字定义变量都会被附加到 window 对象上成为全局变量
+ 例 1 和 例 2 中的 env 都是全局变量，打印 env 和 window.env 会得到相同结果

## 对 Node.JS 而言是 global
global 及 global 对象上的所有属性都是全局变量。

### 例3
```js
var env = 'Node.js';
console.log(env);
// Node.js
console.log(global.env);
// Node.js
```

### 例4
```js
env = 'Node.js';
console.log(env);
// Node.js
console.log(global.env);
// undefined
```
+ 在 Node.js 中，每个模块都有自己的作用域，在模块的顶层作用域下，使用 var 关键字定义的变量不会被附加到 global 对象上，不使用 var 关键字定义的变量会被附加到 global 对象上成为全局变量
+ 例 3 中的 env 是全局变量，打印 env 和 global.env 会得到相同结果，例 4 中的 env 不是全局变量，global 对象中不存在 env 属性

## One More Thing

使用 `<script type="module">` 定义的 JavaScript 浏览器脚本，被认为是个单独的模块，它有自己的顶级作用域，使用 var 关键字定义变量不会被附加到 window 对象上。
在 Node.js 的 REPL（Read Eval Print Loop：交互式解释器）中，使用或不使用 var 关键字定义变量都会被附加到 global 对象上成为全局变量。