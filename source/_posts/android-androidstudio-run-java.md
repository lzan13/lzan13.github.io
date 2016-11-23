---
title: AndroidStudio 开发测试运行 Java 类的 Main 方法
comments: true
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Java
  - Library
  - Module
id: 10073
date: 2016-11-23 18:57:25
---


自从`Google`发布了`AndroidStudio1.5`之后，现在经常使用`AndroidStudio`开发程序了，在习惯了他的强大之后已经深深的把`Eclipse`给抛弃了，现在就算有`Eclipse`的项目也直接导入到`AndroidStudio`里去查看；不过在有时候发现`Eclipse`比`AndroidStudio`还是有一点儿有时的，就是在有时候突然有个小想法，或者想测试小段计算的代码，这个时候也还必须连上手机去运行程序才行，这让我一直很苦恼；

不过后来发现，原来可以通过另外一种方式实现运行`java`类的`main`方法，不过在刚开始的测试过程中一直有问题，最近更新了下`SDK`突然想起来试下，发现成功了，这里记录下，给大家一个参考；

-----------------------

### 项目准备
首先如果想实现在`AndroidStudio`上运行`java`类，必须有个正常的项目，他是不能直接使用的，有了正常的`AndroidStudio`项目之后我们就可以在项目上新建一个`Module`；
步骤：

1. 创建 Module
>`右击项目`->`New`->`Module`
2. 然后在弹出界面选择
>`Java Library`
3. 然后填写Libarary的信息就行了
>- Library Name 
就填写当前 Library 的名字
>- Java package name 
当前创建的 Library 包名
>- Java class 
当前 Library 第一个类的名字

 
### 编码测试
基础我们都已经做好了，下边就可以开始进行编码测试了，这个编码都要在刚才创建的`Library`下进行，这样就可以像在`Eclipse`里进行一样了

创建完成之后就可以看到项目目录下有一个刚才创建的`Module`，我们的`java`代码都是在这个`library`下编写测试;

首先我们要写一个`main`方法，并在里边输出一句话
```java
/**
 * 我们java library 的额第一个类
 */
public class MLMain {
    /**
     * java类的 main方法，可以直接 run
     * @param args
     */
    public static void main(String[] args) {
        System.out.print("hello java library!");
    }
}
```

然后我们可以直接右击当前类，点击`Run'MLMain.main()'`

可以看到控制台输出了我们打印的那句话

### 结束语
OK了，以后我们就可以愉快的在 AndroidStudio 中测试 java 代码了！

希望这篇文章能对大家有所帮助！O(∩_∩)O~
