---
title: zookeeper单机模式安装
date: 2017-07-26 22:40:07
tags: zookeeper
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170805205013.png
keywords: zookeeper,zookeeper单机模式安装
description: zookeeper单机模式安装,ubuntu下直接用命令下载 wget http://apache.osuosl.org/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz...
---

## zookeeper单机模式安装

### 下载zookeeper

ubuntu下直接用命令下载

```
wget http://apache.osuosl.org/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz
```

![](http://osewdhh4j.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20170727093850.png)

### 解压

```
tar -zvxf zookeeper-3.4.10.tar.gz

cd zookeeper-3.4.10/

```

![](http://osewdhh4j.bkt.clouddn.com/20170727161000.png)

### 更改配置文件

进入到`conf`目录下复制zoo_sample.cfg文件改名为zoo.cfg

```
cd conf
cp zoo_sample.cfg zoo.cfg
```

### 启动zookeeper

```
./bin/zkServer.sh start
```
然后用jps查看会多出一个QuorumPeerMain

![](http://osewdhh4j.bkt.clouddn.com/20170727162525.png)

**连接到zookeeper**

```
./bin/zkCli.sh -server localhost:2181
```

![](http://osewdhh4j.bkt.clouddn.com/20170727162942.png)


```
help
```

![](http://osewdhh4j.bkt.clouddn.com/20170727162955.png)

### 关闭zookeeper


```
./bin/zkServer.sh stop
```

![](http://osewdhh4j.bkt.clouddn.com/20170727163234.png)








