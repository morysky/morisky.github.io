---
layout: post
title:  "Git reference[todo]"
date:   2016-05-25 23:50:06 +0800
categories: stuff
---

# Git introduction

## 何谓Git

一款具有以下特性的版本控制软件:

- 免费(貌似svn也不收费)
- 开源
- **分布式**(集中式的有CVS,SVN)

### 那么什么又是版本控制软件呢?

又称之为代码管理(Source control management == Version control)，通常以Revision（版次）来标识每一次改动，这种版本的管理控制在团队多人同时修改同一个文件时显得尤为重要

## 特性

### 分支和合并

Git具有一个与其他SCM软件完全不同的分支模型,这使得你可以进行如下的操作:

1. 平滑的上下文切换
2. 基于角色的代码行
3. 基于特性的工作流模式
4. 可任意处理的实验功能

可能你用其他SCM软件也能实现类似的功能，但那跟Git相比会显得复杂，而且容易出错。

### 轻巧、快速

省去了与中心服务器交互的代价，Git是非常快的.

### 数据安全

Git保证你的每个bit的提交，都完全准确无误.Git的每次提交都对应一个提交ID，这不仅保证了当前的提交是一致的，还能保证历史提交没有修改

# Git Tip

## Alias 别名功能

### git config

```
git config --global alias.st status
```

### 修改~/.gitconfig

```
[alias]
    st = status
    ci = commit -v
```
