---
title: Apache Kafka - 与Storm集成
date: 2017-07-14 19:47:58
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/kafka%E9%9B%86%E6%88%90storm.png
keywords: Apache Kafka,与Storm集成,Apache Kafka - 与Storm集成,kafka,Storm
description: Storm最初是由Nathan Marz和BackType创建的。在短时间内，Apache Storm成为分布式实时处理系统的标准，可让您处理大量数据。Storm非常快，每个节点每秒处理超过一百万个元组的基准测试。Apache Storm持续运行，从配置的源（Spouts）中消耗数据，并将数据传递到处理管道（Bolts）。组合，spout和Bolts 做拓扑。
---

## Apache Kafka教程  之 与Storm集成

![http://blogxinxiucan.sh1.newtouch.com/](http://osewdhh4j.bkt.clouddn.com/kafka%E9%9B%86%E6%88%90storm.png)

### Apache Kafka - 与Storm集成

### 关于Storm
> Storm最初是由Nathan Marz和BackType创建的。在短时间内，Apache Storm成为分布式实时处理系统的标准，可让您处理大量数据。Storm非常快，每个节点每秒处理超过一百万个元组的基准测试。Apache Storm持续运行，从配置的源（Spouts）中消耗数据，并将数据传递到处理管道（Bolts）。组合，spout和Bolts 做拓扑。

####  与Storm融合
Kafka和Storm自然互补，他们强大的合作使得实时流式分析能够快速移动大数据。Kafka和Storm集成是让开发人员更容易地从Storm拓扑中吸收和发布数据流。

**概念流**
spout是流的来源。例如，一个spout可以从Kafka主题中读取元组，并将其作为流发出。Bolts 消耗输入流，处理并可能发射新的流。Bolts 可以从运行功能，过滤元组，进行流聚合，流式连接，与数据库交谈等操作。Storm拓扑中的每个节点并行执行。拓扑运行无限期，直到您终止它。Storm将自动重新分配任何失败的任务。此外，Storm保证不会丢失数据，即使机器掉线，信息丢失。

让我们详细介绍一下Kafka-Storm集成API。将卡夫卡与Storm整合有三个主要课程。他们如下 -

**BrokerHosts - ZkHosts＆StaticHosts**
BrokerHosts是一个接口，ZkHosts和StaticHosts是它的两个主要实现。ZkHosts用于通过维护ZooKeeper中的细节来动态跟踪Kafka经纪人，而StaticHosts用于手动/静态设置Kafka经纪人及其详细信息。ZkHosts是访问Kafka经纪人的简单而快速的方式。

ZkHosts的签名如下：

    public ZkHosts(String brokerZkStr, String brokerZkPath)
    public ZkHosts(String brokerZkStr)

其中brokerZkStr是ZooKeeper主机和brokerZkPath是ZooKeeper路径来维护Kafka代理详细信息。

**KafkaConfig API**
此API用于定义Kafka集群的配置设置。Kafka Con-fig的签名定义如下

    public KafkaConfig(BrokerHosts hosts, string topic)

 - **Hosts** - BrokerHosts可以是ZkHosts / StaticHosts。
 - **Topic** - 主题名称。

**SpoutConfig API**
Spoutconfig是KafkaConfig的扩展，它支持更多的ZooKeeper信息。

    public SpoutConfig(BrokerHosts hosts, string topic, string zkRoot, string id)

 - **Hosts** - BrokerHosts可以是BrokerHosts接口的任何实现
 - **Topic**- 主题名称。
 - **zkRoot** - ZooKeeper根路径。
 - **id** -spout stores存储在Zookeeper中消耗的偏移量的状态。该ID应该唯一标识您的spout。

**SchemeAsMultiScheme**
SchemeAsMultiScheme是一个接口，用于指示从Kafka消耗的ByteBuffer如何转换为Storm元组。它来自MultiScheme并接受Scheme类的实现。有很多的Scheme类的实现，一个这样的实现是StringScheme，它将字节解析为一个简单的字符串。它还控制输出字段的命名。签名的定义如下。

    public SchemeAsMultiScheme(Scheme scheme)

 -  **Scheme**  - 从卡夫卡消费的字节缓冲区。

**KafkaSpout API**
KafkaSpout是我们的spout实现，它将与Storm集成。它从卡夫卡主题中获取消息，并将其作为元组发送到Storm生态系统。KafkaSpout从SpoutConfig获取其配置详细信息。

`以下是创建简单Kafkaspout的示例代码`。

```java
// ZooKeeper connection string
BrokerHosts hosts = new ZkHosts(zkConnString);

//Creating SpoutConfig Object
SpoutConfig spoutConfig = new SpoutConfig(hosts, 
   topicName, "/" + topicName UUID.randomUUID().toString());

//convert the ByteBuffer to String.
spoutConfig.scheme = new SchemeAsMultiScheme(new StringScheme());

//Assign SpoutConfig to KafkaSpout.
KafkaSpout kafkaSpout = new KafkaSpout(spoutConfig);
```

#### Bolt Creation
Bolt是一个组件，它将元组作为输入，处理元组，并产生新的元组作为输出。Bolts 将实现IRichBolt接口。在这个程序中，使用两个Bolts 类WordSplitter-Bolt和WordCounterBolt进行操作。

`IRichBolt`接口有以下几种方法 -

 - **Prepare**- 为Bolts 提供执行的环境。执行器将运行此方法来初始化spout。
 - **Execute**- 处理一个单独的输入元组。
 - **Cleanup** - 当Bolts 关闭时调用。
 - **declareOutputFields** - 声明元组的输出模式。

让我们创建SplitBolt.java，它实现将一个句子分割成单词和CountBolt.java的逻辑，该实现逻辑用于分隔唯一的单词并计算其发生。

**`SplitBolt.java`**
```java
import java.util.Map;

import backtype.storm.tuple.Tuple;
import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Values;

import backtype.storm.task.OutputCollector;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.topology.IRichBolt;
import backtype.storm.task.TopologyContext;

public class SplitBolt implements IRichBolt {
   private OutputCollector collector;
   
   @Override
   public void prepare(Map stormConf, TopologyContext context,
      OutputCollector collector) {
      this.collector = collector;
   }
   
   @Override
   public void execute(Tuple input) {
      String sentence = input.getString(0);
      String[] words = sentence.split(" ");
      
      for(String word: words) {
         word = word.trim();
         
         if(!word.isEmpty()) {
            word = word.toLowerCase();
            collector.emit(new Values(word));
         }
         
      }

      collector.ack(input);
   }
   
   @Override
   public void declareOutputFields(OutputFieldsDeclarer declarer) {
      declarer.declare(new Fields("word"));
   }

   @Override
   public void cleanup() {}
   
   @Override
   public Map<String, Object> getComponentConfiguration() {
      return null;
   }
   
}
```
**`CountBolt.java`**
```java
import java.util.Map;
import java.util.HashMap;

import backtype.storm.tuple.Tuple;
import backtype.storm.task.OutputCollector;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.topology.IRichBolt;
import backtype.storm.task.TopologyContext;

public class CountBolt implements IRichBolt{
   Map<String, Integer> counters;
   private OutputCollector collector;
   
   @Override
   public void prepare(Map stormConf, TopologyContext context,
   OutputCollector collector) {
      this.counters = new HashMap<String, Integer>();
      this.collector = collector;
   }

   @Override
   public void execute(Tuple input) {
      String str = input.getString(0);
      
      if(!counters.containsKey(str)){
         counters.put(str, 1);
      }else {
         Integer c = counters.get(str) +1;
         counters.put(str, c);
      }
   
      collector.ack(input);
   }

   @Override
   public void cleanup() {
      for(Map.Entry<String, Integer> entry:counters.entrySet()){
         System.out.println(entry.getKey()+" : " + entry.getValue());
      }
   }

   @Override
   public void declareOutputFields(OutputFieldsDeclarer declarer) {
   
   }

   @Override
   public Map<String, Object> getComponentConfiguration() {
      return null;
   }
}
```
### 提交到Topology
Storm拓扑基本上是一个节俭的结构。`TopologyBuilder`类提供简单而简单的方法来创建复杂的拓扑。TopologyBuilder类具有设置spout（setSpout）和设置bolt（setBolt）的方法。最后，TopologyBuilder已经创建了一个创建To-pology的拓扑。shuffleGrouping和fieldsGrouping方法有助于为喷嘴和Bolts 设置流分组。

本地集群 - 为了开发目的，我们可以使用LocalCluster对象创建本地集群，然后使用LocalCluster类的submitTopology方法提交拓扑。

**`KafkaStormSample.java`**
```java
import backtype.storm.Config;
import backtype.storm.LocalCluster;
import backtype.storm.topology.TopologyBuilder;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

import backtype.storm.spout.SchemeAsMultiScheme;
import storm.kafka.trident.GlobalPartitionInformation;
import storm.kafka.ZkHosts;
import storm.kafka.Broker;
import storm.kafka.StaticHosts;
import storm.kafka.BrokerHosts;
import storm.kafka.SpoutConfig;
import storm.kafka.KafkaConfig;
import storm.kafka.KafkaSpout;
import storm.kafka.StringScheme;

public class KafkaStormSample {
   public static void main(String[] args) throws Exception{
      Config config = new Config();
      config.setDebug(true);
      config.put(Config.TOPOLOGY_MAX_SPOUT_PENDING, 1);
      String zkConnString = "localhost:2181";
      String topic = "my-first-topic";
      BrokerHosts hosts = new ZkHosts(zkConnString);
      
      SpoutConfig kafkaSpoutConfig = new SpoutConfig (hosts, topic, "/" + topic,    
         UUID.randomUUID().toString());
      kafkaSpoutConfig.bufferSizeBytes = 1024 * 1024 * 4;
      kafkaSpoutConfig.fetchSizeBytes = 1024 * 1024 * 4;
      kafkaSpoutConfig.forceFromStart = true;
      kafkaSpoutConfig.scheme = new SchemeAsMultiScheme(new StringScheme());

      TopologyBuilder builder = new TopologyBuilder();
      builder.setSpout("kafka-spout", new KafkaSpout(kafkaSpoutCon-fig));
      builder.setBolt("word-spitter", new SplitBolt()).shuffleGroup-ing("kafka-spout");
      builder.setBolt("word-counter", new CountBolt()).shuffleGroup-ing("word-spitter");
         
      LocalCluster cluster = new LocalCluster();
      cluster.submitTopology("KafkaStormSample", config, builder.create-Topology());

      Thread.sleep(10000);
      
      cluster.shutdown();
   }
}
```

在移动编译之前，Kakfa-Storm集成需要策展人ZooKeeper客户端的java库。策展人版本2.9.1支持Apache Storm版本0.9.5（我们在本教程中使用）。下载指定的jar文件并将其放在java类路径中。

> - curator-client-2.9.1.jar
> - curator-framework-2.9.1.jar

包含依赖文件后，使用以下命令编译程序，

    javac -cp "/path/to/Kafka/apache-storm-0.9.5/lib/*" *.java

**执行**
启动Kafka Producer CLI（在上一章中介绍），创建一个名为my-first-topic的新主题，并提供一些示例消息，如下所示：

    hello
    kafka
    storm
    spark
    test message
    another test message

现在使用以下命令执行应用程序 -

    java -cp“/path/to/Kafka/apache-storm-0.9.5/lib/*”：。KafkaStormSample

本应用程序的示例输出如下：

    storm : 1
    test : 2
    spark : 1
    another : 1
    kafka : 1
    hello : 1
    message : 2






