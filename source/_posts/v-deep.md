---
title: 'vue样式穿透 ::v-deep的具体使用'
top_img: '/CDN/wallpaper/cover_2.jpg'
cover: '/CDN/wallpaper/cover_2.jpg'
tags:
  - JavaScrip
  - Vue
categories:
  - 前端
abbrlink: 15950
---
这篇文章主要介绍了vue样式穿透 ::v-deep的具体使用，即使目前项目大多使用组件库，但在项目中肯定也需要用额外的样式来打造自己的应用。直接在 `<style lang="scss" scoped> .... </style>` 中编写的话只会影响当前组件内的样式，但如果去掉scoped话又会影响全局样式。想了好多方法，都没得到很好的解决。

百度之后发现 可以用` /deep/`或`::v-deep`来解决（不过在vue3.0的环境下，使用/deep/时，编译会报错）。没想到官方文档中其实早就给出了解决方案，怪自己没有认真看过文档，对vue的掌握还是不够熟悉啊，得好好学习了。

## 深度作用选择器
如果你希望 scoped 样式中的一个选择器能够作用得“更深”，例如影响子组件，你可以使用` >>> `操作符：
`<style scoped> .a >>> .b { /* ... */ } </style> `上述代码将会编译成：
`.a[data-v-f3f3eg9] .b { /* … */ }`

有些像 Sass 之类的预处理器无法正确解析 `>>>`。这种情况下你可以使用 `/deep/` 或 `::v-deep` 操作符取而代之——两者都是 `>>>` 的别名，同样可以正常工作。

- 1、>>>
如果vue的style使用的是css，那么则
```css
<style lang="css" scoped>
.a >>> .b { 
 /* ... */ 
}
</style>
```

- 2、/deep/
```css
<style lang="scss" scoped>
.a{
 /deep/ .b { 
  /* ... */ 
 }
} 
</style>
```

- 3、::v-deep(切记必须是双冒号) 
但是有些开发者反应，在vue-cli3编译时，deep的方式会报错或者警告。此时我们可以使用第三种方式
```css
<style lang="scss" scoped>
.a{
 ::v-deep .b { 
  /* ... */ 
 }
} 
</style>
```

- 使用场景:
当我们需要覆盖element-ui中的样式时只能通过深度作用选择器
```code
style为css时的写法如下

 .a >>> .b {
  ***
 }
style使用css的预处理器(less, sass, scss)的写法如下,建议使用第二种方式,/deep/在某些时候会报错,::v-deep更保险并且编译速度更快.
第一种/deep/

 /deep/ .a {
  ***
 }
第二种::v-deep
```
