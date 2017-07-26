---
title: Spark Streaming
date: 2017-07-23 15:31:46
tags: Spark
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/spark.png
keywords: hadoop,spark,Apache Spark
description: Spark Streaming使用Spark的简单编程模型提供了可扩展，容错，高效的处理流数据的方式。它将流数据转换为“微”批次，这使得Spark的批处理编程模型能够应用于Streaming用例。这种统一的编程模型使得批量和交互式数据处理与流媒体的结合变得容易。图10显示了Spark Streaming如何用于分析来自多个数据源的数据源。
---

## Spark Streaming

![](http://osewdhh4j.bkt.clouddn.com/20170723151914.png)
Spark Streaming使用Spark的简单编程模型提供了可扩展，容错，高效的处理流数据的方式。它将流数据转换为“微”批次，这使得Spark的批处理编程模型能够应用于Streaming用例。这种统一的编程模型使得批量和交互式数据处理与流媒体的结合变得容易。图10显示了Spark Streaming如何用于分析来自多个数据源的数据源。
![](http://osewdhh4j.bkt.clouddn.com/20170723151956.png)

Spark Streaming中的核心抽象是离散流（DStream）。DStream是一系列RDD。每个RDD包含以可配置的时间间隔接收的数据。图12显示了Spark Streaming如何通过将输入数据转换为RDD序列来创建DStream。每个RDD包含由间隔长度定义的每2秒接收的流数据。这可能只有1/2秒，因此处理时间的延迟可能低于1秒。

Spark Streaming还提供复杂的窗口操作符，可帮助您在滚动窗口中对RDD集合进行高效计算。DStream公开了一个API，其中包含应用于组件RDD的运算符（转换和输出运算符）。我们来尝试使用Spark Streaming下载中给出的一个简单示例来理解这一点。假设你想在Twitter流中找到热门哈希标签。请参阅以下示例来查找完整的代码段：


```
spark-1.0.1/examples/src/main/scala/org/apache/spark/examples/streaming/TwitterPopularTags.scala
val sparkConf = new SparkConf().setAppName(“TwitterPopularTags”)
val ssc = new StreamingContext(sparkConf, Seconds(2))
val stream = TwitterUtils.createStream(ssc, None, filters)
```

上面的代码段正在设置Spark Streaming Context。Spark Streaming将在DStream中创建一个包含每两秒检索的Tweets的RDD。

    val hashTags = stream.flatMap(status => status.getText.split(“ “).filter(_.startsWith(“#”)))


上述代码片段将tweet转换为单词序列，然后只对以＃开头的那些进行过滤。

    val topCounts60 = hashTags.map((_, 1)).reduceByKeyAndWindow(_ + _, Seconds(60)).map{case (topic, count) => (count, topic)}. 


上面的代码片段显示了如何计算在60秒的窗口中提到的主题标签的次数的滚动聚合。

```
topCounts60.foreachRDD(rdd => {
val topList = rdd.take(10)
println(“\nPopular topics in last 60 seconds (%s
total):”.format(rdd.count())) topList.foreach{case (count, tag) => println(“%s (%s
tweets)”.format(tag, count))} })
```

上面的代码片段显示了如何提取十大热门推文，然后打印出来

    ssc.start()

上述代码段指示Spark Streaming Context开始检索Tweets。我们来看几个流行的操作。假设我们正在从套接字读取流文本

    al lines = ssc.socketTextStream(“localhost”, 9999, StorageLevel.MEMORY_AND_DISK_SER)


**map（func）**
目的：通过将此函数应用于DStream中的高分辨率RDDS来创建新的`lines.map(x=>x.tolnt*10).print()`
prompt> nc -lk 9999 
12 
34	
输出：
120 
340

**flatMap（func）**
目的：与map相同，但映射函数可以输出0个以上的项目	

    lines.flatMap(_.split(“ “)).print()

prompt> nc -lk 
9999 
Spark很有趣	
输出：
Spark很有趣

**count（）**
目的：创建包含数据元素数的RDD的DStream	

    lines.flatMap(_.split(“ “)).print()

prompt> NC -lk 
9999 
说
你好
到
火花
输出：
4

**reduce（func）**
目的：与count相同但不是count，通过应用该函数导出该值	

    lines.map(x=>x.toInt).reduce(_+_).print()

prompt> nc -lk 
9999 
1 
3 
5 
7
输出：
16

**countByValue（）**
目的：这与map相同，但映射函数可以输出0个或更多个项目	

    lines.map(x=>x.toInt).reduce(_+_).print()

prompt> nc -lk 9999 
9999 
火花
火花
是
有趣的
乐趣
输出：
（is，1）
（spark，2）
（fun，2）

**reduceByKey（FUNC，[numTasks]）**

    lines.map(x=>x.toInt).reduce(_+_).print()

prompt> nc -lk 9999 
9999 
火花
火花
是
有趣的
乐趣
输出：
（is，1）
（spark，2）
（fun，2）

**reduceByKey（FUNC，[numTasks]）**	

    val words = lines.flatMap(_.split(“ “))
    val wordCounts = words.map(x => (x, 1)).
    reduceByKey(_+_)
    wordCounts.print()

prompt>nc –lk 9999
spark is fun
fun
fun
输出：
（is，1）
（spark，1）
（fun，3）

> 以下示例显示了Apache Spark如何将Spark批处理与Spark Streaming相结合。这是一个一体化技术堆栈的强大功能。在此示例中，我们读取了一个包含品牌名称的文件，并对包含文件中任何品牌名称的流数据集进行过滤。

**transform（func）**
目的：通过
对
DStream中的所有RDD应用RDD-> RDD转换来创建新的DStream。
```
brandNames.txt 
coke 
nike 
sprite 
reebok
val sparkConf = new SparkConf（）. 
setAppName（“NetworkWordCount”）
val sc = new SparkContext（sparkConf）
val ssc = new StreamingContext（sc，Seconds（10））
val lines = ssc。
socketTextStream（“localhost”9999，
StorageLevel.MEMORY_AND_DISK_SER）
val brands = sc.textFile（“/ Users / 
akuntamukkala / temp / brandNames.txt”）
lines.transform（rdd => { 
rdd.intersection（brands）
}）print ）
```
prompt> nc -lk 9999 
msft 
apple 
nike 
sprite 
ibm
输出：
sprite
nike

**updateStateByKey（func）**
目的：创建一个新的DStream，通过应用给定的函数来更新每个键的值。	请参阅Spark Streaming中的StatefulNetworkCount示例。

这有助于计算一个单词发生的总次数的运行聚合


### 公共窗口操作

**window（windowLength，slideInterval）**
目的：返回从源DStream的窗口批量计算的新DStream	

    val win = lines.window（Seconds（30），Seconds（10））; win.foreachRDD（rdd => { 
    rdd.foreach（x => println（x +“”））
    }）

prompt> nc -lk 9999 
10（第0秒）
20（10秒后）
30（20秒后）
40（30秒后）
输出：
10 
10 20 
20 10 30 
20 30 40 (drops 10)

**countByWindow（windowLength，slideInterval）**
目的：返回一个新的滑动窗口中的元素数	

    lines.countByWindow(Seconds(30),Seconds(10)).print()

prompt> nc -lk 9999 
10（第0秒）
20（10秒后）
30（20秒后）
40（30秒后）
输出：
1 
2 
3 
3


有关其他转换运算符，请参考：http : //spark.apache.org/docs/latest/streaming-programming-guide.html#transformations

Spark Streaming具有强大的输出运算符。我们已经在上面的例子中看到foreachRDD（）。其他请参考：http : //spark.apache.org/docs/latest/streaming-programming-guide.html#output-operations




