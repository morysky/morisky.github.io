---
layout: post
title:  "How to build a basic php extension[todo]"
date:   2016-06-01 22:17:06 +0800
categories: php 
---

## Build a Basic PHP Extension

### 背景

### 为什么需要PHP扩展

#### 加载方式

- 静态编译:./configure完整编译PHP
- 动态加载:phpize编译成so文件，用dl()函数加载

### 第一个扩展

#### 编辑配置文件
- config.m4

## Q&A

### 扩展无法加载
1. 如果采用php.ini加载的方式，请确认**extension dir**的路径是否正确
