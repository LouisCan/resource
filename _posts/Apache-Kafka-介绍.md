---
title: Apache Kafka - 介绍
date: 2017-07-12 19:10:12
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/kafka-%E4%BB%8B%E7%BB%8D1.png
keywords: kafka,kafka教程,kafka介绍,Apache Kafka,Apache Kafka - 介绍,消息中间件,发布订阅消息系统
description: Apache Kafka起源于LinkedIn，后来成为2011年的开源Apache项目，然后在2012年成为Apache的一流项目。Kafka以Scala和Java编写>。Apache Kafka是基于发布订阅的容错消息系统。它是快速，可扩展和分布的设计。
---


## Apache Kafka教程 之 Apache Kafka - 介绍

### Apache Kafka - 介绍
> Apache Kafka起源于LinkedIn，后来成为2011年的开源Apache项目，然后在2012年成为Apache的一流项目。Kafka以Scala和Java编写。Apache Kafka是基于发布订阅的容错消息系统。它是快速，可扩展和分布的设计。

本教程将探讨Kafka的原理，安装，操作，然后将介绍Kafka集群的部署。最后，我们将总结实时应用和与`Big Data Technologies`的集成。

在进行本教程之前，您必须对`ava`，`Scala`，`分布式消息系统`和`Linux环境`有很好的了解。

在大数据中，使用了大量的数据。关于数据，我们有两个主要挑战。第一个挑战是如何收集大量数据，第二个挑战是分析收集的数据。为了克服这些挑战，您需要一个消息系统。

**Kafka专为分布式高吞吐量系统而设计。Kafka作为一个更传统的邮件经纪人的替代品往往运作良好。与其他消息系统相比，Kafka具有更好的吞吐量，内置的分区，复制和固有的容错能力，使其非常适合大规模的消息处理应用。**

### 什么是邮件系统？
消息系统负责将数据从一个应用程序传输到另一个应用程序，因此应用程序可以专注于数据，但不用担心如何共享数据。分布式消息传递基于可靠消息队列的概念。消息在客户端应用程序和消息系统之间异步排队。两种类型的消息传递模式是可用的 - 一种是`点对点`，另一种是`发布订阅（pub-sub）`消息系统。**大多数消息传递模式跟随pub-sub**。

### 点到点信息系统
在点对点系统中，消息将保留在队列中。一个或多个消费者可以使用队列中的消息，但是特定消息可以由最多仅一个消费者消费。一旦消费者读取队列中的消息，它将从该队列中消失。该系统的典型示例是订单处理系统，其中每个订单将由一个订单处理器处理，但多订单处理器可以同时工作。下图描绘了结构。

![enter image description here](http://osewdhh4j.bkt.clouddn.com/point_to_point_messaging_system.jpg)

### 发布订阅消息系统
在发布订阅系统中，邮件将保留在主题中。与点对点系统不同，消费者可以订阅一个或多个主题并消费该主题中的所有消息。在Publish-Subscribe系统中，消息生成器被称为发布者，消息消费者被称为订户。一个现实的例子是Dish TV，它发布不同的频道，如运动，电影，音乐等，任何人都可以订阅自己的频道，并获得他们的订阅频道。

![enter image description here](http://osewdhh4j.bkt.clouddn.com/publish_subscribe_messaging_system.jpg)

### 什么是Kafka？
Apache Kafka是分布式发布订阅消息传递系统和强大的队列，可以处理大量数据，并使您能够将消息从一个端点传递到另一个终端。Kafka适用于离线和在线消息消费。Kafka消息被保留在磁盘上，并在集群内复制以防止数据丢失。Kafka建立在ZooKeeper同步服务之上。它与Apache Storm和Spark完美结合，实时流式传输数据分析。

> 优点 以下是Kafka的几个好处 -
> -  **可靠性** - Kafka是分布式，分区式，复制型和容错型。
> - **可扩展性** - Kafka消息系统轻松扩展，无需停机时间。
> -  **耐用性** - Kafka使用分布式提交日志，这意味着邮件尽可能快地依然存在于磁盘上，因此它是耐用的。
> - **性能** - Kafka对于发布和订阅消息都具有高吞吐量。它保持稳定的性能，即使存储了许多TB的消息。

**Kafka非常快，保证零停机和零数据丢失。**

### 用例
Kafka可用于许多用例。其中有些列在下面 -

 - 指标 - Kafka经常用于运行监控数据。这涉及从分布式应用程序聚合统计信息，以产生操作数据的集中式提要。
 - 日志聚合解决方案 - Kafka可以在整个组织中使用，从多个服务收集日志，并以标准格式提供给多个服务器。
 - 流处理 - 流行框架（如Storm和Spark
   Streaming）从主题读取数据，处理它，并将处理后的数据写入可用于用户和应用程序的新主题。Kafka的强大耐用性在流处理方面也非常有用。

#### Kafka需要
Kafka是处理所有实时数据源的统一平台。Kafka支持低延迟消息传递，并在存在机器故障的情况下保证容错。它具有处理大量不同消费者的能力。Kafka非常快，执行200万次写/秒。Kafka将所有数据保留到磁盘，这实质上意味着所有的写入都将转到操作系统（RAM）的页面缓存。这将数据从页面缓存传输到网络套接字非常有效。



