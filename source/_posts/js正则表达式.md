---
title: RegExp基础语法
top_img: "/CDN/wallpaper/1.jpg"
cover: "/CDN/wallpaper/1.jpg"
tags:
  - JavaScrip
  - RegExp
categories:
  - 前端
abbrlink: 59605
---

## 匹配模式

创建正则表达式对象时，可以设置'm'、'i'、'g'这三个标志，分别对应多行模式、不区分大小模式和全局模式三种

全局模式 g:
默认地，第一次匹配成功后，正则对象就停止向下匹配了。g 修饰符表示全局匹配(global)，设置'g'标志后，正则对象将匹配全部符合条件的结果，主要用于搜索和替换

```
console.log('1a,2a,3a'.replace(/a/,'b'));//'1b,2a,3a'
console.log('1a,2a,3a'.replace(/a/g,'b'));//'1b,2b,3b'
```

{% note info flat %}
特点：javascript 中的正则表达式最大的特点是不支持空白，必须写在一行中
{% endnote %}

```js
//匹配ab
console.log(/ab/.test("ab")); //true
console.log(/ ab/.test("ab")); //false
console.log(/a b/.test("ab")); //false
console.log(/ab /.test("ab")); //false
```

## 元字符

```js
元字符         名称              匹配对象
.             点号               单个任意字符(除回车\r、换行\n、行分隔符\u2028和段分隔符\u2029外)
[]            字符组             列出的单个任意字符
[^]           排除型字符组        未列出的单个任意字符
?             问号               匹配0次或1次
*             星号               匹配0交或多次
+             加号               匹配1次或多次
{min,max}     区间量词            匹配至少min次，最多max次
^             脱字符             行的起始位置
$             美元符             行的结束位置
|             竖线               分隔两边的任意一个表达式
()            括号               限制多选结构的范围，标注量词作用的元素，为反向引用捕获文本
\1,\2...      反向引用            匹配之前的第一、第二...组括号内的表达式匹配的文本
```

## 转义字符

- 【1】因为元字符有特殊的含义，所以无法直接匹配。如果要匹配它们本身，则需要在它们前面加上反斜杠(\)

{% note info flat %}
右方括号]和右花括号}不需要转义
{% endnote %}

- 【2】'\'加非元字符，表示一些不能打印的特殊字符

```js
\0        NUL字符\u0000
[\b]      匹配退格符\u0008，不要与\b混淆
\t        制表符\u0009
\n        换行符\u000A
\v        垂直制表符\u000B
\f        换页符\u000C
\r        回车符\u000D
\xnn      由十六进制数nn指定的拉丁字符
\uxxxx    由十六进制数xxxx指定的Unicode字符(\u4e00-\u9fa5代表中文)
\cX       控制字符^X，表示ctrl-[X]，其中的X是A-Z之中任一个英文字母，用来匹配控制字符
```

{% note info flat %}
'\'加任意其他字符，默认情况就是匹配此字符，也就是说，反斜线(\)被忽略了
{% endnote %}

## 字符组

- 范围

正则表达式通过连字符(-)提供了范围表示法，可以简化字符组

```js
/[0123456789]/
//等价于
/[0-9]/
/[abcdefghijklmnopqrstuvwxyz]/
//等价于
/[a-z]/
```

- 排除

字符组的另一个类型是排除型字符组，在左方括号后紧跟一个脱字符'^'表示，表示在当前位置匹配一个没有列出的字符，所以[^0-9]表示 0-9 以外的字符

{% note info flat %}
在字符组内部，脱字符'^'表示排除，而在字符组外部，脱字符'^'表示一个行锚点
{% endnote %}
{% note info flat %}
^符号是元字符，在字符组中只要^符号不挨着左方括号就可以表示其本身含义，不转义也可以
{% endnote %}

```js
//匹配abc和^符号
console.log(/[a-c^]/.test("^")); //true
console.log(/[a-c\^]/.test("^")); //true
console.log(/[\^a-c]/.test("^")); //true
```

- 简记

```js
\d     数字，等同于[0-9]
\D     非数字，等同于[^0-9]
\s     空白字符，等同于[\f\n\r\t\u000B\u0020\u00A0\u2028\u2029]
\S     非空白字符，等同于[^\f\n\r\t\u000B\u0020\u00A0\u2028\u2029]
\w     字母、数字、下划线，等同于[0-9A-Za-z_](汉字不属于\w)
\W     非字母、数字、下划线，等同于[^0-9A-Za-z_]
```

## 量词

```js
// 默认情况下，量词都是贪婪模式(greedy quantifier)，即匹配到下一个字符不满足匹配规则为止
{n}       匹配n次
{n,m}     匹配至少n次，最多m次
{n,}      匹配至少n次
?         相当于{0,1}
*         相当于{0,}
+         相当于{1,}
```

懒惰模式(lazy quantifier)和贪婪模式相对应，在量词后加问号'?'表示，表示尽可能少的匹配，一旦条件满足就再不往下匹配

```js
{n}?       匹配n次
{n,m}?     匹配至少n次，最多m次
{n,}?      匹配至少n次
??         相当于{0,1}
*?         相当于{0,}
+?         相当于{1,}
```

## 括号

括号有两个功能，分别是分组和引用。具体而言，用于限定量词或选择项的作用范围，也可以用于捕获文本并进行引用或反向引用

- 分组

```js
//希望字符串'ab'重复出现2次，应该写为(ab){2}
console.log(/(ab){2}/.test("abab")); //true
console.log(/(ab){2}/.test("abb")); //false
console.log(/ab{2}/.test("abab")); //false
console.log(/ab{2}/.test("abb")); //true

//身份证长度有15位和18位两种
/\d{15}(\d{3})?/;
```

- 捕获

```js
//RegExp.$1\RegExp.$2\RegExp.$3……到RegExp.$9分别用于存储第一、第二……第九个匹配的捕获组。在调用exec()或test()方法时，这些属性会被自动填充
console.log(/(\d{4})-(\d{2})-(\d{2})/.test("2016-06-23")); //true
console.log(RegExp.$1); //'2016'
console.log(RegExp.$2); //'06'
console.log(RegExp.$3); //'23'
console.log(RegExp.$4); //''

//在replace()方法中也可以引用分组，形式是$num，num是对应分组的编号
console.log("2000-01-01".replace(/(\d{4})-(\d{2})-(\d{2})/g, "$3-$2-$1")); //'01-01-2000'
```

- 反向引用
  反向引用允许在正则表达式内部引用之前捕获分组匹配的文本，形式是\num，num 表示所引用分组的编号

```js
//开始标签
<([^>]+)>
//标签内容
[\s\S]*?
//匹配成对的标签
/<([^>]+)>[\s\S]*?<\/\1>/

console.log(/<([^>]+)>[\s\S]*?<\/\1>/.test('<a>123</a>'));//true
console.log(/<([^>]+)>[\s\S]*?<\/\1>/.test('<a>123</b>'));//false
```

## 选择

```js
//开头
(0|\+86)?
//前3位
13\d|14[579]|15[0-35-9]|17[0135-8]|18\d
//后8位
\d{8}

//手机号码
var phone = /(0|\+86)?(13\d|14[579]|15[0-35-9]|17[0135-8]|18\d)\d{8}/;
console.log(phone.test('13453250661'));//true
console.log(phone.test('1913250661'));//false
console.log(phone.test('1345325061'));//false
```

## 断言

在正则表达式中，有些结构并不真正匹配文本，而只负责判断在某个位置左/右侧是否符合要求，这种结构被称为断言(assertion)，也称为锚点(anchor)，常见的断言有 3 种：单词边界、行开头结尾、环视

- 单词边界
  正则表达式提供了专用的单词边界(word boundary)，记为\b，它匹配的是'单词边界'位置，而不是字符。

```js
console.log(/\ban\b/.test("an apple")); //true
console.log(/\ban\b/.test("a an")); //true
console.log(/\ban\b/.test("an")); //true
console.log(/\ban\b/.test("and")); //false
console.log(/\ban\b/.test("ban")); //false
```

- 起始结束
  常见的断言还有^和$，它们分别匹配字符串的开始位置和结束位置，所以可以用来判断整个字符串能否由表达式匹配

- 环视
  javascript 只支持正序环视，相当于只支持向前看，不支持往回看;而正序环视又分为肯定正序环视和否定正序环视
  肯定正序环视的记法是(?=n)，表示前面必须是 n 才匹配；否定正序环视的记忆法是(?!n)，表示前面必须不是 n 才匹配

```js
console.log(/a(?=b)/.exec("abc")); //['a']
console.log(/a(?=b)/.exec("ac")); //null
console.log(/a(?!b)/.exec("abc")); //null
console.log(/a(?!b)/.exec("ac")); //['a']

console.log(/a(?=b)b/.exec("abc")); //['ab']
```

## 优先级

```js
//从上到下，优先级逐渐降低
\                            转义符
() (?!) (?=) []              括号、字符组、环视
* + ? {n} {n,} {n,m}         量词
^ $                          起始结束位置
|                            选择
```
