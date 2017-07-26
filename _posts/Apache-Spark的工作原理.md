---
title: Apache Spark的工作原理
date: 2017-07-23 14:34:57
tags: Spark
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/spark.png
keywords: hadoop,spark,Apache Spark
description: Spark引擎提供了一种在一组机器上分布式内存中处理数据的方法。图7显示了典型的Spark作业如何处理信息的逻辑图。
---

## Apache Spark的工作原理

Spark引擎提供了一种在一组机器上分布式内存中处理数据的方法。图7显示了典型的Spark作业如何处理信息的逻辑图。

![](http://osewdhh4j.bkt.clouddn.com/20170723143341.png)

![](http://osewdhh4j.bkt.clouddn.com/20170723143400.png)


主控制如何分割数据，并利用数据位置，同时跟踪从机上的所有分布式数据计算。如果某台从机不可用，该机器上的数据将在其他可用的机器上重建。“大师”目前是一个单一的失败点，但将在即将发布的版本中修复。


