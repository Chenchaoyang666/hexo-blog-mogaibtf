---
title: Vue 特性集合
top_img: '/CDN/wallpaper/cover_1.jpg'
cover: '/CDN/wallpaper/cover_1.jpg'
tags:
  - JavaScrip
  - Vue
categories:
  - 前端
abbrlink: 56620
---
## Vue父子组件传值
多级组件嵌套需要传递数据时，通常使用的方法是通过vuex。如果仅仅是传递数据，而不做中间处理，使用 vuex 处理，未免有点杀鸡用牛刀。Vue 2.4 版本提供了另一种方法，使用 v-bind=”$attrs”, 将父组件中不被认为 props特性绑定的属性传入子组件中，通常配合 interitAttrs 选项一起使用。之所以要提到这两个属性，是因为两者的出现使得组件之间跨组件的通信在不依赖 vuex 和事件总线的情况下变得简洁，业务清晰。
[](https://img.jbzj.com/file_images/article/201801/201818103314216.png?201808103342)

- v-bind="$props"
可以将父组件的所有props下发给它的子组件,子组件需要在其props:{} 中定义要接受的props。
vm.$props: 当前组件接收到的 props 对象。Vue 实例代理了对其 props 对象属性的访问。

- v-bind="$attrs"
将调用组件时的组件标签上绑定的非props的特性(class和style除外)向下传递。在子组件中应当添加inheritAttrs: false(避免父作用域的不被认作props的特性绑定应用在子组件的根元素上)。

- v-on="$listeners"
将父组件标签上的自定义事件向下传递其子组件可以直接通过emit(eventName)的方式调用。

### 示例代码
A组件：
```html
<template>
 <div id="app">
 <child1
 :p-child1="child1"
 :p-child2="child2"
 v-on:test1="onTest1" //此处监听了两个事件，可以在B组件或者C组件中直接触发
 v-on:test2="onTest2"> 
 </child1>
 </div>
</template>
```
```js
<script>
 import Child1 from './Child1.vue';
 export default {
 data () {
 return {};
 },
 components: { Child1 },
 methods: {
 onTest1 () {
 console.log('test1 running...');
 },
 onTest2 () {
 console.log('test2 running');
 }
 }
 };
</script>
```
B组件
```html
<template>
 <div class="child-1">
 <p>in child1:</p>
 <p>props: {{pChild1}}</p>
 <p>$attrs: {{$attrs}}</p>
 <hr>
 <!-- C组件中能直接触发test的原因在于 B组件调用C组件时 使用 v-on 绑定了$listeners 属性 -->
 <!-- 通过v-bind 绑定$attrs属性，C组件可以直接获取到A组件中传递下来的props（除了B组件中props声明的） -->
 <child2 v-bind="$attrs" v-on="$listeners"></child2>
 </div>
</template>
```
```js
<script>
 import Child2 from './Child2.vue';
 export default {
 props: ['pChild1'],
 data () {
 return {};
 },
 inheritAttrs: false,
 components: { Child2 },
 mounted () {
 this.$emit('test1');
 }
 };
</script>
//结果
// in child1:
// props: v_child1
// $attrs: { “p-child2”: “v_child2”}
```

C组件
```html
<template>
 <div class="child-2">
 <p>in child2:</p>
 <p>props: {{pChild2}}</p>
 <p>$attrs: {{$attrs}}</p>
 <hr>
 </div>
</template>
```
```js
<script>
 export default {
 props: ['pChild2'],
 data () {
 return {};
 },
 inheritAttrs: false,
 mounted () {
 this.$emit('test2');
 }
 };
</script>
//结果
// in child2:
// props: v_child2
// $attrs: {}
```

## Vue render
- [点我了解render用法](https://www.jianshu.com/p/0c9516a3be80)

## Vue 代码比对插件
- [vue-code-diff](https://codechina.csdn.net/mirrors/ddchef/vue-code-diff)

## Vue异步更新Dom和$nextTick原理
理论上，我们不应主动去操作DOM，因为Vue的核心思想就是数据驱动DOM，但在很多业务里，我们避免不了会操做一些DOM,这时我们就有可能用到$nextTick。

### $nextTick
- [Vue异步更新Dom](https://www.cnblogs.com/pleaseAnswer/p/13566987.html)
- $nextTick 的使用场景

虽然 Vue 是数据驱动的，但是有时候我们不得不去操作 DOM 去处理一些特殊的场景，而 Vue 更新 DOM 是异步执行的，所以我们不得不去使用 $nextTick 去异步获取 DOM。
```html
<template>
  <div>
    <span ref="msg">{{ msg }}</span>
  </div>
</template>
```
```js
<script>
export default {
  data() {
    return {
      msg: 'hello nextTick'
    }
  },
  methods: {
    changeMsg() {
      this.msg = 'hello world'
      console.log(this.$refs.msg.innerHTML, '同步获取')
      this.$nextTick(() => {
        console.log(this.$refs.msg.innerHTML, '异步获取')
      })
    }
  },
  mounted() {
    this.changeMsg()
  }
}
</script>
```
得到的结果分别是：
```code
hello nextTick 同步获取
hello world 异步获取
```

## Vue @click属性
- [理解vue @click.native~.prevent~.stop~.self~.once](https://blog.csdn.net/L_Sarah/article/details/109805756)