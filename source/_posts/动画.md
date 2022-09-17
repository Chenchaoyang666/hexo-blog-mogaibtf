---
title: css动画(transition)
top_img: "/CDN/post/1.jpg"
cover: "/CDN/post/1.jpg"
tags:
  - Css
categories:
  - 前端
abbrlink: 62887
---

## 漂浮的白云

- 【效果演示】
<iframe style="width: 100%; height: 320px;" src="https://demo.xiaohuochai.site/css/animationApply/a1.html" frameborder="0" width="320" height="240"></iframe>

- 【简要介绍】
  漂浮的白云主要通过远景白云和近景白云来实现立体漂浮效果。远景和近景分别使用两张背景图片，通过改变其背景定位来实现白云移动效果，通过设置不同的动画持续时间来实现交错漂浮的效果
- 【主要代码】

```css
.box {
  position: relative;
  height: 300px;
  width: 500px;
}
.in1,
.in2 {
  position: absolute;
  height: 100%;
  width: 100%;
  background-size: cover;
  animation: move 100s infinite linear alternate;
}
@keyframes move {
  100% {
    background-position: 500% 0;
  }
}
.in1 {
  background-image: url("http://sandbox.runjs.cn/uploads/rs/26/ddzmgynp/cloud.png");
}
.in2 {
  background-image: url("http://sandbox.runjs.cn/uploads/rs/26/ddzmgynp/cloud1.png");
  animation-duration: 10s;
}
```

```html
<div class="box">
  <div class="in1"></div>
  <div class="in2"></div>
</div>
```

## 旋转的星球

- 【效果演示】
<iframe style="width: 100%; height: 430px;" src="https://demo.xiaohuochai.site/css/animationApply/a2.html" frameborder="0" width="320" height="240"></iframe>

- 【简要介绍】
  旋转的星球主要通过 rotate()旋转函数来实现。实际上，蓝色的地球和黑色的月球并没有发生旋转，只是其父级旋转形成的视觉上的旋转效果
- 【主要代码】

```css
.box {
  transform: scale(0.5);
  position: relative;
  padding: 1px;
  height: 300px;
  width: 300px;
}
.sunline {
  position: relative;
  height: 400px;
  width: 400px;
  border: 2px solid black;
  border-radius: 50%;
  margin: 50px 0 0 50px;
  display: flex;
  animation: rotate 10s infinite linear;
}
.sun {
  height: 100px;
  width: 100px;
  margin: auto;
  background-color: red;
  border-radius: 50%;
  box-shadow: 5px 5px 10px red, -5px -5px 10px red, 5px -5px 10px red, -5px 5px
      10px red;
}
.earthline {
  position: absolute;
  right: 0;
  top: 50%;
  height: 200px;
  width: 200px;
  margin: -100px -100px 0 0;
  border: 1px solid black;
  border-radius: 50%;
  display: flex;
  animation: rotate 2s infinite linear;
}
.earth {
  margin: auto;
  height: 50px;
  width: 50px;
  background-color: blue;
  border-radius: 50%;
}
.moon {
  position: absolute;
  left: 0;
  top: 50%;
  height: 20px;
  width: 20px;
  margin: -10px 0 0 -10px;
  background-color: black;
  border-radius: 50%;
}
@keyframes rotate {
  100% {
    transform: rotate(360deg);
  }
}
```

```html
<div class="box">
  <div class="sunline">
    <div class="sun"></div>
    <div class="earthline">
      <div class="earth"></div>
      <div class="moon"></div>
    </div>
  </div>
</div>
```

-【进阶】
如果要在内侧旋转的球内放文本，并且文本不跟着旋转，则代码如下

```css
@keyframes spin {
  100% {
    transform: rotate(1turn);
  }
}
.outer {
  width: 100px;
  height: 100px;
  background-color: pink;
  border-radius: 50%;
  animation: spin 3s linear infinite;
  animation-play-state: running;
  text-align: center;
}
.inner {
  width: 40px;
  height: 40px;
  line-height: 40px;
  background-color: tan;
  border-radius: 50%;
  animation: inherit;
  animation-direction: reverse;
}
div:hover,
div:focus {
  animation-play-state: paused;
}
```

鼠标移入后，动画停止；移出时，动画继续

<iframe style="width: 100%; height: 160px;" src="https://demo.xiaohuochai.site/css/animationApply/a3.html" frameborder="0" width="320" height="240"></iframe>

## 正方体合成

- 【效果演示】
<iframe style="width: 100%; height: 300px;" src="https://demo.xiaohuochai.site/css/animationApply/a4.html" frameborder="0" width="320" height="240"></iframe>

## 使用 animate.css

animate.css 是一个使用 CSS3 的 animation 制作的动画效果的 CSS 集合，里面预设了很多种常用的动画，且使用非常简单

- 从官网下载

[https://raw.github.com/daneden/animate.css/master/animate.css](https://raw.github.com/daneden/animate.css/master/animate.css)

- 通过 npm 安装

`$ npm install animate.css`

- 使用在线 cdn

[https://unpkg.com/animate.css@3.5.2/animate.min.css](https://unpkg.com/animate.css@3.5.2/animate.min.css)
