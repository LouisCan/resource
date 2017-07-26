---
title: Spark SQL
date: 2017-07-23 15:16:17
tags: Spark
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/spark.png
keywords: hadoop,spark,Apache Spark
description: Spark SQL提供了一种方便的方法，使用Spark Engine使用名为SchemaRDD的特殊类型的RDD，在大型数据集上运行交互式查询。SchemaRDD可以从现有的RDD或其他外部数据格式（如Parquet文件，JSON数据）或通过在Hive上运行HQL创建。SchemaRDD与RDBMS中的表类似。一旦数据在SchemaRDD中，Spark引擎就会将其与批量和流式使用情况相统一。
---

## Spark SQL

Spark SQL提供了一种方便的方法，使用Spark Engine使用名为SchemaRDD的特殊类型的RDD，在大型数据集上运行交互式查询。SchemaRDD可以从现有的RDD或其他外部数据格式（如Parquet文件，JSON数据）或通过在Hive上运行HQL创建。SchemaRDD与RDBMS中的表类似。一旦数据在SchemaRDD中，Spark引擎就会将其与批量和流式使用情况相统一。Spark SQL提供两种类型的上下文：扩展SparkContext功能的SQLContext和HiveContext。

SQLContext提供对简单SQL解析器的访问，而HiveContext提供对HiveQL解析器的访问。HiveContext使企业能够利用其现有的Hive基础架构。

让我们来看一个使用SQLContext的简单例子。

说我们有以下'|' 包含客户数据的分隔文件：

```
John Smith|38|M|201 East Heading Way #2203,Irving, TX,75063 Liana Dole|22|F|1023 West Feeder Rd, Plano,TX,75093 Craig Wolf|34|M|75942 Border Trail,Fort Worth,TX,75108 John Ledger|28|M|203 Galaxy Way,Paris, TX,75461 Joe Graham|40|M|5023 Silicon Rd,London,TX,76854
```
定义Scala案例类来表示每一行：
`case class Customer(name:String,age:Int,gender:String,address: String)`

以下代码片段显示如何使用SparkContext创建SQLContext，读取输入文件，将每行转换为SchemaRDD中的记录，然后在简单SQL中查询以查找30岁以下的男性消费者：

```
val sparkConf = new SparkConf().setAppName(“Customers”)
val sc = new SparkContext(sparkConf)
val sqlContext = new SQLContext(sc)
val r = sc.textFile(“/Users/akuntamukkala/temp/customers.txt”) val records = r.map(_.split(‘|’))
val c = records.map(r=>Customer(r(0),r(1).trim.toInt,r(2),r(3))) c.registerAsTable(“customers”)
```

```
sqlContext.sql(“select * from customers where gender=’M’ and age < 30”).collect().foreach(println)
Result:[John Ledger,28,M,203 Galaxy Way,Paris, TX,75461]
```
有关使用SQL＆HiveQL的更实际的示例，请参考以下链接：

https://spark.apache.org/docs/latest/sql-programming-guide.html

https://databricks-training.s3.amazonaws.com/data-exploration-using-spark-sql.html




