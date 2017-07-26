---
title: Apache Kafka - 生产者示例
date: 2017-07-13 20:56:48
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/kafka%E7%94%9F%E4%BA%A7%E8%80%85%E7%A4%BA%E4%BE%8B.png
keywords: Apache Kafka - 生产者示例,kafka,生产者示例
description: 让我们创建一个使用Java客户端发布和使用消息的应用程序。Kafka生产者客户端由以下API组成。让我们了解本节中最重要的一套Kafka生产者API。KafkaProducer API的核心部分是KafkaProducer类。KafkaProducer类提供了一个选项，可以使用以下方法在其构造函数中连接Kafka代理。
---

## Apache Kafka教程  之 Apache Kafka - 生产者示例

![http://blogxinxiucan.sh1.newtouch.com/](http://osewdhh4j.bkt.clouddn.com/kafka%E7%94%9F%E4%BA%A7%E8%80%85%E7%A4%BA%E4%BE%8B.png)

### Apache Kafka - 生产者示例

> 让我们创建一个使用Java客户端发布和使用消息的应用程序。Kafka生产者客户端由以下API组成。

### KafkaProducer API
让我们了解本节中最重要的一套Kafka生产者API。KafkaProducer API的核心部分是KafkaProducer类。

 - KafkaProducer类提供了一个选项，可以使用以下方法在其构造函数中连接Kafka代理。

`producer.send(new ProducerRecord<byte[],byte[]>(topic,  partition, key1, value1) , callback); `
   
 - ProducerRecord - 生产者管理一个等待发送的记录缓冲区。
 - 回调 - 当服务器确认记录时执行的用户提供的回调（null表示无回调）。
 - KafkaProducer类提供了一种刷新方法，以确保所有先前发送的消息已经实际完成。flush方法的语法如下 public void flush()
 - KafkaProducer类提供了partitionFor方法，它有助于获取给定主题的分区元数据。这可以用于自定义分区。此方法的签名如下
`public Map metrics()`
它返回生产者维护的内部指标图。
 - public void close（） KafkaProducer类提供了紧密的方法块，直到所有先前发送的请求完成为止。

### 生产者API
Producer API的中心部分是Producer类。生产者类提供了通过以下方法在其构造函数中连接Kafka代理的选项。

**生产者类**
生产者类提供发送方法，使用以下签名将消息发送到单个或多个主题。

    public void send(KeyedMessaget<k,v> message) 
    - sends the data to a single topic,par-titioned by key using either sync or async producer.
    public void send(List<KeyedMessage<k,v>>messages)
    - sends data to multiple topics.
    Properties prop = new Properties();
    prop.put(producer.type,”async”)
    ProducerConfig config = new ProducerConfig(prop);

有两种类型的生产者 - **同步**和**异步**。

同样的API配置也适用于同步生成器。它们之间的区别是同步生成器直接发送消息，但是在后台发送消息。当您想要更高的吞吐量时，Async生产者是首选。在以前的版本，如0.8，一个异步生成器没有回调send（）来注册错误处理程序。这仅在当前版本的0.9中可用。

**public void close（）**
生产者类提供了关闭与所有kafka兄弟的生产者池连接的紧密方法。

### 配置设置
Producer API的主要配置设置列在下表中，以便更好地了解 -

S.No	 | 配置设置和说明
----|-----------
1  client.id  | 识别生产者应用程序
2	 producer.type   |    同步或异步
3	 Acks    |    acks配置控制生产者请求下的条件被完全匹配。
4	 retries |  如果生产者请求失败，则会自动重试具体值。
5	 bootstrap.servers |  经纪人的引导列表。
6	 linger.ms  |  如果要减少请求数，可以将linger.ms设置为大于某值的值。
7	 key.serializer | 串行器接口的关键。
8	 value.serializer | 串行器接口的值。
9	batch.size | 缓冲区大小。
10	buffer.memory | 控制生产者可用于缓冲的总内存量。

### ProducerRecord API
ProducerRecord是发送给`Kafka cluster.ProducerRecord`类构造函数的一个键/值对，用于使用以下签名创建具有分区，键和值对的记录。

    public ProducerRecord (string topic, int partition, k key, v value)

 - **Topic** - 将附加到记录的用户定义的主题名称。
 - **Partition** - 分区计数
 - **Key** - 将包括在记录中的关键。
 - **Value** - 记录内容

    public ProducerRecord (string topic, k key, v value)

ProducerRecord类构造函数用于创建具有键，值对和无分区的记录。

 - **Topic**- 创建主题以分配记录。
 - **Key** - 键记录。
 - **Value** - 记录内容。

    public ProducerRecord (string topic, v value)

ProducerRecord类创建一个没有分区和键的记录。

 - **Topic**- 创建主题。
 - **Value** - 记录内容。

ProducerRecord类方法列在下表中：

S.No	 | 类方法和描述
------ | ----
1 | public string topic() 主题将附加到记录。
2|	public K key（）将包含在记录中的关键字。如果没有这样的键，null将在这里重新转换。
3|	public V value（）记录内容。
4|	partition() 记录的分区数

### SimpleProducer应用程序
在创建应用程序之前，首先启动ZooKeeper和Kafka代理，然后使用create topic命令在Kafka代理中创建自己的主题。之后，创建一个名为`SimplepleProducer.java`的java类，并键入以下代码。

```java

//import util.properties packages
import java.util.Properties;

//import simple producer packages
import org.apache.kafka.clients.producer.Producer;

//import KafkaProducer packages
import org.apache.kafka.clients.producer.KafkaProducer;

//import ProducerRecord packages
import org.apache.kafka.clients.producer.ProducerRecord;

//Create java class named “SimpleProducer”
public class SimpleProducer {
   
   public static void main(String[] args) throws Exception{
      
      // Check arguments length value
      if(args.length == 0){
         System.out.println("Enter topic name”);
         return;
      }
      
      //Assign topicName to string variable
      String topicName = args[0].toString();
      
      // create instance for properties to access producer configs   
      Properties props = new Properties();
      
      //Assign localhost id
      props.put("bootstrap.servers", “localhost:9092");
      
      //Set acknowledgements for producer requests.      
      props.put("acks", “all");
      
      //If the request fails, the producer can automatically retry,
      props.put("retries", 0);
      
      //Specify buffer size in config
      props.put("batch.size", 16384);
      
      //Reduce the no of requests less than 0   
      props.put("linger.ms", 1);
      
      //The buffer.memory controls the total amount of memory available to the producer for buffering.   
      props.put("buffer.memory", 33554432);
      
      props.put("key.serializer", 
         "org.apache.kafka.common.serializa-tion.StringSerializer");
         
      props.put("value.serializer", 
         "org.apache.kafka.common.serializa-tion.StringSerializer");
      
      Producer<String, String> producer = new KafkaProducer
         <String, String>(props);
            
      for(int i = 0; i < 10; i++)
         producer.send(new ProducerRecord<String, String>(topicName, 
            Integer.toString(i), Integer.toString(i)));
               System.out.println(“Message sent successfully”);
               producer.close();
   }
}
```

编译 - 可以使用以下命令编译应用程序。

    javac -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*” *.java

执行 - 可以使用以下命令执行应用程序。

    java -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*”:. SimpleProducer <topic-name>

产量
```java
Message sent successfully
To check the above output open new terminal and type Consumer CLI command to receive messages.
>> bin/kafka-console-consumer.sh --zookeeper localhost:2181 —topic <topic-name> —from-beginning
1
2
3
4
5
6
7
8
9
10
```
### 简单的消费者例子
到目前为止，我们已经创建了一个生产者来发送消息到Kafka集群。现在让我们创建一个消费者来消费kafka群集的消息。KafkaConsumer API用于消费来自Kafka群集的消息。KafkaConsumer类构造函数定义如下。

    public KafkaConsumer(java.util.Map<java.lang.String,java.lang.Object> configs)

configs - 返回消费者配置的映射。

`KafkaConsumer`类具有下表中列出的以下重要方法。

S.No	|方法和说明
------|----
1	|public java.util.Set <TopicPar-tition> assignment（）获取当前由con-sumer分配的分区集。
2	|public string subscription（）订阅给定的主题列表以获得动态签名的分区。
3	|public void sub-scribe（java.util.List <java.lang.String> topics，ConsumerRe-balanceListener listener）订阅给定的主题列表以获得动态签名的分区。
4	|public void unsubscribe（）从给定的分区列表中取消订阅主题。
5	 |public void sub-scribe（java.util.List <java.lang.String> topics）订阅给定的主题列表以获得动态签名的分区。如果给定的主题列表为空，则它将被视为与unsubscribe（）相同。
6	|public void sub-scribe（java.util.regex.Pattern pattern，ConsumerRebalanceLis-tener listener）参数模式是指以正则表达式格式的订阅模式，并且listener参数从订阅模式获取通知。
7	|public void as-sign（java.util.List <TopicParti-tion>分区）手动分配给客户的分区列表。
8	|poll()获取使用其中一个订阅/分配API指定的主题或分区的数据。如果在轮询数据之前没有订阅主题，这将返回错误。
9|	public void commitSync（）针对所有主题和分区的划分列表，对最后一次poll（）返回的提交偏移量。相同的操作将应用于commitAsyn（）。
10	|public void seek（TopicPartition partition，long offset）获取消费者将在下一个poll（）方法上使用的当前偏移值。
11	|public void resume（）恢复已暂停的分区。
12	|public void wakeup（）唤醒消费者。

### ConsumerRecord API
`ConsumerRecord API`用于从Kafka集群接收记录。该API由一个主题名称，分区号，从其接收的记录和指向Kafka分区中的记录的偏移量组成。`ConsumerRecord`类用于创建具有特定主题名称，分区计数和<key，value>对的消费者记录。它具有以下签名。

    public ConsumerRecord(string topic,int partition, long offset,K key, V value)

 - **Topic** - 从kafka群集收到的消费者记录的主题名称。
 - **Partition** - 主题分区。
 - **Key** - 记录的键，如果没有键存在null将被返回。
 - **Value** - 记录内容。

ConsumerRecords API
ConsumerRecords API充当ConsumerRecord的容器。该API用于为特定主题保留每个分区的ConsumerRecord列表。其构造函数定义如下。

`public ConsumerRecords(java.util.Map<TopicPartition,java.util.List
<Consumer-Record>K,V>>> records)`

 - **TopicPartition** - 返回特定主题的分区映射。
 - **记录** - ConsumerRecord的返回列表。

### ConsumerRecords类定义了以下方法。

S.No	|方法和说明
----|---
1	 |public int count（） 所有主题的记录数。
2	 |public set partitions（） 该记录集中的数据集（如果没有数据被返回，则该集合为空）。
3	| public Iterator iterator（） 迭代器使您能够遍历集合，获取或重新移动元素。
4	 |公开列表记录（） 获取给定分区的记录列表。

### 配置设置
Consumer客户端API主配置设置的配置设置如下所示：

S.No	| 设置和说明
----|---
1	| bootstrap.servers 经纪人列表。
2	| group.id 将一个消费者分配给一个组。
3	| enable.auto.commit 如果值为true，则启用自动提交偏移量，否则不提交。
4	| auto.commit.interval.ms 更新消耗的偏移量返回给ZooKeeper的频率。
5 |	 session.timeout.ms 表示Kafka将在放弃并继续使用消息之前等待ZooKeeper响应请求（读取或写入）多少毫秒。

**SimpleConsumer应用程序**
生产者应用步骤在此保持不变。首先，启动您的ZooKeeper和Kafka经纪人。然后使用名为`SimpleCon-sumer.java`的java类创建一个`SimpleConsumer`应用程序，并键入以下代码。

```java
import java.util.Properties;
import java.util.Arrays;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;

public class SimpleConsumer {
   public static void main(String[] args) throws Exception {
      if(args.length == 0){
         System.out.println("Enter topic name");
         return;
      }
      //Kafka consumer configuration settings
      String topicName = args[0].toString();
      Properties props = new Properties();
      
      props.put("bootstrap.servers", "localhost:9092");
      props.put("group.id", "test");
      props.put("enable.auto.commit", "true");
      props.put("auto.commit.interval.ms", "1000");
      props.put("session.timeout.ms", "30000");
      props.put("key.deserializer", 
         "org.apache.kafka.common.serializa-tion.StringDeserializer");
      props.put("value.deserializer", 
         "org.apache.kafka.common.serializa-tion.StringDeserializer");
      KafkaConsumer<String, String> consumer = new KafkaConsumer
         <String, String>(props);
      
      //Kafka Consumer subscribes list of topics here.
      consumer.subscribe(Arrays.asList(topicName))
      
      //print the topic name
      System.out.println("Subscribed to topic " + topicName);
      int i = 0;
      
      while (true) {
         ConsumerRecords<String, String> records = con-sumer.poll(100);
         for (ConsumerRecord<String, String> record : records)
         
         // print the offset,key and value for the consumer records.
         System.out.printf("offset = %d, key = %s, value = %s\n", 
            record.offset(), record.key(), record.value());
      }
   }
}
```

**编译 - 可以使用以下命令编译应用程序。**

    javac -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*” *.java

**执行 -可以使用以下命令执行应用程序**

    java -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*”:. SimpleConsumer <topic-name>

**输入 - 打开生产者CLI并向主题发送一些消息。您可以将smple输入作为“您好消费者”。**

**输出 - 以下是输出。**

    Subscribed to topic Hello-Kafka
    offset = 3, key = null, value = Hello Consumer

