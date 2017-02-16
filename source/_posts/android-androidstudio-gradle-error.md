---
title: AndroidStudio使用Gradle编译项目Failed to complete Gradle execution错误解决
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Gradle
date: 2015-04-28 14:09:48
id: 1430201388000
comments: true
---

在使用`AndroidStudio`构建项目时有时会提示错误`Failed to complete Gradle execution`比较蛋疼的是这个问题还不是经常的，是偶尔，不确定因素，因为不知道什么时候就出现了，运行着好好的就突然这样，重启也不行，让人抓狂！
结局办法就是修改醒目的配置文件：
下边就是项目工程配置文件`gradle.properties`，默认是全部注释掉的，
```java
# Project-wide Gradle settings.
# IDE (e.g. Android Studio) users:
# Gradle settings configured through the IDE *will override*
# any settings specified in this file.

# For more details on how to configure your build environment visit
# http://www.gradle.org/docs/current/userguide/build_environment.html

# Specifies the JVM arguments used for the daemon process.
# The setting is particularly useful for tweaking memory settings.
# Default value: -Xmx10248m -XX:MaxPermSize=256m
# org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
org.gradle.jvmargs=-Xmx512m -XX:MaxPermSize=512m

# When configured, Gradle will run in incubating parallel mode.
# This option should only be used with decoupled projects. More details, visit
# http://www.gradle.org/docs/current/userguide/multi_project_builds.html#sec:decoupled_projects
# org.gradle.parallel=true
```
加上这句话就会好了`org.gradle.jvmargs=-Xmx512m -XX:MaxPermSize=512m`
这句话不是在`studio`的配置文件中设置的，而是在项目的配置文件`gradle.properties`中