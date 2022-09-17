---
title: 安装配置git教程
top_img: '/CDN/post/4.jpg'
cover: '/CDN/post/4.jpg'
tags:
  - Git
categories:
  - 开发工具
abbrlink: 37056
---

## 第一步，下载安装git
打开 [git官网](https://git-scm.com/)，下载git对应操作系统的版本。

所有东西下载慢的话就可以去找镜像！官网下载太慢，我们可以使用淘宝镜像下载：[http://npm.taobao.org/mirrors/git-for-windows/下载对应的版本即可安装！](http://npm.taobao.org/mirrors/git-for-windows)

## 第二步，检查git是否安装成功
打开命令行窗口，在cmd中输入git或git--version。

## 第三步，配置User信息（默认已注册github账号）
当你安装Git后首先要做的事情是设置你的用户名称和e-mail地址。

这是非常重要的，因为每次Git提交都会使用该信息。它被永远的嵌入到了你的提交中：
```
# 配置用户名
git config --global user.name "username"    //（ "username"是自己的账户名，）
# 配置邮箱
git config --global user.email "username@email.com"     //("username@email.com"注册账号时用的邮箱)
```
以上命令执行结束后，可用 git config --global --list 命令查看配置是否OK。

## 第四步，设置本机绑定SSH公钥，实现免密码登录
继续刚才的操作，在命令框中输入以下命令:

`ssh-keygen -t rsa`

然后连敲三次回车键，结束后去系统盘目录下（一般在 C:Users你的用户名.ssh）(mac: /Users/用户/.ssh）查看是否有。ssh文件夹生成，此文件夹中以下两个文件：


将ssh文件夹中的公钥（ id_rsa.pub）添加到GitHub管理平台中，在GitHub的个人账户的设置中找到如下界面。title随便起一个，将公钥（ id_rsa.pub）文件中内容复制粘贴到key中，然后点击Ass SSH key就好啦。

## 第五步，测试配置是否成功
在Git Bush命令框（就是刚才配置账号和邮箱的命令框）中继续输入以下命令，回车：

`ssh -T git@github.com`