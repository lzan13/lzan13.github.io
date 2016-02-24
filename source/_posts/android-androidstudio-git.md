---
title: AndroidStudio结合Git同步代码到Github库提高开发效率
date: 2015-03-30 14:33:18
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Git
  - Github
id: 10003
---

之前都是用优盘拷贝代码，这里修改一次，拷贝一下，现在用`AndroidStudio`带有版本控制功能，可以直接使用`git`同步到`github`，所以学着弄下，这里记录下来

首先是在`github`上新建一个仓库，这是我的测试项目
![androidstudio-git5](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git5.png)

创建成功会跳转到下图页面，此页面中的：git remote add origin https://github.com/xxx/xxx.git 这句话在后边通过`AndroidStuido`提交代码到库需要用到，复制备用
![androidstudio-git5](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git14.png)

OK 网络上的仓库创建好了，那就要创建本地的项目了，打开`AndroidStudio`创建新项目：
新项目创建好之后默认显示的是`Android`的当前`Module`
注意：`AndroidStudio V1.0.1`版本的`Module`名字显示为`APP`不能改，不然会找不到`Module`!!!
这里把显示选择到整个项目，这是为了第一次提交到github做准备，把所有的项目文件都提交到github去
![androidstudio-git2](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git2.png)

接着设置下`git`以及`github`账户
![androidstudio-git13](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git13.png)
![androidstudio-git15](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git15.jpg)

OK 然后就是`init`当前项目
![androidstudio-git11](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git11.png)
![androidstudio-git6](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git6.png)

`init`项目之后有一个步骤是必须的，但是却无法在`AndroidStudio`中完成，因为要`remote`项目但是`AndroidStudio`没有提供这个菜单选项，所以要在`git Bash`命令行进行；
打开`git Bash`窗口，切换到当前项目根目录，然后输入`remote`命令回车
![androidstudio-git3](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git3.png)

然后就是添加选中的项目文件，第一次提交项目选中整个`project`，
当项目文件变绿色时，说明add成功
![androidstudio-git7](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git7.png)

接着就是需要`commit`项目文件了，
![androidstudio-git8](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git8.png)

![androidstudio-git9](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git9.png)

`commit`之后就是`pull`项目
OK 提交也提交过了，下面就是真正的把项目`Push`到`github`了
选择:`VCS->Git->Push`其实也可以在`commit`的同时选择`commit and push`
![androidstudio-git11](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git10.png)

弹出`push`确认对话框，选择提交到分支，后边第一次默认是`master`分支，可以自定义，
![androidstudio-git12](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git14.png)

点击`push`后会弹出输入`github`账户和密码，输入之后会提示创建本地密码仓库密码，可以取消，如果设置了密码，在以后提交时会出现下图的密码输入框
![androidstudio-git10](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git12.png)

OK 刷新下`github`的项目，可以看到整个项目已经被`push`到库中了
![androidstudio-git1](http://lzan13.qiniudn.com/blog/uploads/images/2015/04/androidstudio-git1.png)