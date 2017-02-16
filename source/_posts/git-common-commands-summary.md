---
title: Git常用命令总结
categories:
  - Develop
tags:
  - Branch
  - Clone
  - Git
  - Remote
  - SSH
date: 2016-11-11 12:12:12
id: 1478837532000
comments: true
---

简单总结下使用 `git` 进行版本控制的时候一些常用命令，以及一些常用语


创建SSH秘钥
-----
```Git
ssh-keygen -t rsa -C "lzan13@sina.com"
```
这个命令会在当前用户目录下生成一个`.ssh`目录，然后生成两个文件`.id_rsa`私钥和`.id_rsa.pub`公钥



本地操作相关
-------
主要是在本地仓库操作时使用的一些命令


### #git status
```Git
git status
```
查看当前仓库状态：是否有文件修改，需要添加等



### #git add 
```Git
git add <file>
git add .
```
将文件添加到暂存区，后边跟着文件名，可以使用`.`表示添加全部文件

### #git commit
```Git
git commit -m "first commit"
```
提交暂存区的所有代码到版本库，参数就是本次提交的说明信息



### #git log
```Git
git log // 查看提交日志
git log --pretty=oneline // 查询信息都在一行显示
git log --graph // 查看分之合并图
```
提交日志相关，列出从最近到之前的提交日志，列出信息包含有`SHA1`计算得到的`commit_id`



### #git reset
```Git
git reset --hard HEAD^
git reset --hard <commit_id>
```
这个命令的作用是将代码回退到之前提交的版本，在Git中，用`HEAD`表示当前版本，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，如果是多个可以写成`HEAD~ N`，N 代表是第几个，也可以和`git log`的结果关联起来使用



### #git relog
```Git
git relog
```
Git 工具操作命令历史日志，和`git log`稍有不同，`git relog`会记录所有 git 操作



### #git diff
```Git
git diff
git diff <file>
git diff [<commit_id>] -- <file> // 表示要查看当前文件与上个版本库中的区别
```
查看文件差异，不带有参数时会列出本次修改所有文件，带上参数时需要制定文件全路径



### #git checkout
```Git
git checkout -- <file>
```
撤销对文件的本次修改，回到之前的状态，准确点儿是回到上次调用`git add`或者`git commit`状态



### #git rm 
```Git
git rm <file>
```
从版本库删除一个文件，与本地的删除一个文件不同，这里是直接将版本库里的文件删除了，如果之前有将文件提交到版本库，可以使用`git checkout -- filename`恢复；



### #git branch
```Git
git branch // 查看分支 -v 本地全部分支，-a 包括远程全部分支
git checkout -b <branch> // 创建并切换分支，等同于下边两个命令
git branch <branch> // 创建分支
git checkout <branch> // 切换分支
git branch -d <branch> // 删除本地分支
```
分支相关操作命令



### #git merge
```Git
git merge <branch>
```
合并指定分支到当前所在分支，在使用`merge`命令时有可能会出现代码修改冲突的情况，特别是在多人协作的情况下，冲突用`<<<<<<<`和`>>>>>>>`包裹，中间用`=======`分割，`=======`以上是当前分支内容，以下是合并到当前分支的其他分支内容；





远程操作相关
---------
主要是和操作远程仓库相关的一些命令

### #git remote
```Git
git remote add <name> <url>
git remote remove <name>
git remote rename <old> <new>
```
管理当前仓库的远程地址，分别是：添加远程仓库地址、删除远程仓库、重命名远程仓库

### #git push
```Git
git push -u <remote> <branch>
git push <remote> <branch>
git push <remote> <branch>:<branch> // 将本地分支上传到远程的一个新分支
git push <remote :<branch> // 将本地空分支推送到远端，即删除远程分支
```
此命令是将本地仓库推送到远程仓库，第一次推送到远程分支需要加上`-u`参数，

### #git clone 
```Git
git clone <url> [<dir>]
git clone -b <branch> <url> [<dir>]
git checkout -b <branch> <remote>/<branch> // 根据远程仓库的分支创建并切换到新分支
```
克隆远程仓库到本地，可以使用`-b`参数指定克隆的分支，以及指定克隆后的文件夹名称，不设置就是默认远程仓库名字；

## 参考

参考网站 [廖雪峰 Git 教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

