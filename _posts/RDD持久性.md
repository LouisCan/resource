---
title: RDD持久性
date: 2017-07-23 15:04:13
tags: Spark
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/spark.png
keywords: hadoop,spark,Apache Spark
description: Apache Spark的主要功能之一就是在集群内存中持久/缓存RDD。这加速了迭代计算。
---

## RDD持久性

Apache Spark的主要功能之一就是在集群内存中持久/缓存RDD。这加速了迭代计算。

**下表显示了Spark的各种选项**

存储级别 |目的
----|------
MEMORY_ONLY（默认级别）|	此选项将RDD存储在可用的集群存储器中，作为反序列化的Java对象。如果没有足够的集群内存，某些分区可能不会被缓存。这些分区将根据需要在飞行中重新计算。
MEMORY_AND_DISK	|此选项将RDD存储为反序列化的Java对象。如果RDD不适合集群内存，则将这些分区存储在磁盘上，并根据需要读取它们。
MEMORY_ONLY_SER	|此选项将RDD存储为序列化的Java对象（每个分区一个字节数组）。这是更多的CPU密集型，但节省内存，因为它更节省空间。某些分区可能不被缓存。这些将根据需要在飞行中重新计算。
MEMORY_ONLY_DISK_SER|	此选项与上述相同，只是当内存不足时使用该磁盘。
DISC_ONLY	|此选项仅将RDD存储在磁盘上
MEMORY_ONLY_2,MEMORY_AND_DISK_2等|	与其他级别相同，但分区在2个从属节点上进行复制


可以通过RDD上的persist（）操作访问上述存储级别。cache（）操作是指定MEMORY_ONLY选项的一种便捷方式

有关持久性选项的更多详细信息，请参阅：

http://spark.apache.org/docs/latest/programming-guide.html#rdd-persistence

Spark使用最近最少使用（LRU）算法来删除旧的，未使用的缓存的RDD以回收内存。它还提供了一个方便的unpersist（）操作来强制删除缓存/持久化的RDD。


