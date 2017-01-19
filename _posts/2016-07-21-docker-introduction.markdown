---
layout: post
title:  "Docker introduction"
date:   2016-07-21 10:01:06 +0800
categories: docker
---

## 什么是Docker

## Docker特性

---

## Docker客户端

### 基本指令

```
# Usage:  [sudo] docker [subcommand] [flags] [arguments] ..
# Example:
$ docker run -i -t ubuntu /bin/bash
```

---

## 跟着例子学

### 1. 简单的程序

### 2. 运行一个基于Python的Web APP

运行: `docker run -d -P training/webapp python app.py`

- -d(daemonize)表示后台运行
- -P 表示端口映射(host -> container),这个例子会将host的任意可用端口映射到container的5000上

```
// 运行的结果用docker ps查看如下
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
01452b386a23        training/webapp     "python app.py"     8 seconds ago       Up 8 seconds        0.0.0.0:32768->5000/tcp   mad_cori
```

这时候访问[http://localhost:32768](http://localhost:32768)就能够看到输出helloworld了

> 本来写了好多东西，但是搞Jekyll新主题的时候手贱删除了本地的整个Repo，后来才想起来上班时间码的那么多字都白写了！Shit！
