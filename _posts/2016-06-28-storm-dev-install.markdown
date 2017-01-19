---
layout: post
title:  "Storm develop environment installation"
date:   2016-06-28 22:10:29 +0800
categories: bigdata
---

## Arch

一个完整的流式计算平台包含以下几个部分:

- 数据采集（日志采集）-> Flume
- 消息队列 -> Kafka
- 流式计算 -> Storm/Spark
- 数据输出

## Installation

- 下载storm源码

`
wget http://mirrors.cnnic.cn/apache/storm/apache-storm-0.9.6/apache-storm-0.9.6.tar.gz
`

- 配置环境变量

```
export $STORM_PATH

export $PATH 
```

- [可选]修改python bin，**storm 启动版本至少需要python2.7**

## Storm-Kafka Example

1. 配置Maven
2. 编写Spout
3. 编写Bolt
4. mvn package打包

### Problem

a. java.lang.ClassNotFoundException: kafka.api.OffsetRequest

原因:依赖的jar没有包含或是storm-kafka版本没有匹配

解决办法:

```xml
<dependencies>
<!-- storm-kafka的包 -->
<dependency>
<groupId>org.apache.storm</groupId>
<artifactId>storm-kafka</artifactId>
<version>${storm.version}</version>
</dependency>

<!-- kafka的jar,注意version里写的是kafka的版本号-->
<!-- kafka的版本号需要去kafak-path/lib下找到类似于
kafka_2.11-0.10.0.0.jar
2.11表示scala的版本
0.10.0.0表示kafka的版本
-->
<dependency>
<groupId>org.apache.kafka</groupId>
<artifactId>${kafka.version}</artifactId>
<version>0.10.0.0</version>
<exclusions>
<exclusion>
<groupId>org.apache.zookeeper</groupId>
<artifactId>zookeeper</artifactId>
</exclusion>
<exclusion>
<groupId>org.slf4j</groupId>
<artifactId>slf4j-log4j12</artifactId>
</exclusion>
<exclusion>
<groupId>log4j</groupId>
<artifactId>log4j</artifactId>
</exclusion>
</exclusions>
</dependency>
</dependencies>

<properties>
<!-- storm version -->
<storm.version>0.9.3</storm.version>
<!-- actually the scala version -->
<kafka.version>kafka_2.11</kafka.version>
</properties>
```

## Extra Component

### 1.Zookeeper

#### 依赖

1. Java(Best Java7)

#### 安装

- 下载zookeeper源码
- 配置conf/zoo.cfg
- 修改bin/zkServer.sh

添加

```
ZOO_LOG_DIR="$($GREP "^[[:space:]]*dataLogDir" "$ZOOCFG" | sed -e 's/.*=//')"
// 这样可以避免在哪启动zookeeper，就在哪写zookeeper.out,这个ZOO_LOG_DIR默认是当前路径
```

- 启动

`
sh bin/zkServer.sh start
`

- 测试

### 2.Kafka

#### Install

##### sbt update

##### sbt 

> sbt是scala的包管理器

#### Problem

- Exception in thread "main" java.lang.UnsupportedClassVersionError: kafka/Kafka : Unsupported major.minor version 51.0

Java版本太老

> 50.0 -> Java 6

> 51.0 -> Java 7

> 52.0 -> Java 8

#### Hello World

##### 1.启动Kafka Server

```
nohup ./bin/kafka-server-start.sh config/server.properties > \
~/netdisk/log/kafka/nohup.log 2>&1 &
```

##### 2.建立Topic

```
bin/kafka-topics.sh --create --zookeeper 10.95.41.51:2181 \
    --replication-factor 1 --partitions 1 --topic hellokafka

// 查看现有topic
bin/kafka-topics.sh --list --zookeeper 10.95.41.51:2181
```

##### 3.发送消息

```
bin/kafka-console-producer.sh --broker-list 10.95.41.51:9092 \
--topic hellokafka
Good After Noon Kafka
```

##### 4.启动Consumer，接收消息

```
bin/kafka-console-consumer.sh --zookeeper 10.95.41.51:2181 \
--topic hellokafka --from-beginning
```
