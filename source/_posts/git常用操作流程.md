---
title: git常用操作流程
top_img: "/CDN/wallpaper/101.png"
cover: "/CDN/wallpaper/101.png"
tags:
  - Git
categories:
  - 开发工具
abbrlink: 29524
---

## 推送代码到远端

日常开发完一块功能，要把代码推送到远端保存时，一般是按如下步骤进行操作：

- `git status` 显示本地仓库的状态。如果此时显示远端没有更新的代码，接着执行以下步骤；
- `git add <filePath>` 把此次改动的文件加入本地缓存区；
- `git commit -m 'msg'` 把文件提交到本地仓库，并一同带上此次修改信息；
- `git push origin <branchName>` 把当前本地分支的代码推送到相应的远程分支上。

## 分支

- 1 创建并切换到新分支

在日常功能开发时，首先是从主分支检出新分支作为功能开发分支，此时的本地仓库当前分支需要为主分支，且保证本地仓库版本与远端一致。然后，基于主分支创建自己的新分支，使用如下命令：

```
git checkout -b <branchName>
```

执行完该命令，你会得到一个新的本地分支，并且当前分支已切换到新创建的分支上。这其实是两个步骤：创建分支，切换分支。你当然可以分开执行下面两行命令，达到同样的效果：

```
git branch <branchName>
git checkout <branchName>
```

- 2 显示分支

需要显示工程下有多少个分支时，使用命令：`git branch`。其中，带有 \* 的代表当前分支。加上 -v 参数，你可以在显示分支时，一并显示各个分支最新一次的提交：`git branch -v`。除此之外，你还可以使用命令 `git branch -vv` 来显示所有分支，同时带有每个分支对应的远程分支，及最新的一次提交。

- 3 推送分支到远端

当你开发完业务功能，需要推送到远端保存时，使用 push 命名：`git push origin <branchName>`

- 4 拉取分支

在上述 1 章节介绍执行 `git status` 后，如果显示本地仓库代码并非最新时，需要是先拉取远端代码，把本地代码保持最新，之后提交到远端才可能避免冲突。但是，如果本地分支没有与某个远程分支建立联系（例如新建工程第一次提交的时候），要先建立联系，而推送本地分支到远端时可以一并建立联系：

```
git push --set-upstream origin <branchName>
```

这样就会在远端也建立 branchName 分支，并与本地的 branchName 分支建立了联系。当需要拉取远程分支到本地的时候就可以使用命令：`git pull origin <branchName>`此外，`git fetch` 可以把远程的数据抓取到本地，但是不会修改本地的内容，需要自己手动 merge： `git merge`。而刚才说的 `git pull` 命令，其实正是合并了上面两条命令的功能，在抓取后一并修改。

- 5 分支的合并

开发完成，我们需要把功能分支合并会主分支。使用 merge 方式，进行开发分支合并到 master 主分支的场景时，我的操作步骤如下：
首先检出当前分支，例如是开发分支，然后我们要合并 master 分支进开发分支。

```
1 切换到本地 master 分支，使用 git pull 保证本地 master 是最新的
2 切换到本地的开发分支，使用合并的命令：git merge master，将 master 合并进开发分支
3 把本地分支 git push 推送到远端
4 再在本地切换到 master 分支，使用合并分支的代码：git merge <开发分支名>，把功能代码合并进主分支
5 使用 git push 把 master 分支推送到远程
```

## gitflow 工作流
- [gitflow管理分支](https://www.cnblogs.com/jeffery-zou/p/10280167.html)

## git常用命令
[Git常用命令及方法大全](https://blog.csdn.net/web_csdn_share/article/details/79243308)