---
title: 回溯算法
top_img: '/CDN/post/19.jpg'
cover: '/CDN/post/19.jpg'
tags:
  - JavaScrip
  - 回溯算法
categories:
  - 算法和数据结构
abbrlink: 60033
---

**回溯算法**也叫试探法，它是一种系统地搜索问题的解的方法。回溯算法实际上一个类似枚举的搜索尝试过程，主要是在搜索尝试过程中寻找问题的解，当发现已不满足求解条件时，就“回溯”返回，尝试别的路径。

## 用回溯算法解决问题的一般步骤：

- 1、针对所给问题，定义问题的解空间，它至少包含问题的一个（最优）解。
- 2、确定易于搜索的解空间结构,使得能用回溯法方便地搜索整个解空间 。
- 3、以深度优先的方式搜索解空间，并且在搜索过程中用剪枝函数避免无效搜索。

## BFS+回溯实例

![电话号码字母组合](https://s3.ax1x.com/2020/11/13/D9c3AU.md.png)

```js
/**
 * @param {string} digits
 * @return {string[]}
 */
var letterCombinations = function (digits) {
  if (digits.length == 0) return [];
  let res = [];
  const map = {
    2: 'abc',
    3: 'def',
    4: 'ghi',
    5: 'jkl',
    6: 'mno',
    7: 'pqrs',
    8: 'tuv',
    9: 'wxyz',
  };
  const dfs = (curStr, i) => {
      //设置数组越界后，输出一个解并跳出本次回溯的分支
    if (i > digits.length - 1) {
      res.push(curStr);
      return;
    }
    //提取当前i的字符串，并建立分支，进行回溯递归
    const letters = map[digits[i]];
    for (const l of letters) {
      dfs(curStr + l, i + 1);
    }
  };
  dfs('', 0);//递归入口
  return res;
};
```
