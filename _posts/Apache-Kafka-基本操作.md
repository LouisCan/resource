---
title: Apache Kafka - 基本操作
date: 2017-07-13 19:55:31
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/kafka%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C.png
keywords: Apache Kafka - 基本操作,Apache Kafka,kafka操作,kafka
description: 首先让我们开始实现单节点单个代理配置，然后我们将我们的设置迁移到单节点多代理配置。希望你现在可以在你的机器上安装Java，ZooKeeper和Kafka。在移动到Kafka群集设置之前，首先需要启动ZooKeeper，因为Kafka Cluster使用ZooKeeper。
---

## Apache Kafka教程  之 Apache Kafka - 基本操作

![http://blogxinxiucan.sh1.newtouch.com/](http://osewdhh4j.bkt.clouddn.com/kafka%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C.png)

### Apache Kafka - 基本操作

> 首先让我们开始实现单节点单个代理配置，然后我们将我们的设置迁移到单节点多代理配置。希望你现在可以在你的机器上安装Java，ZooKeeper和Kafka。在移动到Kafka群集设置之前，首先需要启动ZooKeeper，因为Kafka Cluster使用ZooKeeper。

### 启动ZooKeeper
打开一个新终端并键入以下命令 -

    bin/zookeeper-server-start.sh config/zookeeper.properties

要启动Kafka Broker，请键入以下命令 -

    bin/kafka-server-start.sh config/server.properties

启动`Kafka Broker`后，在`ZooKeeper`终端上键入命令`jps`，您将看到以下响应 -

    821 QuorumPeerMain
    928 Kafka
    931 Jps

现在你可以看到在`QuorumPeerMain`是`ZooKeeper`守护进程的终端上运行两个守护进程，另一个是Kafka守护进程。

### 单节点单代理配置
在此配置中，您有一个ZooKeeper和代理ID实例。以下是配置它的步骤 -

**创建Kafka主题** - Kafka提供了名为kafka-topics.sh的命令行实用工具，用于在服务器上创建主题。打开新终端并键入以下示例。

**句法**

    bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 
    --partitions 1 --topic topic-name

例

    bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1   
    --partitions 1 --topic Hello-Kafka

我们刚刚创建了一个名为Hello-Kafka的主题，具有单个分区和一个副本因素。以上创建的输出将类似于以下输出 -

**输出** - 创建主题Hello-Kafka

创建主题后，您可以在Kafka代理终端窗口中获​​取通知，并在`config / server.properties`文件中的`“/ tmp / kafka-logs /”`中指定的创建主题的日志。

### 主题列表
要获取Kafka服务器中的主题列表，可以使用以下命令 -

**句法**

    bin/kafka-topics.sh --list --zookeeper localhost:2181

**output**

    Hello-Kafka

由于我们创建了一个主题，它将仅列出Hello-Kafka。假设，如果创建多个主题，您将在输出中获取主题名称。

**启动生产者发送消息**
句法

    bin/kafka-console-producer.sh --broker-list localhost:9092 --topic topic-name

从上述语法，生产者命令行客户端需要两个主要参数 -

**Brokers列表** - 我们要发送消息的Brokers列表。在这种情况下，我们只有一个Brokers。Config / server.properties文件包含代理端口ID，因为我们知道我们的代理正在侦听端口9092，因此您可以直接指定它。

**主题名称** - 以下是主题名称的示例。

例

    bin/kafka-console-producer.sh --broker-list localhost:9092 --topic Hello-Kafka

生产者将等待stdin的输入并发布到Kafka集群。默认情况下，每条新行都将作为新消息发布，然后在`config / producer.properties`文件中指定默认的生产者属性。现在，您可以在终端中输入几行消息，如下所示。

**output**

    $ bin/kafka-console-producer.sh --broker-list localhost:9092 
    --topic Hello-Kafka[2016-01-16 13:50:45,931] 
    WARN property topic is not valid (kafka.utils.Verifia-bleProperties)
    Hello
    My first message
    My second message

**开始消费者接收消息**
与生产者类似，默认消费者属性在`config / consumer.proper-ties`文件中指定。打开一个新终端，并键入以下消息消息语法。

**句法**

    bin/kafka-console-consumer.sh --zookeeper localhost:2181 —topic topic-name 
    --from-beginning

例

    bin/kafka-console-consumer.sh --zookeeper localhost:2181 —topic Hello-Kafka 
    --from-beginning

**output**

    Hello
    My first message
    My second message

最后，您可以从生产者的终端输入消息，并看到它们出现在消费者终端中。到目前为止，您对具有单个代理的单节点群集有非常好的了解。现在让我们来看看多个Brokers的配置。

### 单个节点多个代理配置
在转到多个代理群集设置之前，首先启动您的ZooKeeper服务器。

**创建多个KafkaBrokers** - 我们在con-fig / server.properties中已经有一个Kafka代理实例。现在我们需要多个代理实例，因此将现有的server.prop-erties文件复制到两个新的配置文件中，并将其重命名为server-one.properties和server-two.prop-erties。然后编辑两个新文件并分配以下更改 -

**配置/ server-one.properties**

    # The id of the broker. This must be set to a unique integer for each broker.
    broker.id=1
    # The port the socket server listens on
    port=9093
    # A comma seperated list of directories under which to store log files
    log.dirs=/tmp/kafka-logs-1

**配置/ server-two.properties**

    # The id of the broker. This must be set to a unique integer for each broker.
    broker.id=2
    # The port the socket server listens on
    port=9094
    # A comma seperated list of directories under which to store log files
    log.dirs=/tmp/kafka-logs-2

**启动多个Brokers** - 在三个服务器上进行了所有更改后，再打开三个新终端，逐个启动每个代理。

    Broker1
    bin/kafka-server-start.sh config/server.properties
    Broker2
    bin/kafka-server-start.sh config/server-one.properties
    Broker3
    bin/kafka-server-start.sh config/server-two.properties

现在我们有三个不同的Brokers在机器上运行。尝试通过在ZooKeeper终端上键入jps来检查所有守护程序，然后您将看到响应。

**创建主题**
让我们为这个主题分配三个复制因子值，因为我们有三个不同的Brokers运行。如果您有两个Brokers，则分配的副本值将为两个。

**句法**

    bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 
    -partitions 1 --topic topic-name

例

    bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 
    -partitions 1 --topic Multibrokerapplication

**output**

    created topic “Multibrokerapplication”

将描述命令用于检查哪个代理对当前创建的主题听如下所示-

    bin/kafka-topics.sh --describe --zookeeper localhost:2181 
    --topic Multibrokerappli-cation

output

    bin/kafka-topics.sh --describe --zookeeper localhost:2181 
    --topic Multibrokerappli-cation
    
    Topic:Multibrokerapplication    PartitionCount:1 
    ReplicationFactor:3 Configs:
       
    Topic:Multibrokerapplication Partition:0 Leader:0 
    Replicas:0,2,1 Isr:0,2,1

从上面的输出，我们可以得出结论，第一行给出了所有分区的摘要，显示了我们已经选择的主题名称，分区计数和复制因子。在第二行中，每个节点将成为随机选择的分区部分的引导者。

在我们的例子中，我们看到我们的第一个Brokers（有broker.id 0）是领导者。然后副本：0,2,1意味着所有的券商复制的话题终于ISR是一组在同步副本。那么这是目前还没有出现并且被领导者赶上的副本的子集。

**启动生产者发送消息**
此过程与单个代理程序设置保持相同。

例

    bin/kafka-console-producer.sh --broker-list localhost:9092 
    --topic Multibrokerapplication

**output**

    bin/kafka-console-producer.sh --broker-list localhost:9092 --topic Multibrokerapplication
    [2016-01-20 19:27:21,045] WARN Property topic is not valid (kafka.utils.Verifia-bleProperties)
    This is single node-multi broker demo
    This is the second message

### 开始消费者接收消息
此过程与单个代理设置中所示的相同。

例

    bin/kafka-console-consumer.sh --zookeeper localhost:2181 
    —topic Multibrokerapplica-tion --from-beginning

**output**

    bin/kafka-console-consumer.sh --zookeeper localhost:2181 
    —topic Multibrokerapplica-tion —from-beginning
    This is single node-multi broker demo
    This is the second message

基本主题操作
在本章中，我们将讨论各种基本主题操作。

**修改主题**
您已经了解如何在Kafka群集中创建主题。现在让我们使用以下命令修改创建的主题

句法

    bin/kafka-topics.sh —zookeeper localhost:2181 --alter --topic topic_name 
    --parti-tions count

例

    We have already created a topic “Hello-Kafka” with single partition count and one replica factor. 
    Now using “alter” command we have changed the partition count.
    bin/kafka-topics.sh --zookeeper localhost:2181 
    --alter --topic Hello-kafka --parti-tions 2

**output**

    WARNING: If partitions are increased for a topic that has a key, 
    the partition logic or ordering of the messages will be affected
    Adding partitions succeeded!

### 删除主题
要删除主题，可以使用以下语法。

**句法**

    bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic topic_name

例

    bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic Hello-kafka

**output**

    > Topic Hello-kafka marked for deletion

`注 -如果delete.topic.enable未设置为true，则不会有任何影响`




