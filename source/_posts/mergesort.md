---
title: 排序算法-归并排序
top_img: '/CDN/post/14.jpg'
cover: '/CDN/post/14.jpg'
tags:
  - JavaScrip
  - 排序
  - 分而治之
categories:
  - 算法和数据结构
abbrlink: 51816
---

**归并排序**第一个可以实际使用排序算法之一。时间复杂度为 O(nlog(n)),空间复杂度为 O(n)。

<!-- more -->

## 设置比较函数

```js
function compareFn(a, b) {
  if (a === b) {
    return 0;
  }
  return a < b ? -1 : 1;
}
```

## 主函数负责用递归法将一个大数组分成多个小数组，并调用辅助函数进行排序、合并

```js
function mergeSort(array, compareFn = defaultCompare) {
  if (array.length > 1) {
    const { length } = array; // 获取数组长度，参照ES6语法
    const middle = Math.floor(length / 2);
    const left = mergeSort(array.slice(0, middle), compareFn);
    const right = mergeSort(array.slice(middle, length), compareFn);
    array = merge(left, right, compareFn);
  }
  return array;
}
```

## merge 函数负责合并小数组来产生大数组，直到回到原始数组

```js
function merge(left, right, compareFn) {
  let i = 0;
  let j = 0;
  const result = [];
  while (i < left.length && j < right.length) {
    result.push(
      compareFn(left[i], right[j]) === Compare.LESS_THAN
        ? left[i++]
        : right[j++]
    );
  }
  return result.concat(i < left.length ? left.slice(i) : right.slice(j));
}
```
