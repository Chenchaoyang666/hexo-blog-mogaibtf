---
title: css过渡(transition)
top_img: '/CDN/post/3.jpg'
cover: '/CDN/post/3.jpg'
tags:
  - Css
categories:
  - 前端
abbrlink: 24228
---

## 定义

- 过渡 transition 是一个复合属性，包括 `transition-property`、`transition-duration`、`transition-timing-function`、`transition-delay` 这四个子属性。通过这四个子属性的配合来完成一个完整的过渡效果

```css
transition-property: 过渡属性(默认值为all)
transition-duration: 过渡持续时间(默认值为0s)
transiton-timing-function: 过渡函数(默认值为ease函数)
transition-delay: 过渡延迟时间(默认值为0s)
```

- 过渡 transition 的这四个子属性只有`<transition-duration>`是必需值且不能为 0。其中，`<transition-duration>`和`<transition-delay>`都是时间。当两个时间同时出现时，第一个是`<transition-duration>`，第二个是`<transition-delay>`；当只有一个时间时，它是`<transition-duration>`，而`<transition-delay>`为默认值 0

```css
transition: <transition-property> || <transition-duration> ||
  <transition-timing-function> || <transition-delay>;
```

## 效果展示

```css
#test1 {
  transition-property: width, background, opacity;
  transition-duration: 2s, 500ms;
  transition-timing-function: linear, ease;
  transition-delay: 200ms, 0s;
}
/*类似于*/
#test2 {
  transition: width 2s linear 200ms, background 500ms ease 0s,
    opacity 2s linear 200ms;
}
```

<iframe style="width: 100%; height: 250px;" src="https://demo.xiaohuochai.site/css/transition/t10.html" frameborder="0" width="320" height="240"></iframe>
