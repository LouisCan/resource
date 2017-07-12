---
title: Kafka 介绍
date: 2017-07-10 19:53:04
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170710191038.png
keywords: 辛修灿,Kafka,kafka介绍,xinxiucan,blogxinxiucan
description: Apache Kafka是一个分布式发布 -订阅消息系统和一个强大的队列，可以处理大量的数据，并使您能够将消息从一个端点传递到另一个端点。Kafka适合离线和在线消息消费。Kafka消息保留在磁盘上，并在群集内复制以防止数据丢失。
---


## Kafka 介绍

> Apache Kafka是一个分布式发布 -订阅消息系统和一个强大的队列，可以处理大量的数据，并使您能够将消息从一个端点传递到另一个端点。Kafka适合离线和在线消息消费。Kafka消息保留在磁盘上，并在群集内复制以防止数据丢失。

 **Kafka构建在ZooKeeper同步服务之上。 它与Apache Storm和Spark非常好地集成，用于实时流式数据分析。Kafka将消息以topic为单位进行归纳。将向Kafka topic发布消息的程序成为producers.将预订topics并消费消息的程序成为consumer.Kafka以集群的方式运行，可以由一个或多个服务组成，每个服务叫做一个broker.producers通过网络将消息发送到Kafka集群，集群向消费者提供消息。**

### 点对点消息系统

在点对点系统中，消息被保留在队列中。 一个或多个消费者可以消耗队列中的消息，但是特定消息只能由最多一个消费者消费。 一旦消费者读取队列中的消息，它就从该队列中消失。 该系统的典型示例是订单处理系统，其中每个订单将由一个订单处理器处理，但多个订单处理器也可以同时工作。 

### 发布 - 订阅消息系统

在发布 - 订阅系统中，消息被保留在主题中。 与点对点系统不同，消费者可以订阅一个或多个主题并使用该主题中的所有消息。 在发布 - 订阅系统中，消息生产者称为发布者，消息使用者称为订阅者。 一个现实生活的例子是Dish电视，它发布不同的渠道，如运动，电影，音乐等，任何人都可以订阅自己的频道集，并获得他们订阅的频道时可用。

> **kafka 特点:**
> 
> -  **可靠性** - Kafka是分布式，分区，复制和容错的。
> -  **可扩展性** - Kafka消息传递系统轻松缩放，无需停机。
> - **耐用性** - Kafka使用分布式提交日志，这意味着消息会尽可能快地保留在磁盘上，因此它是持久的。
> - **性能** - Kafka对于发布和订阅消息都具有高吞吐量。 即使存储了许多TB的消息，它也保持稳定的性能。


![http://osewdhh4j.bkt.clouddn.com/20170710193358.png](http://osewdhh4j.bkt.clouddn.com/20170710193358.png)

### kafak组件

| 组件 | 详细 |
|-------- | -----|
| `Topics` | Topics（主题）属于特定类别的消息流称为主题。 数据存储在主题中。主题被拆分成分区。|
| `Partition` | Partition（分区）主题可能有许多分区，因此它可以处理任意数量的数据。|
| `Partition offset` | Partition offset（分区偏移）每个分区消息具有称为 offset 的唯一序列标识。| 
| `Replicas of partition` | Replicas of partition（分区备份）副本只是一个分区的备份。 副本从不读取或写入数据。 它们用于防止数据丢失。| 
| `Brokers`  | Brokers ：broker就是kafka中的server| 
|  `Kafka Cluster` | Kafka Cluster（Kafka集群）Kafka有多个Brokers被称为Kafka集群。 可以扩展Kafka集群，无需停机。 这些集群用于管理消息数据的持久性和复制。| 
| `Producers` | Producers（生产者）生产者是发送给一个或多个Kafka主题的消息的发布者。 生产者向Kafka Brokers发送数据。 Producer将消息发布到它指定的topic中,并负责决定发布到哪个分区。| 
| `Consumers` | Consumers（消费者）Consumers从Brokers处读取数据。 消费者订阅一个或多个主题，并通过从代理中提取数据来使用已发布的消息。| 
| `Leader` | Leader（主节点）Leader 是负责给定分区的所有读取和写入的节点。 每个分区都有一个服务器充当Leader| 
| `Follower` | Follower（从节点） 如果Leader（主节点）出现问题失败，一个Follower（从节点）将自动成为新的Leader（主节点）。 | 


### Kafka有序性

相比传统的消息系统，Kafka可以很好的保证`有序性`。
传统的队列在服务器上保存有序的消息，如果多个`consumers`同时从这个服务器消费消息，服务器就会以消息存储的顺序向consumer分发消息。虽然服务器按顺序发布消息，但是消息是被异步的分发到各consumer上，所以当消息到达时可能已经失去了原来的顺序，这意味着并发消费将导致顺序错乱。为了避免故障，这样的消息系统通常使用“专用consumer”的概念，其实就是只允许一个消费者消费消息，当然这就意味着失去了并发性。

在这方面Kafka做的更好，通过分区的概念，Kafka可以在多个consumer组并发的情况下提供较好的有序性和负载均衡。将每个分区分只分发给一个consumer组，这样一个分区就只被这个组的一个consumer消费，就可以顺序的消费这个分区的消息。因为有多个分区，依然可以在多个consumer组之间进行负载均衡。注意consumer组的数量不能多于分区的数量，也就是有多少分区就允许多少并发消费。

**Kafka只能保证一个分区之内消息的有序性**，在不同的分区之间是不可以的，这已经可以满足大部分应用的需求。如果需要topic中所有消息的有序性，那就只能让这个topic只有一个分区，当然也就只有一个consumer组消费它。







