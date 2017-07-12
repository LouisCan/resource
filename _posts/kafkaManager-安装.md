---
title: kafkaManager 安装
date: 2017-07-11 22:57:50
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170712154016.png
keywords: kafka,kafka安装,kafka安装部署,中间件,kafka Manager,kafkaManager
description: kafka Manager 用于管理Apache Kafka的工具,code地址-https://github.com/yahoo/kafka-manager
---

## Kafka Manager 安装

**Kafka Manager是用于管理Apache Kafka的工具**。

`code` 地址：https://github.com/yahoo/kafka-manager

> 它支持以下内容：

> * 管理多个群集
> * 容易检查集群状态（主题，消费者，偏移量，经纪人，副本分配，分区分配）
> * 运行首选副本选举
> * 使用选项生成分区分配，以选择要使用的代理
> * 运行分区重新分配（基于生成的分配）
> * 创建可选主题配置的主题（0.8.1.1具有不同于0.8.2+的配置）
> * 删除主题（仅支持0.8.2+，并记住在代理配​​置中设置delete.topic.enable = true）
> * 主题列表现在表示标记为删除的主题（仅支持0.8.2+）
> * 批量生成多个主题的分区分配，并选择要使用的代理
> * 批量运行多个主题的分区重新分配
> * 将分区添加到现有主题
> * 更新现有主题的配置
> * （可选）启用JMX轮询代理级和主题级度量。
> * 可选地筛选出在zookeeper中没有ids / owner /＆offset /目录的消费者。

![](http://osewdhh4j.bkt.clouddn.com/20170707162733.png)

### kafka-manager-1.3.0.4.zip下载
链接: [https://pan.baidu.com/s/1gfOQWHt](https://pan.baidu.com/s/1gfOQWHt)  密码: b8x4

### 开始服务

解压缩生成的zip文件，并将工作目录更改为可以运行的服务，如下所示：
```
$ bin/kafka-manager
```
默认情况下，它将选择端口`9000`.这是可以覆盖的，配置文件的位置也是如此。例如：
```
$ bin/kafka-manager -Dconfig.file=/path/to/application.conf -Dhttp.port=8080
```
再次，如果java不在您的路径中，或者您需要针对不同版本的Java运行，请按如下所示添加-`java-home`选项：
```
$ bin/kafka-manager -java-home /usr/local/oracle-java-8
```

![](http://osewdhh4j.bkt.clouddn.com/20170707162805.png)

![](http://osewdhh4j.bkt.clouddn.com/20170707163447.png)

![](http://osewdhh4j.bkt.clouddn.com/20170710171541.png)

![](http://osewdhh4j.bkt.clouddn.com/20170710171610.png)

![](http://osewdhh4j.bkt.clouddn.com/20170710171653.png)



