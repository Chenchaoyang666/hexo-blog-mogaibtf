---
title: css清浮动
top_img: "/CDN/post/5.jpg"
cover: "/CDN/post/5.jpg"
tags:
  - Css
categories:
  - 前端
abbrlink: 17955
---

人们经常谈起清浮动，其实就是解决浮动元素的包含块高度塌陷的问题

## 【1】clear 属性

值: `left | right | both | none | inherit`
初始值: none
应用于: 块级元素(块级元素指 block 元素，不包括 inline-block 元素)
继承性: 无

```
    left:左侧不允许存在浮动元素
    right:右侧不允许存在浮动元素
    both:左右两侧不允许存在浮动元素
    none:允许左右两侧存在浮动元素
```

- `<div style="clear:both"></div>`<注意>并不是很适用，若包含块为`<ul>`，则子元素只能为`<li>`，则在`<li>`后面添加`<div>`元素不合适
- `<br style="clear:both">`<注意>虽然 clear 属性只应用于块级元素，但在除 IE7-以外的其他浏览器都可以将 clear 属性应用于`<br>`元素
- 为浮动元素的 after 伪元素设置 clear 属性`.clear:after{content:""; display: block; clear: both;}`

## 【2】BFC

- 1、float: left/right

- 2、position:absolute/fixed

- 3、display:inline-block/table-cell/table-caption/flex

- 4、overflow:hidden/scroll/auto

[关于 BFC 的详细信息移步至此](https://www.cnblogs.com/xiaohuochai/p/5248536.html)

## 【3】IE7-

关于 IE7-浏览器有一个其特有的属性 haslayout，当触发包含块的 haslayout 时，浮动元素被 layout 元素自动包含

- 1、display:inline-block

- 2、height/width:除 auto 外

- 3、float: left/right

- 4、position: absolute

- 5、writing-mode: tb-rl

- 6、zoom: 除 normal 外

[关于 haslayout 的详细信息移步至此](https://www.cnblogs.com/xiaohuochai/p/4845314.html)
