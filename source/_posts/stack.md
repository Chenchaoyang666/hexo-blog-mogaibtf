---
title: 数据结构-Stack
top_img: '/CDN/post/6.jpg'
cover: '/CDN/post/6.jpg'
tags:
  - JavaScrip
  - 栈
categories:
  - 算法和数据结构
abbrlink: 12893
date: 2020-11-03 16:23:25
---

Stack 是一种遵从**后进后出（LIFO）**原则的有序集合。

<!-- more -->

## 栈与队列

用 JavaScrip 写一个 Stack 的类，实现栈的常用方法 push、pop 等

```js
export default class Stack {
  constructor() {
    this.count = 0;
    this.items = {};
  }

  push(element) {
    this.items[this.count] = element;
    this.count++;
  }

  pop() {
    if (this.isEmpty()) {
      return undefined;
    }
    this.count--;
    const result = this.items[this.count];
    delete this.items[this.count];
    return result;
  }

  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.count - 1];
  }

  isEmpty() {
    return this.count === 0;
  }

  size() {
    return this.count;
  }

  clear() {
    this.items = {};
    this.count = 0;
  }

  toString() {
    if (this.isEmpty()) {
      return '';
    }
    let objString = `${this.items[0]}`;
    for (let i = 1; i < this.count; i++) {
      objString = `${objString},${this.items[i]}`;
    }
    return objString;
  }
}
```
