---
title: 关于Apache Spark
date: 2017-07-23 14:18:42
tags: Spark
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/spark.png
keywords: hadoop,spark,Apache Spark
description: Apache Spark是一个开放源码，Hadoop兼容，快速，富于表现力的集群计算平台。它是在加州大学伯克利分校的AMPLabs创建的，作为伯克利数据分析平台（BDAS）的一部分。它已经成为一个顶级的Apache项目。图4显示了当前Apache Spark堆栈的各种组件。
---

## 关于Apache Spark

Apache Spark是一个开放源码，Hadoop兼容，快速，富于表现力的集群计算平台。它是在加州大学伯克利分校的AMPLabs创建的，作为伯克利数据分析平台（BDAS）的一部分。它已经成为一个顶级的Apache项目。图4显示了当前Apache Spark堆栈的各种组件。

![](http://osewdhh4j.bkt.clouddn.com/20170723141505.png)

它有五大优点：

 1. 闪电的计算速度，因为数据被加载到分布式存储器（RAM）的机器集群上。可以对数据进行快速转换，并根据需要进行缓存，以便后续使用。已经注意到，由于内存不足，一些数据溢出到磁盘上时，Apache
    Spark会比Hadoop Map更快地处理数据，当所有数据都适合内存时，数据速度提升10倍。
    ![enter image description here](http://osewdhh4j.bkt.clouddn.com/20170723141522.png)
 2. 通过Java，Scala，Python，SQL（用于交互式查询）内置的标准API可以很方便地访问，并且具有丰富的机器学习库可用于开箱即用。
 3. 与现有的Hadoop v1（SIMR）和2.x（YARN）生态系统的兼容性使公司能够利用其现有的基础架构。
 ![](http://osewdhh4j.bkt.clouddn.com/20170723141540.png)
 4. 方便的下载和安装过程。方便的shell（REPL：Read-Eval-Print-Loop）交互式学习API。
 5. 提高生产率，因为高层次结构将重点放在计算内容上。

此外，Spark在Scala中实现，这意味着代码非常简洁。


