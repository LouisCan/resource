---
title: 如何安装Apache Spark
date: 2017-07-23 14:29:21
tags: Spark
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/spark.png
keywords: hadoop,spark,Apache Spark
description: Apache Spark可以配置为独立运行，也可以在Hadoop V1 SIMR或Hadoop 2 YARN / Mesos上运行。Apache Spark需要Java，Scala或Python中等技能。这里我们将看到如何在独立配置中安装和运行Apache Spark。
---

## 如何安装Apache Spark

下表列出了一些重要的链接和先决条件：

当前版本 |	1.0.1 @ http://d3kbcqa49mib13.cloudfront.net/spark-1.0.1.tgz
-----|---
下载页面|	https://spark.apache.org/downloads.html
JDK版本（必填）|	1.6以上
Scala版本（必填）|	2.10以上
Python（可选）|	[2.6,3.0）
简单构建工具（必需）|	http://www.scala-sbt.org
开发版本|	git clone git：//github.com/apache/spark.git
Building说明|	https://spark.apache.org/docs/latest/building-with-maven.html
Maven|	3.0以上

**Apache Spark可以配置为独立运行，也可以在Hadoop V1 SIMR或Hadoop 2 YARN / Mesos上运行。Apache Spark需要Java，Scala或Python中等技能。这里我们将看到如何在独立配置中安装和运行Apache Spark。**

 1. 安装JDK 1.6+，Scala 2.10+，Python [2.6,3）和sbt
 2. 下载Apache Spark 1.0.1发行版
 3. 在指定的目录中解压缩并解压缩spark-1.0.1.tgz
```
akuntamukkala@localhost~/Downloads$ pwd 
   /Users/akuntamukkala/Downloads akuntamukkala@localhost~/Downloads$ tar -zxvf spark- 1.0.1.tgz -C /Users/akuntamukkala/spark
```
 4、 从＃4转到目录并运行sbt来构建Apache Spark
```
akuntamukkala@localhost~/spark/spark-1.0.1$ pwd /Users/akuntamukkala/spark/spark-1.0.1 akuntamukkala@localhost~/spark/spark-1.0.1$ sbt/sbt assembly
```
 5、 启动Apache Spark独立REPL对于Scala，请使用：
 ```
 / Users / akuntamukkala / spark / spark - 1.0。1 / bin / spark - shell
 ```
  对于Python，请使用：
  ```
  /Users/akuntamukkala/spark/spark-1.0.1/bin/ pyspark
  ```
  
 6.、转到SparkUI @ http：// localhost：4040




