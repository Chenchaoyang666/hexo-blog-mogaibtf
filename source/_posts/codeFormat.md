---
title: 前端vue项目代码格式化配置
top_img: '/CDN/wallpaper/cover_10.jpg'
cover: '/CDN/wallpaper/cover_10.jpg'
tags:
  - Vue
categories:
  - 前端
abbrlink: 8236
---
## eslint+prettier配置
安装依赖，如果是eslint-plugin-prettier需升级对应eslint依赖才可使用
```code
npm install --save-dev eslint-plugin-prettier eslint-config-prettier
npm install --save-dev prettier eslint
```

package.json
```json
"scripts": {
 "format": "prettier --write \"src/**/*.js\" \"src/**/*.vue\"",
}
```
.eslintrc.js
```js
module.exports = {
  extends: [
    'plugin:vue/strongly-recommended',
    'plugin:prettier/recommended'//新增
  ]
  rules: {
    "prettier/prettier": "error"//新增
  }
}
```
.prettierrc
```js
module.exports = {
“printWidth”: 80, // 每行代码长度（默认80）
“tabWidth”: 2, // 每个tab相当于多少个空格（默认2）
“useTabs”: false, // 是否使用tab进行缩进（默认false）
“singleQuote”: true, // 使用单引号（默认false）
“semi”: true, // 声明结尾使用分号(默认true)
“trailingComma”: “none”, // 在对象或数组最后一个元素后面是否加逗号（默认none）
“bracketSpacing”: true, // 对象字面量的大括号间使用空格（默认true）
“jsxBracketSameLine”: false, // 多行JSX中的>放置在最后一行的结尾，而不是另起一行（默认false）
“arrowParens”: “avoid”, // 只有一个参数的箭头函数的参数是否带圆括号（默认avoid）
‘endOfLine’: ‘auto’ //避免window和linux换行符不同导致报delete (cr)的错
}
```

## husky配置
6.0.0版本前后配置有很大区别可参考下面链接
1.[官网的新配置方式](https://www.npmjs.com/package/husky/v/7.0.4)
2.[https://blog.csdn.net/DoneSpeak/article/details/118469233](https://blog.csdn.net/DoneSpeak/article/details/118469233)

## lint-stage

```json
"lint-staged": {
  "src/**/*.{js,json,css,vue}": [
   "prettier --write", // 先执行prettier，再执行eslint，保证代码质量
   "eslint --fix",
   "git add"
  ]
},
```
## 工作空间setting.json(编辑器版本不一样可能会有差异，自行调整配置)
```code
"editor.formatOnSave":true;
"":""
```