---
title: Git命令行将原作者的代码pull到本地并合并
date: 2016-02-22 11:40:11
categories:
  - Develop
  - Git
tags:
  - Branch
  - Github
  - Merge
  - Pull
id: 10054
---
今天在提交自己修改的代码到原作者的项目时遇到了一些问题，原作者最近更新比较频繁，导致我提交的代码有了冲突，所以要自己先把原作者的进行`pull`下来，然后解决冲突之后才能提交，既然要解决冲突，首先肯定是要把原作者的代码给`pull`下来，这里记录下更新提交的过程
首先要给当前的版本库添加原作者的路径，因为我`fork`并`clone`的时候是没有添加的
```bat
git remote add easemob https://github.com/easemob/easeui.git
```
`Pull`下原作者代码之后一般会自动合并，合并的过程会打印一些详细的信息，比如是否有冲突，然后要做的就是去代码里找到这些冲突的地方，把冲突给解决掉