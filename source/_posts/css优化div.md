---
title: CSS实现优化UI体验效果
top_img: '/CDN/wallpaper/cover_5.jpg'
cover: '/CDN/wallpaper/cover_5.jpg'
tags:
  - Css
categories:
  - 前端
abbrlink: 6962
---
## CSS实现中部div滚动
```css
.top {xxx}
//这里的外部content高度小于内部content2内容高度时触发滚动
.content1 {
  display: flex
  flex: 1
  height: 200px
  .content2 {
    flex: 1
    overflow：auto
  }
}
.footer {xxx}
```

### CSS实现中部滚动，底部操作栏随窗口移动
```html
<div class="main">
  <div class="tip"></div>
  <div class="test-container">
    <div class="test-top"></div>
    <div class="test-content">
      <div class="content"></div>
    </div>
    <div class="footer"></div>
  </div>
</div>
```
```css
<style lang="sass" scoped>
  .main {
    display: flex;
    flex-direction: column;
    height: 100%;
  }
  .tip{
    flex-shrink: 0;
  }
  .test-container {
    display: flex;
    flex-direction: column;
    padding: 16px;
    height: 100%;
    overflow: auto;
    min-width: 1200px;
    background: #FFFFFF;
    border-radius: 4px;
    .test-top {
      margin: 10px 0 16px;
    }
    .test-content {
      display: flex;
      flex-direction: column;
      flex-grow: 1;
      height: calc(100% - 150px);
      border-top: 1px solid #E0E0E0;
      .content {
        flex: 1;
        overflow: auto;
      }
    }
    .footer {
      height: 48px;
      line-height: 48px;
      text-align: center;
      border-top: 1px solid #E0E0E0;
    }
  }
</style>
```