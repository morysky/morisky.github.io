---
layout: post
title:  "How to build a Blog With Github"
date:   2016-05-23 23:17:06 +0800
categories: stuff
---

## What
本文介绍如何使用Github创建属于自己的博客，并具有如下的特性：

- Free
- Markdown
- 静态，性能杠杠的
- 在页面上写/或本地(用Vim或者**Ulysses**随你)写，书写方便

## Background
目前开源的博客系统，非大PHP的Wordpress莫属了，但是，作为一个专注技术的**轻量**博客，真的需要Wordpress么？

今天我将介绍一个，基于Ruby on Rails的开源博客框架**Jekyll**搭建的博客系统，当然你不用了解Ruby on Rails，而且你也不用购买任何的云服务器资源，仅仅需要几步和简单的git技能，就能建设一个属于自己的清新舒爽的博客。

## Installation

### 1.安装Jekyll

#### 使用gem安装

> gem是一个ruby的类似包管理器的东西，同样有源(**Source**)的概念；默认的源在国内一般网络环境下是被墙的，建议替换成taobao的源

`sudo gem install jekyll`

##### 坑

- `Broken Pipe`，提示SSL网络交互失败,就是因为源不稳定,按照下面步骤替换成淘宝的

`gem sources -l`

`gem sources --remove https://rubygems.org/`

`gem sources -add https://ruby.taobao.org/`

`gem sources -l`

- 标题类型无法解析

> 需要在`#`后面**添加空格**，这样才能正常解析标题. PS: 其实markdown的标题本来就应该那样写

### 2.在Github上创建一个repository
**注意**：名字必须为**username**.github.io，比如我的`syaokun219.github.io`

使用`jekyll new myblog`新建一个**Jekyll App**，把所有文件放置在刚才新建的repository下，然后提交。

### 3.大功告成

访问https://username.github.io 去看效果吧！

具体Jekyll的使用就不详细说了，随便改改_config.yaml配置文件就行了，为了清爽嘛，就不要太酷炫了,享受**Jekyll+Markdown+Github**带来的书写体验吧!也可以在本地的目录下用**调试模式**看效果哟，输入如下命令

```
jelly serve
```

## Reference

[Github Page Doc](https://pages.github.com/)

[Jekyll](https://jekyllrb.com/docs/quickstart/)
