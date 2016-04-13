---
title: Git命令行将原作者的代码pull到本地并合并
comments: true
categories:
  - Develop
tags:
  - Android
id: 10054
date: 2016-02-22 11:40:11
---

今天在提交自己修改的代码到原作者的项目时遇到了一些问题，原作者最近更新比较频繁，导致我提交的代码有了冲突，所以要自己先把原作者的进行`pull`下来，然后解决冲突之后才能提交，既然要解决冲突，首先肯定是要把原作者的代码给`pull`下来，这里记录下更新提交的过程
首先要给当前的版本库添加原作者的`remote`，因为我是`fork`并`clone`自己的仓库进行修改，当时是没有添加的原作者的`remote`的
```bat
git remote add easemob https://github.com/easemob/easeui.git
```
然后运行命令去`pull`下来原作者的代码
```bat
git pull easemob dev
```
此命令`pull`下来的原作者代码会自动合并到当前分支，合并的过程会打印一些详细的信息，比如是否有冲突，然后要做的就是去代码里找到这些冲突的地方，把冲突给解决掉；当我们`pull`完了之后，如果有冲突，在命令行的分支那里会提示`MERGING`，修改了之后我们要`commit`下才行，操作之后就正常了，然后我们就可以愉快的`push`自己的代码，并向原作者提交`pull request`了