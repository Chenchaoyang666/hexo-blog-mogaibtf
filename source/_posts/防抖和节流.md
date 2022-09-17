---
title: 防抖和节流
top_img: '/CDN/post/2.jpg'
cover: '/CDN/post/2.jpg'
tags:
  - JavaScrip
categories:
  - 前端
abbrlink: 35375
---

## 场景描述

**功能需求**-监听浏览器滚动事件，返回当前滚条与顶部的距离

```js
function showTop() {
  var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
  console.log("滚动条位置：" + scrollTop);
}
window.onscroll = showTop;
```

## 防抖(debounce)

触发高频事件后 n 秒函数只会执行一次，如果 n 秒内高频事件再次被触发，则重新计算时间

```js
function debounce(fn, delay) {
  let timer = null;
  return function () {
    if (timer) {
      clearTimeout(timer);
    }
    timer = setTimeout(fn, delay);
  };
}

function showTop() {
  var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
  console.log("滚动条位置：" + scrollTop);
}
window.onscroll = debounce(showTop, 1000);
```

## 节流

高频事件触发，但在 n 秒内只会执行一次，所以节流会稀释函数的执行频率

```js
function throttle(fn, delay) {
  let valid = true;
  return function () {
    if (!valid) {
      //休息时间 暂不接客
      return false;
    }
    // 工作时间，执行函数并且在间隔期内把状态位设为无效
    valid = false;
    setTimeout(() => {
      fn();
      valid = true;
    }, delay);
  };
}
/* 请注意，节流函数并不止上面这种实现方案,
   例如可以完全不借助setTimeout，可以把状态位换成时间戳，然后利用时间戳差值是否大于指定间隔时间来做判定。
   也可以直接将setTimeout的返回的标记当做判断条件-判断当前定时器是否存在，如果存在表示还在冷却，并且在执行fn之后消除定时器表示激活，原理都一样
    */
function showTop() {
  var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
  console.log("滚动条位置：" + scrollTop);
}
window.onscroll = throttle(showTop, 1000);
```

## 其他应用场景举例

- 搜索框 input 事件，例如要支持输入实时搜索可以使用节流方案（间隔一段时间就必须查询相关内容），或者实现输入间隔大于某个值（如 500ms），就当做用户输入完成，然后开始搜索，具体使用哪种方案要看业务需求。
- 页面 resize 事件，常见于需要做页面适配的时候。需要根据最终呈现的页面情况进行 dom 渲染（这种情形一般是使用防抖，因为只需要判断最后一次的变化情况）
