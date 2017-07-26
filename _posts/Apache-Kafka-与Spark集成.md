---
title: Apache Kafka - 与Spark集成
date: 2017-07-14 20:03:32
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/kafka%E4%B8%8ESpark%E9%9B%86%E6%88%90.png
keywords: Apache Kafka - 与Spark集成,kafka,spark,kafka spark
description: Spark Streaming API支持实时数据流的可扩展，高吞吐量，容错流处理。数据可以从诸如Kafka，Flume，Twitter等许多来源中获取，并且可以使用诸如地图，缩小，连接和窗口之类的高级功能的复杂算法进行处理。最后，处理后的数据可以推送到文件系统，数据库和现场仪表盘上。弹性分布式数据集（RDD）是Spark的基础数据结构。它是一个不可变的分布式对象集合。RDD中的每个数据集分为逻辑分区，可以在集群的不同节点上进行计算。
---

## Apache Kafka教程  之 与Spark集成

![http://blogxinxiucan.sh1.newtouch.com/](http://osewdhh4j.bkt.clouddn.com/kafka%E4%B8%8ESpark%E9%9B%86%E6%88%90.png)

### Apache Kafka - 与Spark集成

### 关于Spark
`Spark Streaming API`支持实时数据流的可扩展，高吞吐量，容错流处理。数据可以从诸如Kafka，Flume，Twitter等许多来源中获取，并且可以使用诸如地图，缩小，连接和窗口之类的高级功能的复杂算法进行处理。最后，处理后的数据可以推送到文件系统，数据库和现场仪表盘上。`弹性分布式数据集（RDD）`是Spark的基础数据结构。它是一个不可变的分布式对象集合。RDD中的每个数据集分为逻辑分区，可以在集群的不同节点上进行计算。

### 与Spark集成
**Kafka**是Spark流媒体的潜在消息传递和集成平台。卡夫卡作为实时数据流的中心枢纽，并使用Spark Streaming中的复杂算法进行处理。处理数据后，`Spark Streaming`可能会将结果发布到`HDFS`，数据库或仪表板中的另一个Kafka主题或存储。下图描绘了概念流程。

![与Spark集成](http://osewdhh4j.bkt.clouddn.com/integration_spark.jpg)

现在，让我们详细介绍一下Kafka-Spark API。

#### SparkConf API
它代表Spark应用程序的配置。用于将各种Spark参数设置为键值对。

`SparkConf`类有以下方法 -

> - **set**（string key，string value） - 设置配置变量。
> - **remove**（string key） - 从配置中删除密钥。
> - **setAppName**（string name） - 为应用程序设置应用程序名称。
> - **get**（string key） - 获取密钥

#### StreamingContext API
这是Spark功能的主要入口点。SparkContext表示与Spark集群的连接，可用于在集群上创建RDD，累加器和广播变量。签名的定义如下图所示。

    public StreamingContext(String master, String appName, Duration batchDuration, 
       String sparkHome, scala.collection.Seq<String> jars, 
       scala.collection.Map<String,String> environment)

 >- **master** - 要连接的群集URL（e.g. mesos://host:port, spark://host:port, local[4]）。
 >- **appName** - 您的工作的名称，以在集群Web UI上显示
 >- **batchDuration** - 流数据将分批的时间间隔

    public StreamingContext(SparkConf conf, Duration batchDuration)

通过提供新的SparkContext所需的配置来创建StreamingContext。

 >- **conf** - Spark参数
 >- **batchDuration** - 流数据将分批的时间间隔

#### KafkaUtils API
KafkaUtils API用于将Kafka集群连接到Spark流。此API具有如下定义的重要方法createStream签名。

    public static ReceiverInputDStream<scala.Tuple2<String,String>> createStream(
       StreamingContext ssc, String zkQuorum, String groupId,
       scala.collection.immutable.Map<String,Object> topics, StorageLevel storageLevel)

上面显示的方法用于创建从卡夫卡经纪公司提取消息的输入流。

 - ssc - StreamingContext对象。
 - zkQuorum - Zookeeper法定人数。
 - groupId - 此消费者的组ID。
 - topics- 返回主题地图消费。
 - storageLevel - 用于存储接收到的对象的存储级别。

`KafkaUtils API`有另一种方法`createDirectStream`，用于创建一个直接从卡夫卡经纪公司提取消息而不使用任何接收器的输入流。该流可以保证来自Kafka的每条消息都被包含在转换中一次。

示例应用程序在`Scala`中完成。要编译应用程序，请下载并安装**sbt，scala构建工具**（类似于maven）。主要应用代码如下。

```java
import java.util.HashMap

import org.apache.kafka.clients.producer.{KafkaProducer, ProducerConfig, Produc-erRecord}
import org.apache.spark.SparkConf
import org.apache.spark.streaming._
import org.apache.spark.streaming.kafka._

object KafkaWordCount {
   def main(args: Array[String]) {
      if (args.length < 4) {
         System.err.println("Usage: KafkaWordCount <zkQuorum><group> <topics> <numThreads>")
         System.exit(1)
      }

      val Array(zkQuorum, group, topics, numThreads) = args
      val sparkConf = new SparkConf().setAppName("KafkaWordCount")
      val ssc = new StreamingContext(sparkConf, Seconds(2))
      ssc.checkpoint("checkpoint")

      val topicMap = topics.split(",").map((_, numThreads.toInt)).toMap
      val lines = KafkaUtils.createStream(ssc, zkQuorum, group, topicMap).map(_._2)
      val words = lines.flatMap(_.split(" "))
      val wordCounts = words.map(x => (x, 1L))
         .reduceByKeyAndWindow(_ + _, _ - _, Minutes(10), Seconds(2), 2)
      wordCounts.print()

      ssc.start()
      ssc.awaitTermination()
   }
}
```

#### 构建脚本
spark-kafka集成取决于spark，spark流和spark卡夫卡集成罐。创建一个新的文件build.sbt并指定应用程序细节及其依赖关系。该SBT在编译和打包应用程序会下载必要的罐子。

 - name := "Spark Kafka Project" version := "1.0" scalaVersion :=
   "2.10.5"
   
   libraryDependencies += "org.apache.spark" %% "spark-core" % "1.6.0"
   libraryDependencies += "org.apache.spark" %% "spark-streaming" %
   "1.6.0" libraryDependencies += "org.apache.spark" %%
   "spark-streaming-kafka" % "1.6.0"

#### 汇编/包装
运行以下命令来编译和打包应用程序的jar文件。我们需要将jar文件提交到spark控制台来运行应用程序。

    sbt package

**提交到Spark**
启动Kafka Producer CLI（在上一章中介绍），创建一个名为my-first-topic的新主题，并提供一些示例消息，如下所示。

    Another spark test message

运行以下命令将应用程序提交到spark控制台。

    /usr/local/spark/bin/spark-submit --packages org.apache.spark:spark-streaming
    -kafka_2.10:1.6.0 --class "KafkaWordCount" --master local[4] target/scala-2.10/spark
    -kafka-project_2.10-1.0.jar localhost:2181 <group name> <topic name> <number of threads>

此应用程序的示例输出如下所示。

    spark console messages ..
    (Test,1)
    (spark,1)
    (another,1)
    (message,1)
    spark console message ..


