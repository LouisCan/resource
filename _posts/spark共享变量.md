---
title: spark共享变量
date: 2017-07-23 15:11:30
tags: Spark
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/spark.png
keywords: hadoop,spark,Apache Spark
description: Spark提供了一种非常方便的方法，通过提供累加器来避免可变计数器和计数器同步问题。累加器在具有默认值的Spark上下文中初始化。这些累加器在从站节点上可用，但从站节点无法读取它们。他们唯一的目的是获取原子更新并将其转发给Master。Master是唯一可以读取和计算所有更新的聚合的程序。例如，假设我们想要在日志级别“错误”的日志文件中查找语句的数量...
---

## spark共享变量

### Accumulators
Spark提供了一种非常方便的方法，通过提供累加器来避免可变计数器和计数器同步问题。累加器在具有默认值的Spark上下文中初始化。这些累加器在从站节点上可用，但从站节点无法读取它们。他们唯一的目的是获取原子更新并将其转发给Master。Master是唯一可以读取和计算所有更新的聚合的程序。例如，假设我们想要在日志级别“错误”的日志文件中查找语句的数量...

```
akuntamukkala@localhost~/temp$ cat output.log
error
warning
info
trace
error
info
info
scala> val nErrors=sc.accumulator(0.0)
scala> val logs = sc.textFile(“/Users/akuntamukkala/temp/output.log”)
scala> logs.filter(_.contains(“error”)).foreach(x=>nErrors+=1)
scala> nErrors.value
Result:Int = 2
```

### 广播变量
在RDD上执行加入操作以通过某个密钥合并数据是很常见的。在这种情况下，很可能将大型数据集发送到从属节点，从属节点将托管要连接的分区。这表现出巨大的性能瓶颈，因为网络I / O比RAM访问慢100倍。为了减轻这个问题，Spark提供了广播变量，顾名思义，广播变量被广播到从节点。节点上的RDD操作可以快速访问广播变量值。例如，假设我们要计算文件中所有订单项的运费。我们有一个静态查找表来指定每种运输类型的成本。该查找表可以是广播变量。

```
akuntamukkala@localhost~/temp$ cat packagesToShip.txt ground
express
media
priority
priority
ground
express
media
scala> val map = sc.parallelize(Seq((“ground”,1),(“med”,2), (“priority”,5),(“express”,10))).collect().toMap
map: scala.collection.immutable.Map[String,Int] = Map(ground -> 1, media -> 2, priority -> 5, express -> 10)
scala> val bcMailRates = sc.broadcast(map)
```

在上述命令中，我们创建一个广播变量，一个包含按服务类别的成本的地图。

    <p>scala> val pts = sc.textFile(“/Users/akuntamukkala/temp/packagesToShip.txt”)</p>

    <p>scala> pts.map(shipType=>(shipType,1)).reduceByKey(_+_). map{case (shipType,nPackages)=>(shipType,nPackages*bcMailRates. value(shipType))}.collect()</p>

在上面的命令中，我们通过从广播变量查询邮寄率来计算运输成本。

```
Array[(String, Int)] = Array((priority,10), (express,20), (media,4), (ground,2))
scala> val shippingCost=sc.accumulator(0.0)
scala> pts.map(x=>(x,1)).reduceByKey(_+_).map{case (x,y)=>(x,y*bcMailRates.value(x))}.foreach(v=>shippingCost+=v._2) scala> shippingCost.value
Result: Double = 36.0
</p>
```

在上面的命令中，我们使用累加器来计算总成本。以下演示文稿提供了更多信息：

http://ampcamp.berkeley.edu/wp-content/uploads/2012/06/matei-zaharia-amp-camp-2012-advanced-spark.pdf


