---
title: 调用小米接口修改步数
top_img: '/CDN/wallpaper/cover_9.jpg'
cover: '/CDN/wallpaper/cover_9.jpg'
tags:
  - Python
categories:
  - 后端
abbrlink: 52583
---
## 修改步数前置条件
- 1.应用商店下载**小米运动App**，手机号注册登录

> 注意一定要用手机号，别直接第三方登录

- 2.点击**我的->第三方接入**，绑定你想同步数据的项目

> 注意同步微信步数需关注公众号：华米科技

- 3.打开网页或者本地运行的脚本，输入**小米运动账号**、密码和修改的步数

> 账号是小米运动的账号，不是小米账号！！！

## 修改步数网页版
这个使用别人的服务器，修改有时间段限制
[小米运动步数网页版](https://4og.cn/xm/)

## 修改步数源码(需搭建Pyhton环境)
github项目地址：[步数修改源码](https://github.com/Chenchaoyang666/updateStep)
gitee项目地址：[步数修改源码](https://gitee.com/chenchaoyang666/updateStep)
本地环境运行时要下载包和修改账号密码
```python
#pip install requests,time,re,json,random
import requests,time,re,json,random

# 修改为小米运动账号
user = "写你的手机号"
# 修改为小米运动登录密码
passwd = "写你的密码"
# 要修改的步数，直接输入想要修改的步数值，留空为随机步数
step ="0"
```