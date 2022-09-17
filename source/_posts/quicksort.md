---
title: 排序算法-快速排序
top_img: '/CDN/post/7.jpg'
cover: '/CDN/post/7.jpg'
tags:
  - JavaScrip
  - 排序
  - 分而治之
categories:
  - 算法和数据结构
abbrlink: 7837
---

**快速排序**最常用的排序算法之一。时间复杂度一般为 O(nlog(n)),空间复杂度为O(1)(左右指针交替的原地排序)。

<!-- more -->

快速排序和[归并排序](/posts/51816)一样也使用[分而治之]()的方法。

## 设置比较函数和交换函数

- 自定义一个 compare 函数，命名为 compareFn

```js
function compareFn(a, b) {
  if (a === b) {
    return 0;
  }
  return a < b ? -1 : 1;
}
```
- 自定义一个交换函数，命名为 swap

```js
function swap(array,a,b){
  //经典方式
  // const temp = array[a];
  // array[a] = array[b];
  // array[b] = temp;

  //ES6 的方式
  [array[a],array[b]] = [array[b],array[a]];
}
```

## 划分过程

- 随机选取其中一个值或选择中间值做为主元，初始化第一个值为 left 指针，最后一个值为 right 指针

```js
function partition(array, left, right, compareFn) {
  const pivot = array[Math.floor((right + left) / 2)];
  let i = left;
  let j = right;
```

- 移动 left 指针直到找到比主元大的元素。
- 同理，移动 right 指针直到找到一个比主元小的元素。

```js
while (i <= j) {
    while (compareFn(array[i], pivot) === Compare.LESS_THAN) {
      i++;
    }
    while (compareFn(array[j], pivot) === Compare.BIGGER_THAN) {
      j--;
    }
```

- 此时当 left 指针索引小于等于 right 指针索引时，进行交换,继续移动指针，重复此过程，并返回最终左指针索引，用于递归函数创建子数组。

```js
    if (i <= j) {
      swap(array, i, j);
      i++;
      j--;
    }
  }
  return i;
}
```

## 递归函数重复对数组进行划分

```js
function quick(array, left, right, compareFn) {
  let index;
  if (array.length > 1) {
    index = partition(array, left, right, compareFn);
    if (left < index - 1) {
      quick(array, left, index - 1, compareFn);
    }
    if (index < right) {
      quick(array, index, right, compareFn);
    }
  }
  return array;
}
```

## 设置主函数调用递归函数

```js
function quickSort(array, compareFn) {
  return quick(array, 0, array.length - 1, compareFn);
}
```
