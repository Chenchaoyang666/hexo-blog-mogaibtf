---
title: 自定义树节点样式记录
top_img: '/CDN/wallpaper/cover_5.jpg'
cover: '/CDN/wallpaper/cover_5.jpg'
tags:
  - Css
categories:
  - 前端
abbrlink: 1789
---
## Css自定义树节点显示
需求：节点左侧显示title，文本宽度超出省略，右侧根据鼠标移动显示相关操作图标和颜色变化。
```css
.node-label {
  flex: 1;
  width: 100px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
.node-icon {
  margin-right: 8px;
  display: none;
  .first-icon {
    margin-right: 5px;
    &:hover {
      color: #409eff;
    }
  }
  .second-icon {
    &:hover {
      color: #ff5050;
    }
  }
}
&:hover .node-icon {
  display: inline-block;
}
```