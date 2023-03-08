---
title: git的常用工具推荐
top_img: '/CDN/wallpaper/033.webp'
cover: '/CDN/wallpaper/033.webp'
tags:
  - Git
categories:
  - 开发工具
abbrlink: 31626
---

## Tortoigit
### 安装与使用教程一大堆，这里只说一下将对比工具改成BeyondCompare

- [TortoiseGit 使用教程](https://www.cnblogs.com/anayigeren/p/10177027.html)
- 在设置中找到差异查看器->外部->选择下载好的比较工具文件

本人认为自带的比较不好用，所以使用外部的比较工具，按需引入
- [beyondcompare安装](https://www.beyondcompare.cc/)

## GitKraken

直接下载这个版本的GitKraken，顺便附上了破解方法
- [GitKraken安装文件](https://pan.baidu.com/s/1B-Fvg4xsTR0V63IIx1hhOg)提取码: 5p2p
- [破解方法](https://github.com/5cr1pt/GitCracken)
- [GitKraken快速上手使用教程](https://www.cnblogs.com/brifuture/category/1219605.html)


## git配置时注意
- git配置用户名邮箱和ssh后，需要再加入配置`git config --global  core.autocrlf=false`,避免因为符号格式导致代码在不同环境编译失败的问题

- 如果电脑上有两个环境需要在两种环境下都进行git基础配置以及同时在远程仓库提交两份生成的ssh公钥`ssh-keygen -t rsa`


