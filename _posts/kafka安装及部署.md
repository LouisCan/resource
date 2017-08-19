---
title: kafka安装及部署
date: 2017-07-10 20:45:40
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170805205013.png
keywords: kafka,kafka安装,kafka安装部署,中间件
description: kafka安装及部署,下载Kafka并解压 kafka_2.11-0.10.1.1.tgz. 百度网盘下载地址- 链接 ![](https://pan.baidu.com/s/1hrJOIPI) 密码  67i9,下载后解压,tar -zxvf kafka_2.11-0.10.1.1.tgz
---

## kafka安装及部署


####  下载Kafka并解压

**kafka_2.11-0.10.1.1.tgz**
链接: https://pan.baidu.com/s/1hrJOIPI  密码: 67i9

**下载后解压：**

```
tar -zxvf kafka_2.11-0.10.1.1.tgz 
```

### 单机模式启动

```
cd kafka_2.11-0.10.1.1
```
先启动`zookeeper`
```
./bin/zookeeper-server-start.sh config/zookeeper.properties 
```
![](http://osewdhh4j.bkt.clouddn.com/20170707162208.png)

再启动`kafka`
```
./bin/kafka-server-start.sh  config/server.properties 
```

![](http://osewdhh4j.bkt.clouddn.com/20170707162456.png)
![](http://osewdhh4j.bkt.clouddn.com/20170707162511.png)













