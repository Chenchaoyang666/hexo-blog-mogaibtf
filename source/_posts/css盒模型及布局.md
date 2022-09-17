---
title: css盒模型及布局
top_img: "https://s3.ax1x.com/2020/12/04/Dq2ucR.jpg"
cover: "https://s3.ax1x.com/2020/12/04/Dq2ucR.jpg"
highlight_shrink: true
tags:
  - Css
categories:
  - 前端
abbrlink: 17705
---

## 普通盒模型

在 CSS 中盒模型被分为两种，第一种是 W3C 的标准模型，第二种是 IE 怪异盒模型。不同之处在于后者的宽高定义的是可见元素框的尺寸，而不是元素框的内容区尺寸。目前对于浏览器大多数元素都是基于 W3C 标准的盒模型，但对于表单`form`中的部分元素还是基于 IE 的怪异盒模型，如`input`里的`radio`、`checkbox`、`button`等元素，如果给其设置`border`和`padding`它们也只会往元素盒内延伸
![](/CDN/post/1.png)
在 W3C 的标准模型下，宽度和高度仅仅包含了内容宽度，除去了边框和内边距两个区域，这样为 web 设计师处理效果带来了不少麻烦。为了解决这个问题，CSS3 新增了一个盒模型属性`box-sizing`，能够事先定义盒模型的尺寸解析方式

> box-sizing
>    值: content-box | bordrer-box | padding-box | inherit
>    初始值: content-box
>    应用于: 块级元素和替换元素
>    继承性: 无

{% note info flat %}
注意 1：只有 firefox 浏览器支持 padding-box 属性值
{% endnote %}
{% note info flat %}
注意 2：IE 浏览器在 getComputedStyle 得到 width/height 是按照标准模式计算的，而不论 box-sizing 的取值
{% endnote %}

## 弹性盒模型(flex)

要让一个元素变成伸缩容器，需要使用 display 属性。采用 flex 布局的元素，称为伸缩容器(flex container)，容器内的子元素称为伸缩项目(flex item)

```
//弹性盒模型: 块级伸缩容器 | 内联伸缩容器
//新版本
display: flex | inline-flex;
```

![](/CDN/post/CSS_flex.png)
【1】伸缩流方向:指定主轴的方向(即伸缩项目在伸缩容器中的排列方向)

```
//伸缩流方向: 水平方向 | 反向水平 | 垂直方向 | 反向垂直
//新版本同混合版本
flex-direction: row[默认] | row-reverse | column | column-reverse
```

【2】伸缩流换行:指定伸缩项目溢出伸缩容器时是否换行

```
//伸缩行换行:不换行 | 换行 | 反转换行
//新版本同混合版本
flex-wrap: nowrap[默认] | wrap | wrap-reverse
```

【3】伸缩流:伸缩流方向与伸缩行换行的缩写

```
//伸缩流: 伸缩流方向 | 伸缩行换行
//新版本同混合版本
flex-flow: <flex-direction> | <flex-wrap>
[默认值] flex-flow: row nowrap
```

【4】主轴对齐:用来设置伸缩容器当前行伸缩项目在主轴方向的对齐方式，指定如何在伸缩项目之间分布伸缩容器额外空间;当一行上的所伸缩项目不能伸缩或可伸缩已达到最大长度时，这一属性才会对伸缩容器额外空间进行分配。当伸缩项目溢出某一行时，这一属性也会在项目的对齐上施加一些控制

```
//主轴对齐方式: 左对齐 | 居中对齐 | 右对齐 | 两端对齐 | 扩散对齐
//新版本
justify-content: flex-start[默认] | center | flex-end | space-between | space-around
```

【5】侧轴对齐:用来设置伸缩容器当前行在侧轴方向的对齐方式

```
//侧轴对齐方式: 顶边对齐 | 中间对齐 | 底部对齐 | 基线对齐 | 伸缩项目拉伸填充整个伸缩容器
//新版本
align-items: flex-start | center | flex-end | baseline | stretch[默认]
```

【6】堆栈伸缩行:指定多个伸缩项目行在侧轴的对齐方式

```
//侧轴对齐方式: 顶边对齐 | 中间对齐 | 底部对齐 | 两端对齐 | 扩散对齐 | 伸缩项目拉伸填充整个伸缩容器
//新版本
align-content: flex-start | center | flex-end | space-between | space-around | stretch[默认]
```

### flex 兼容

{% note info flat %}
为适配旧版设备需要添加-webkit-前缀、 -ms- 或 -moz-
{% endnote %}
{% note info flat %}
flex 是 flex-grow、flex-shrink、flex-basis 的缩写
{% endnote %}

```css
/*display*/
.display_flex {
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
}
.display_flex > * {
  display: block;
}
.display_inline-flex {
  display: -webkit-inline-box;
  display: -ms-inline-flexbox;
  display: -webkit-inline-flex;
  display: inline-flex;
}
.display_inline-flex > * {
  display: block;
}
/*伸缩流方向*/
.flex-direction_column {
  -webkit-box-orient: vertical;
  -ms-flex-direction: column;
  -webkit-flex-direction: column;
  flex-direction: column;
}
/*主轴对齐*/
.justify-content_flex-center {
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  -webkit-justify-content: center;
  justify-content: center;
}
.justify-content_flex-end {
  -webkit-box-pack: end;
  -ms-flex-pack: end;
  -webkit-justify-content: flex-end;
  justify-content: flex-end;
}
.justify-content_flex-justify {
  -webkit-box-pack: justify;
  -ms-flex-pack: justify;
  -webkit-justify-content: space-between;
  justify-content: space-between;
}
/*侧轴对齐*/
.align-items_flex-start {
  -webkit-box-align: start;
  -ms-flex-align: start;
  -webkit-align-items: flex-start;
  align-items: flex-start;
}
.align-items_flex-end {
  -webkit-box-align: end;
  -ms-flex-align: end;
  -webkit-align-items: flex-end;
  align-items: flex-end;
}
.align-items_center {
  -webkit-box-align: center;
  -ms-flex-align: center;
  -webkit-align-items: center;
  align-items: center;
}
.align-items_baseline {
  -webkit-box-align: baseline;
  -ms-flex-align: baseline;
  -webkit-align-items: baseline;
  align-items: baseline;
}
/*伸缩性*/
.flex_auto {
  -webkit-box-flex: 1;
  -ms-flex: auto;
  -webkit-flex: auto;
  flex: auto;
}
.flex_1 {
  width: 0;
  -webkit-box-flex: 1;
  -ms-flex: 1;
  -webkit-flex: 1;
  flex: 1;
}
/*显示顺序*/
.order_2 {
  -webkit-box-ordinal-group: 2;
  -ms-flex-order: 2;
  -webkit-order: 2;
  order: 2;
}
.order_3 {
  -webkit-box-ordinal-group: 3;
  -ms-flex-order: 3;
  -webkit-order: 3;
  order: 3;
}
```

## 布局系统

### css Media 媒体查询

【媒体类型】

```
screen         计算机屏幕（默认值）
tty            电传打字机以及使用等宽字符网格的类似媒介
tv             电视类型设备（低分辨率、有限的屏幕翻滚能力）
projection     放映机
handheld       手持设备（小屏幕、有限的带宽）
print          打印预览模式 / 打印页
braille        盲人用点字法反馈设备
aural          语音合成器
all            适合所有设备
```

【媒体属性】

```
width | min-width | max-width
     height | min-height | max-height
     device-width | min-device-width | max-device-width
     device-height | min-device-height | max-device-height
     aspect-ratio | min-aspect-ratio | max-aspect-ratio
     device-aspect-ratio | min-device-aspect-ratio | max-device-aspect-ratio
     color | min-color | max-color
     color-index | min-color-index | max-color-index
     monochrome | min-monochrome | max-monochrome
     resolution | min-resolution | max-resolution
     scan | grid
```

【辑操作符】

操作符 not、and、only 和逗号(,)(相当于 or)可以用来构建复杂的媒体查询

### css 多列布局

### grid 栅格布局

![](/CDN/post/CSS_grid.png)

### 移动优先的响应式布局

【设计原则】
关于响应式设计，有渐进增加和优雅降级两个设计原则

- 渐进增强(progressive enhancement)
  是指基本需求得到满足、实现，再根据不同浏览器及不同分辨率设备的特点，利用高级浏览器下的新特性提供更好的体验。比如，圆角、阴影、动画等

- 优雅降级(graceful degradation)
  则正好相反，现有功能已经开发完备，但需要向下兼容版本和不支持该功能的浏览器。虽然兼容性方案的体验不如常规方案，但保证了功能可用性

> 移动优先的响应式布局采用的是渐进增强原则，制作响应式网站时，先搞定手机版，然后再去为更大设备去设计和开发更复杂的功能。特征是使用 min-width 匹配页面宽度。从上到下书写样式时，首先考虑的是移动设备的使用场景，默认查询的是最窄的情况，再依次考虑设备屏幕逐渐变宽的情况

【三要素】
响应式设计包括三个要素：弹性布局、媒体查询和弹性图片(最简单的响应式图片设置 max-width 为 100%即可)

## 居中布局(以 flex 为例)

- 水平居中
  【1】在伸缩容器上设置主轴对齐方式`justify-content:center`
  【2】在伸缩项目上设置`margin: 0 auto`
- 垂直居中
  【1】在伸缩容器上设置侧轴对齐方式`align-items: center`
  【2】在伸缩项目上设置`margin: auto 0`
- 同时实现水平居中和垂直居中(加起来就行)

## 常见布局方式

三栏式布局(所谓的圣杯和双飞翼)

```CSS
<style>
body,p{margin: 0;}
.top,.bottom{height: 30px;}
.middle{display: flex;}
.left,.right{width: 100px;}
.right{order: 2;}
.main{
    order: 1;
    flex: 1;
    margin: 0 20px;
}
</style>
```

```HTML
<div class="parent" id="parent" style="background-color: lightgrey;">
    <div class="top" style="background-color: lightblue;">
        <p>top</p>
    </div>
    <div class="middle" style="background-color: pink;">
        <div class="main" style="background-color: lightcoral;">
            <p>main</p>
            <p>main</p>
        </div>
        <div class="left" style="background-color: lightseagreen;" >
            <p>left</p>
        </div>
        <div class="right" style="background-color: lightcyan;" >
               <p>right</p>
        </div>
    </div>
    <div class="bottom" style="background-color: lightgreen;">
        <p>bottom</p>
    </div>
</div>
```

<iframe style="width: 100%; height: 110px;" src="https://demo.xiaohuochai.site/css/buju4/b5.html" frameborder="0" width="320" height="240"></iframe>
