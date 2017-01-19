---
layout: post
title:  "[翻译]Diagnosing Network Issues with MTR"
date:   2016-07-26 00:33:06 +0800
categories: stuff
---

## Intro

MTR是一个系统管理员诊断、定位网络问题的强大工具，并且能够生成有用的网络状态(network status)报告给上游供应商（upstream providers）.它实际上是融合了`traceroute`和`ping`两款网络工具的软件.

## 网络诊断的背景

网络诊断工具主要包括：

- ping
- traceroute
- mtr

他们都是通过发送`ICMP`包来测试网络。当我们ping一台远程主机的时候，将会发送一系列的ICMP包，每个请求包都应收到相应的响应包。然后用户的客户端(也就是`ping`)就能够计算两点的回路时间(**Round Trip Time**)。与ping不同的是，traceroute或MTR发送递增TTL的包来判断网络间的跳数（**hops**）,当达到跳数之后会回源给客户端。

MTR不仅能够提供Internet中的传输路径上的基本信息，还能提供额外的一些无状态的信息包括：连接，中间件主机的响应性(responsiveness of the intermediate hosts)

## 安装MTR

Ubuntu : `apt-get install mtr-tiny`

CentOs : `yum install mtr`

## 生成MTR报告

命令: `mtr -rw [destination_host]`

参数说明

- r 表示--report
- w 表示hostname的长版本 --report-wide

## 初读MTR报告

下面是一份连接google.com的MTR报告:

```
$ mtr --report google.com
HOST: example                  Loss%   Snt   Last   Avg  Best  Wrst StDev
1. inner-cake                    0.0%    10    2.8   2.1   1.9   2.8   0.3
2. outer-cake                    0.0%    10    3.2   2.6   2.4   3.2   0.3
3. 68.85.118.13                  0.0%    10    9.8  12.2   8.7  18.2   3.0
4. po-20-ar01.absecon.nj.panjde  0.0%    10   10.2  10.4   8.9  14.2   1.6
5. be-30-crs01.audubon.nj.panjd  0.0%    10   10.8  12.2  10.1  16.6   1.7
6. pos-0-12-0-0-ar01.plainfield  0.0%    10   13.4  14.6  12.6  21.6   2.6
7. pos-0-6-0-0-cr01.newyork.ny.  0.0%    10   15.2  15.3  13.9  18.2   1.3
8. pos-0-4-0-0-pe01.111eighthav  0.0%    10   16.5  16.2  14.5  19.3   1.3
9. as15169-3.111eighthave.ny.ib  0.0%    10   16.0  17.1  14.2  27.7   3.9
10. 72.14.238.232                 0.0%    10   19.1  22.0  13.9  43.3  11.1
11. 209.85.241.148                0.0%    10   15.1  16.2  14.8  20.2   1.6
12. lga15s02-in-f104.1e100.net    0.0%    10   15.6  16.9  15.2  20.6   1.7
```

上面表示发送了10个包到google，总共包含12个Hop,并观察输出,如果没有--report参数，mtr将会持续发送icmp包.在大多数场合下，mtr默认的报告输出格式已经比较优雅，而且能够展示出有用的信息了.各个列的含义说明如下：

字段说明:

- Loss: 在每一个Hop上的丢包率
- Snt: 发送的包（Packet）的数目，在`-report`的参数下，mtr会默认发送10个包。若想改包数量，添加`--report-cycles=[number-of-packets]`参数
- Last, Avg, Best, and Wrst各种维度表示延迟
- StDev 表示每个主机延迟的标准差(standard deviation),能够表示出延迟的稳定程度

刚开始的Hop是靠近源的，也就是你的本地网络，如果这里异常，需要检查你的本地网络服务商ISP或者检查本地网络的配置是否正确.同理,路径上的最后几个Hop是终点附近的，如果有问题就是远程的主机有异常。

## 分析MTR报告

### 确定丢包率

核心的两个参数:

- loss 丢包率
- latency 延迟

造成丢包的原因有很多，可能是因为网络原因，也有可能服务提供商对MTR使用的ICMP包做了限制.

```
root@localhost:~# mtr --report www.google.com
HOST: example               Loss%   Snt   Last   Avg  Best  Wrst StDev
1. 63.247.74.43                  0.0%    10    0.3   0.6   0.3   1.2   0.3
2. 63.247.64.157                50.0%    10    0.4   1.0   0.4   6.1   1.8
3. 209.51.130.213                0.0%    10    0.8   2.7   0.8  19.0   5.7
4. aix.pr1.atl.google.com        0.0%    10    6.7   6.8   6.7   6.9   0.1
5. 72.14.233.56                  0.0%    10    7.2   8.3   7.1  16.4   2.9
6. 209.85.254.247                0.0%    10   39.1  39.4  39.1  39.7   0.2
7. 64.233.174.46                 0.0%    10   39.6  40.4  39.4  46.9   2.3
8. gw-in-f147.1e100.net          0.0%    10   39.6  40.5  39.5  46.7   2.2
```

从上面的MTR报告可以看出，只有**1->2**之间有丢包，而之后的Hop则没有了，这说明极有可能是63.237.64.157这个主机做了ICMP包的限制

### 理解网络延迟

由于物理设备的限制，网络延迟会随着跳数的增多而增长，然而这个增长速度应该是线性的。连接速度同样会影响延迟量。

```
root@localhost:~# mtr --report www.google.com
HOST: localhost                   Loss%   Snt   Last   Avg  Best  Wrst StDev
1. 63.247.74.43                  0.0%    10    0.3   0.6   0.3   1.2   0.3
2. 63.247.64.157                 0.0%    10    0.4   1.0   0.4   6.1   1.8
3. 209.51.130.213                0.0%    10    0.8   2.7   0.8  19.0   5.7
4. aix.pr1.atl.google.com        0.0%    10  388.0 360.4 342.1 396.7   0.2
5. 72.14.233.56                  0.0%    10  390.6 360.4 342.1 396.7   0.2
6. 209.85.254.247                0.0%    10  391.6 360.4 342.1 396.7   0.4
7. 64.233.174.46                 0.0%    10  391.8 360.4 342.1 396.7   2.1
8. gw-in-f147.1e100.net          0.0%    10  392.0 360.4 342.1 396.7   1.2
```

从上图的报告可以看出，hop3->4之间延迟非常大.

## 名词解释

### ICMP(Internet Control Message Protocol)

IP协议族的一员，主要用于网络设备间发送错误指示信息,一般不用于传输数据，常见部署在用户端网络程序中，诸如traceroute或ping等程序

### TTL(Time To Live)

此处的Time表示的是**次数**，而不是时间，表达的是一个包在结束之前还能经过的跳数

#### Hop

跳数: 网络中两个端路径上的节点，路由器的数目

## 参考文献

[原文](http://www.bitwizard.nl/mtr/)

[MTR官方网站](http://www.bitwizard.nl/mtr/)
