---
title: hexo主题分页问题
top_img: '/CDN/post/13.jpg'
cover: '/CDN/post/13.jpg'
tags:
  - hexo
categories:
  - 其他
abbrlink: 65246
---
辅助函数 paginator 能够帮助我们插入分页链接，具体参数查看[https://hexo.io/docs/helpers.html#paginator](https://hexo.io/docs/helpers.html#paginator)

hexo博客搭建：[https://zhuanlan.zhihu.com/p/26625249/](https://zhuanlan.zhihu.com/p/26625249/)

<!-- more -->

## 问题

分页标签显示为 HTML 代码如：`<i class="nexmoefont icon-left"></i>`

## 解决办法

找到 pagination.ejs 中的 paginator 函数，添加 escape: false 试试，完整代码形如：

```
<%- paginator({
      prev_text: '<i class="iconfont icon-arrow-left"></i>',
      next_text: '<i class="iconfont icon-arrow-right"></i>',
      escape: false
    }) %>

```

原文参考：https://www.zhihu.com/question/370971597/answer/1147674723
