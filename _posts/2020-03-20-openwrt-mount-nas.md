---
layout: post
title:  "OpenWrt Mount Remote NAS"
date:   2020-03-20 02:42:17:06 +0800
categories: macos
---

# Intro
最近入手了斐讯N1盒子，经过小半天的折腾，中间经历个人脑残过程，终于搞好了。该软路由除了完成**特殊功能**外，剩下有个小小的需求就是离线下载。但在典型的家庭网络下，显然**存储**与软路由是两套东西的，即NAS和软路由。

# CIFS

没错，其实方案就是`Samba`，NAS上Samba服务端一般都是支持的，因此思路就是把Samba的目录挂载到软路由(OpenWrt)上即可.

```
1. opkg install --nodeps --force-depends kmod-fs-cifs cifsmount
2. mount.cifs -v -o username=admin,password=shits //192.168.0.183/Admin_Home1 /mnt/nas
```

**注意**
1. `cat /etc/banner`
```
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
\     ____                 _       __     __   /
\    / __ \____  ___  ____| |     / /____/ /_  /
\   / / / / __ \/ _ \/ __ \ | /| / / ___/ __/  /
\  / /_/ / /_/ /  __/ / / / |/ |/ / /  / /_    /
\  \____/ .___/\___/_/ /_/|__/|__/_/   \__/    /
\      /_/  W I R E L E S S   F R E E D O M    /
\                                              /
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 Base on OpenWrt R20.3.11 by lean & lienol
 Kernel 5.4.25-amlogic-flippy-30+
 Packaged by flippy on 2020-03-14
```
即内核为5.4.25，若不加入`--nodeps`，会报Linux内核不匹配的错，大概意思是说镜像仓库中的package版本比当前低，加入此参数可强制安装

2. 上面提到的`Admin_Home1`要注意，跟你的NAS Samba路径有关，因为不能挂载root，所以你要指定一个默认路径。我的NAS（海康威视闲小盘）比较蛋疼，Samba的目录是`Admin_Home1`，但是AFS的却是`disk_admin`我以为一致，引发了问题.


# 参考文献
- 软路由
- 斐讯N1
- OpenWrt/LEDE