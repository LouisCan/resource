---
title: Apache Kafka - 消费者示例
date: 2017-07-14 19:10:09
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/kafka6%E6%B6%88%E8%B4%B9%E8%80%85%E7%A4%BA%E4%BE%8B.png
keywords: Apache Kafka - 消费者示例,kafka Consumer,kafka
description: Apache Kafka - 消费者（Consumer Group）是从kafka Topic的多线程或多机器消费,添加更多进程/线程将导致kafka重新平衡。如果任何消费者或broker无法向`ZooKeeper`发送心跳信息，则可以通过Kafka群集重新配置。在此重新平衡过程中，Kafka会将可用的分区分配给可用的线程，可能将分区移动到另一个进程。
---

## Apache Kafka教程  之 Apache Kafka - 消费者示例

![http://blogxinxiucan.sh1.newtouch.com/](http://osewdhh4j.bkt.clouddn.com/kafka6%E6%B6%88%E8%B4%B9%E8%80%85%E7%A4%BA%E4%BE%8B.png)

### Apache Kafka - 消费者示例（Consumer Group）
**Consumer Group是从kafka Topic的多线程或多机器消费。**

>  **Consumer Group**
 - 消费者可以使用相同的`group.id`加入`Group`。
 - 组中的最大并行度是组中的消费者数量←不是分区。
 - kafka将一个 Topic的分区分配给组中的消费者，以便每个分区由组中的一个消费者消耗。
 - kafka保证消息只能由组内的**单个消费者**读取。
 - 消费者可以按照日志中存储的顺序查看消息。

### 重新平衡消费者
**添加更多进程/线程将导致kafka重新平衡。如果任何消费者或broker无法向`ZooKeeper`发送心跳信息，则可以通过Kafka群集重新配置。在此重新平衡过程中，Kafka会将可用的分区分配给可用的线程，可能将分区移动到另一个进程。**

```java
import java.util.Properties;
import java.util.Arrays;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;

public class ConsumerGroup {
   public static void main(String[] args) throws Exception {
      if(args.length < 2){
         System.out.println("Usage: consumer <topic> <groupname>");
         return;
      }
      
      String topic = args[0].toString();
      String group = args[1].toString();
      Properties props = new Properties();
      props.put("bootstrap.servers", "localhost:9092");
      props.put("group.id", group);
      props.put("enable.auto.commit", "true");
      props.put("auto.commit.interval.ms", "1000");
      props.put("session.timeout.ms", "30000");
      props.put("key.deserializer",          
         "org.apache.kafka.common.serializa-tion.StringDeserializer");
      props.put("value.deserializer", 
         "org.apache.kafka.common.serializa-tion.StringDeserializer");
      KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(props);
      
      consumer.subscribe(Arrays.asList(topic));
      System.out.println("Subscribed to topic " + topic);
      int i = 0;
         
      while (true) {
         ConsumerRecords<String, String> records = con-sumer.poll(100);
            for (ConsumerRecord<String, String> record : records)
               System.out.printf("offset = %d, key = %s, value = %s\n", 
               record.offset(), record.key(), record.value());
      }     
   }  
}
```
汇编

    javac -cp “/path/to/kafka/kafka_2.11-0.9.0.0/libs/*" ConsumerGroup.java

执行

    >>java -cp “/path/to/kafka/kafka_2.11-0.9.0.0/libs/*":. 
    ConsumerGroup <topic-name> my-group
    >>java -cp "/home/bala/Workspace/kafka/kafka_2.11-0.9.0.0/libs/*":. 
    ConsumerGroup <topic-name> my-group

在这里，我们创建了一个示例组名称作为我的组与两个消费者。同样，您可以创建组中的组和消费者数量。

输入
打开生产者CLI并发送一些消息，如 -

    Test consumer group 01
    Test consumer group 02

第一个过程的输出

    Subscribed to topic Hello-kafka
    offset = 3, key = null, value = Test consumer group 01

第二过程的产出

    Subscribed to topic Hello-kafka
    offset = 3, key = null, value = Test consumer group 02

现在希望您将通过使用Java客户端演示了解`SimpleConsumer`和`ConsumeGroup`。现在您了解如何使用Java客户端发送和接收消息。




