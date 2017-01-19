---
layout: post
title:  "Maven brief introduction"
date:   2016-07-10 23:17:06 +0800
categories: stuff
---

## Intro

Maven是一个Java世界里用于构建工程(Build Project)的工具，就好比是C世界的Make

Maven与Make对比:

----| Maven | Make
----|------|----
依赖文件 | pom.xml  | Makefile
Command | mvn | make

> POM(Project Obeject Model)是Maven工程的基础单元组件，是一个XML格式的配置信息文件，其中一些字段(Value)不必声明就有默认的参数，比如:

- target 编译路径
- src/main/java 代码路径

POM在Maven 1中称作**project.xml**

## 安装jar到本地仓库

```
mvn install:install-file -Dfile=common-util-1.0.jar -DgroupId=com.baidu.bigdata -DartifactId=common-util -Dversion=1.0 -Dpackaging=jar
```

## Problem

### 1.mavan官方镜像慢

修改

`
~/.m2/settings.xml
`

中的mirrors标签，添加你想添加的镜像地址

> 要一个snapshot和普通的

- snapshot : 频繁更新（只要有新的就更新）
- 普通：仅更新到rep一次

### 2. 引入第三方lib，但并未打包到最终jar中

## Reference
[官方手册](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)
