---
title: 基于wangEditor实现格式刷
top_img: '/CDN/wallpaper/cover_11.jpg'
cover: '/CDN/wallpaper/cover_11.jpg'
tags:
  - Vue
  - Js
  - 富文本
categories:
  - 前端
abbrlink: 19904
---
## 主要实现思路
前提：vue项目已经引进wangEditor`npm install wangeditor @^4.7.7`,并搭建好demo
```vue
<template>
  <div id="editor-container"></div>
</template>
<script>
  // 引入 wangEditor
  import wangEditor from "wangeditor";
  
  export default {
    name:"wangeditor",
    props: {
      value: {
        type: String,
        default: ''
      }
    },

    data() {
      return {
        editor: null,
      };
    },

    watch: {
      value (val) {
        if(val !== editor.txt.html()){
          this.editor.txt.html(val)
        }
      }
    },
  
    mounted() {
      this.setEditor()
    },
  
    methods: {  
      setEditor () {
        const editor = new wangEditor(`#editor-container`); // 配置 onchange 回调函数，将数据同步到 vue 中
        editor.config.uploadImgShowBase64 = true
        editor.config.zIndex = 1
        editor.config.height = 400
        editor.config.excludeMenus = [
          'emoticon',
          'video'
        ]
        editor.config.onchange = (newHtml) => {
          this.$emit('input', newHtml)
        }; // 创建编辑器
        editor.create();
        editor.txt.html(this.value);
        this.editor = editor;
      }
    }
  }
</script>
```
### 鼠标选择内容后保存选区
使用mouseup事件和saveRange保存选区

### 获取选区样式
- getSelectionContainerElem获取选中节点
- getTargetDom获取目标节点
- getAllStyle获取所有样式

### 改变格式刷状态
- getIsBrush获取格式刷状态
- setIsBrush设置格式刷状态

### 刷新样式

- getSelectionRangeTopNodes.length判断选取行数
- offset和setStart/setEnd重设选取范围
- getSelectionText和addStyle替换样式

## brushFormat.js
```js
import E from ‘wangeditor’;
const { BtnMenu } = E;
let isBrushOn = false;

export class FormatBrush extends BtnMenu {
constructor(editor) {
// data-title属性表示当鼠标悬停在该按钮上时提示该按钮的功能简述
const $elem = E.$(
// <div class="w-e-menu" data-title="格式刷" style="font-size:16px"> <i class="icon-geshishua"></i> </div>
);
super($elem, editor);
this.editor = editor;
}
// 菜单点击事件
clickHandler() {
// 做任何你想做的事情
// 可参考【常用 API】文档，来操作编辑器
if (!isBrushOn) {
if (this.editor.selection.isSelectionEmpty()) return;
let domToParse =
this.editor.selection.getSelectionContainerElem().elems[0];
this.editor.copyStyleList = parseDom(domToParse);
console.log(this.editor.selection.getRange());
this.active();
}
isBrushOn = !isBrushOn;
}
// 菜单是否被激活（如果不需要，这个函数可以空着）
// 1. 激活是什么？光标放在一段加粗、下划线的文本时，菜单栏里的 B 和 U 被激活，如下图
// 2. 什么时候执行这个函数？每次编辑器区域的选区变化（如鼠标操作、键盘操作等），都会触发各个菜单的 tryChangeActive 函数，重新计算菜单的激活状态
tryChangeActive() {
// 激活菜单
// 1. 菜单 DOM 节点会增加一个 .w-e-active 的 css class
// 2. this.isActive === true
// this.active()
// 取消激活菜单
// 1. 菜单 DOM 节点会删掉 .w-e-active
// 2. this.isActive === false
// this.unActive()
if (isBrushOn) {
this.active();
} else {
this.unActive();
}
}
}
//格式刷状态获取、修改
export function getIsBrush() {
return isBrushOn;
}
export function setIsBrush(val) {
isBrushOn = val;
}

//粘贴样式
export function pasteStyle(editor) {
if (editor.selection.isSelectionEmpty()) return;
//根据wang中选区顶级节点数判断选区行数
if (editor.selection.getSelectionRangeTopNodes().length <= 1) {
const text = editor.selection.getSelectionText();
let targetDom = addStyle(text, editor.copyStyleList);
editor.cmd.do(‘insertHTML’, targetDom.outerHTML);
} else {
let elements = [];
let range = editor.selection.getRange();
let $startElem = editor.selection.getSelectionStartElem();
//这里调用wangeditor中的dom方法，也可使用原生DOM寻找父节点方法
let $startElemCon = $startElem.parentUntil(‘p’);
let $endElem = editor.selection.getSelectionEndElem();
let $endElemCon = $endElem.parentUntil(‘p’);
elements.push({
type: ‘start’,
elem: $startElem,
offset: range.startOffset,
container: $startElemCon
});
while (!$startElemCon.next().equal($endElemCon)) {
$startElemCon = $startElemCon.next();
elements.push({
type: ‘mid’,
elem: $startElemCon,
container: $startElemCon
});
}
elements.push({
type: ‘end’,
elem: $endElem,
offset: range.endOffset,
containerType: range.startContainer.nodeType === 1 ? ‘NODE’ : ‘TEXT’,
container: $endElemCon
});
elements.forEach(element => {
const $container = element.container;
editor.selection.createRangeByElem($container, null, true);
let range = editor.selection.getRange();
if (element.type === ‘start’) {
//.firstChild这里用于定义标签中的文本，否则会报错There is no child at offset 4.
range.setStart(element.elem.elems[0].firstChild, element.offset);
} else if (element.type === ‘end’) {
range.setEnd(element.elem.elems[0].firstChild, element.offset);
}
const text = editor.selection.getSelectionText();
let targetDom = addStyle(text, editor.copyStyleList);
editor.cmd.do(‘insertHTML’, targetDom.outerHTML);
});
}
}

//获取目标节点及样式
function parseDom(dom) {
let targetDom = null;
let nodeArray = [];

getTargetDom(dom);

getAllStyle(targetDom);

function getTargetDom(dom) {
for (let i of dom.childNodes) {
if (i.nodeType === 3 && i.nodeValue && i.nodeValue.trim() !== ‘’) {
targetDom = dom;
return;
}
}
getTargetDom(dom.children[0]);
}

function getAllStyle(dom) {
if (!dom) return;
const tagName = dom.tagName.toLowerCase();
nodeArray.push({
tagName: tagName,
attributes: Array.from(dom.attributes).map(i => {
return {
name: i.name,
value: i.value
};
})
});
if ([‘p’, ‘h1’, ‘h2’, ‘h3’, ‘h4’, ‘h5’].includes(tagName)) return;
getAllStyle(dom.parentNode);
}
return nodeArray;
}

//根据保存节点信息添加样式
function addStyle(text, nodeArray) {
let currentNode = null;
nodeArray.forEach((ele, index) => {
let node = document.createElement(ele.tagName);
for (const attr of ele.attributes) {
node.setAttribute(attr.name, attr.value);
}
if (index === 0) {
node.innerText = text;
currentNode = node;
} else {
node.appendChild(currentNode);
currentNode = node;
}
});
return currentNode;
}
```

## demo页导入formatBrush.js
- [扩展菜单参考](https://www.wangeditor.com/doc/pages/11-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%89%A9%E5%B1%95%E8%8F%9C%E5%8D%95/01-%E5%BF%AB%E9%80%9F%E6%89%A9%E5%B1%95%E4%B8%80%E4%B8%AA%E8%8F%9C%E5%8D%95.html)