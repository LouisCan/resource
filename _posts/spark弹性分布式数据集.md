---
title: spark弹性分布式数据集
date: 2017-07-23 14:59:18
tags: Spark
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/spark.png
keywords: hadoop,spark,Apache Spark
description: apache spark的核心概念是弹性分布式数据集（RDD）。它是一个不可变的分布式数据集合，它在集群中的机器之间进行分区。它有助于两种类型的操作：转换和动作。转换是在RDD上产生另一个RDD的操作，如filter（），map（）或union（）。触发计算的Anactionisanoperationsuchascount（），first（），take（n）或collect（）返回一个值返回给Master，或写入稳定的存储系统。转型被懒惰地评估，因为直到行动保证才能运行。Spark Master / Driver记住应用于RDD的转换，所以如果一个分区丢失（比如从机失效），该分区可以很容易地在集群中的其他机器上重构。这就是为什么叫“弹性”。
---

## 弹性分布式数据集

apache spark的核心概念是弹性分布式数据集（RDD）。它是一个不可变的分布式数据集合，它在集群中的机器之间进行分区。它有助于两种类型的操作：转换和动作。转换是在RDD上产生另一个RDD的操作，如filter（），map（）或union（）。触发计算的Anactionisanoperationsuchascount（），first（），take（n）或collect（）返回一个值返回给Master，或写入稳定的存储系统。转型被懒惰地评估，因为直到行动保证才能运行。Spark Master / Driver记住应用于RDD的转换，所以如果一个分区丢失（比如从机失效），该分区可以很容易地在集群中的其他机器上重构。这就是为什么叫“弹性”。

图9显示了如何懒惰地评估变换：
![](http://osewdhh4j.bkt.clouddn.com/20170723144005.png)

让我们通过使用以下示例来理解这一点：说我们想在文本文件中找到5个最常用的单词。可能的解决方案如图10所示。
![](http://osewdhh4j.bkt.clouddn.com/20170723144021.png)

以下代码片段显示了如何使用Spark Scala REPL shell在Scala中执行此操作。

    scala> val hamlet = sc.textFile(“/Users/akuntamukkala/temp/gutenburg.txt”)
    hamlet: org.apache.spark.rdd.RDD[String] = MappedRDD[1] at textFile at <console>:12


在上面的命令中，我们读取文件并创建一个RDD的字符串。每个条目表示文件中的一行。

    scala> val topWordCount = hamlet.flatMap(str=>str.split(“ “)). filter(!_.isEmpty).map(word=>(word,1)).reduceByKey(_+_).map{case (word, count) => (count, word)}.sortByKey(false)
    topWordCount: org.apache.spark.rdd.RDD[(Int, String)] = MapPartitionsRDD[10] at sortByKey at <console>:14

 1. 以上命令显示了使用简洁的Scala
    API链接变换和操作的简单性。我们分割每行`lineintowordsusinghamlet.flatMap（str => str.split（“”））。`
 2. 可能会有多个空格分开的单词，这会导致空字符串的单词。所以我们需要使用`filter（！_.isEmpty）`来过滤这些空字。
 3. 我们将每个单词映射成一个键值对：`map（word =>（word，1））`。
 4. 为了汇总计数，我们需要使用`reduceByKey（_ + _）`调用一个缩减步骤。_ + _是一个快捷键，用于每个键添加值。
 5. 我们有言论和各自的意思，但我们需要按数量排序。在Apache Spark中，我们只能按键而不是值进行排序。所以，我们需要使用`map
    {case（word，count）=>（count，word）}将（word，count）反转成（count，word）。`
 6. 我们想要最常用的五个词，所以我们需要使用sortByKey（false）按降序对计数进行排序。

```
scala> topWordCount.take(5).foreach(x=>println(x))
(1044,the)
(730,and)
(679,of)
(648,to)
(511,I)
```
上述命令contains.take（5）（一个触发计算的操作操作），并在输入文本文件中打印前十个最常用的单词：/Users/akuntamukkala/temp/gutenburg.txt。

同样可以在Python shell中完成。

可以使用有用的操作来跟踪RDD谱系：**toDebugString**

```java
scala> topWordCount.toDebugString
res8: String = MapPartitionsRDD[19] at sortByKey at <console>:14 
        ShuffledRDD[18] at sortByKey at <console>:14
        MappedRDD[17] at map at <console>:14
            MapPartitionsRDD[16] at reduceByKey at <console>:14
                ShuffledRDD[15] at reduceByKey at <console>:14 
                    MapPartitionsRDD[14] at reduceByKey at <console>:14
                        MappedRDD[13] at map at <console>:14 
                            FilteredRDD[12] at filter at <console>:14
                                FlatMappedRDD[11] at flatMap at <console>:14 
                                    MappedRDD[1] at textFile at <console>:12
                                        HadoopRDD[0] at textFile at <console>:12

```

### 常用变换：

转型与目的	| 示例和结果
-----|------
filter（func）目的：通过选择func返回true的数据元素来创建新RDD	|	scala> val rdd = sc.parallelize(List(“ABC”,”BCD”,”DEF”)) scala> val filtered = rdd.filter(_.contains(“C”)) scala> filtered.collect() **Result:** Array[String] = Array(ABC, BCD)		
map（func）目的：通过在每个数据元素上应用func来返回新的RDD	|	scala> val rdd=sc.parallelize(List(1,2,3,4,5)) scala> val times2 = rdd.map(_*2) scala> times2.collect() **Result:**	Array[Int] = Array(2, 4, 6, 8, 10)	
flatMap（func）目的：与map类似，但func会返回一个Seq而不是一个值。例如，将句子映射到单词的Seq	|	scala> val rdd=sc.parallelize(List(“Spark is awesome”,”It is fun”)) scala> val fm=rdd.flatMap(str=>str.split(“ “)) scala> fm.collect() **Result:**	Array[String] = Array(Spark, is, awesome, It, is, fun)	
reduceByKey（func，[numTasks]）目的：使用函数聚合键的值。“numTasks”是指定reduce任务数的可选参数	|	scala> val word1=fm.map(word=>(word,1)) scala> val wrdCnt=word1.reduceByKey(_+_) scala> wrdCnt.collect() **Result:** Array[(String, Int)] = Array((is,2), (It,1), (awesome,1), (Spark,1), (fun,1))		
groupByKey（[numTasks]）目的：将（K，V）转换为（K，Iterable <V>）	|	scala> val cntWrd = wrdCnt.map{case (word, count) => (count, word)} scala> cntWrd.groupByKey().collect() **Result:** Array[(Int, Iterable[String])] = Array((1,ArrayBuffer(It, awesome, Spark, fun)), (2,ArrayBuffer(is)))		
distinct（[numTasks]）目的：从RDD中删除重复项	|	scala> fm.distinct().collect() **Result:**	Array[String] = Array(is, It, awesome, Spark, fun)	

### 常用设置操作

**union（）**
目的：包含源RDD和参数的所有元素的新RDD。	

    Scala> val rdd1 = sc.parallelize（List（'A'，'B'））
    scala> val rdd2 = sc.parallelize（List（'B'，'C'））
    scala> rdd1.union（rdd2）.collect （）

结果：

    Array [Char] = Array（A，B，B，C）

**intersection（）**
目的：新的RDD只包含来自源RDD和参数的公共元素。	

    Scala> rdd1.intersection（rdd2）.collect（）

结果：

    Array [Char] = Array（B）

**cartesian()**
目的：来自源RDD和参数的所有元素的新RDD交叉乘积	

    Scala> rdd1.cartesian（rdd2）.collect（）

结果：

    Array [（Char，Char）] = Array（（A，B），（A，C），（B，B），（B，C））

**subtract（）**
目的：通过删除源RDD中与参数相同的数据元素创建的新RDD	

    scala> rdd1.subtract（rdd2）.collect（）

结果：

    Array [Char] = Array（A）

**join（RDD，[numTasks]）**
目的：当在（K，V）和（K，W）上调用时，此操作创建一个新的RDD（K，（V，W））	

    scala> val personFruit = sc.parallelize(Seq((“Andy”, “Apple”), (“Bob”, “Banana”), (“Charlie”, “Cherry”), (“Andy”,”Apricot”)))
    scala> val personSE = sc.parallelize(Seq((“Andy”, “Google”), (“Bob”, “Bing”), (“Charlie”, “Yahoo”), (“Bob”,”AltaVista”)))
    scala> personFruit.join(personSE).collect()

结果：

    Array[(String, (String, String))] = Array((Andy,(Apple,Google)), (Andy,(Apricot,Google)), (Charlie,(Cherry,Yahoo)), (Bob,(Banana,Bing)), (Bob,(Banana,AltaVista)))

**cogroup（RDD，[numTasks]）**
目的：将（K，V）转换为（K，Iterable <V>）	

    scala> personFruit.cogroup（personSe）.collect（）

结果：

    Array [（String，（Iterable [String]，Iterable [String]）]] = Array（（Andy，（ArrayBuffer（Apple，Apricot）），ArrayBuffer ），（Charlie，（ArrayBuffer（Cherry），ArrayBuffer（Yahoo））），（Bob，（ArrayBuffer（Banana）），ArrayBuffer（Bing，AltaVista）））


有关转换的更多详细信息，请参阅：
`http : //spark.apache.org/docs/latest/programming-guide.html#transformations`

### 常用的动作

**count（）**目的：获取RDD中的数据元素数	
`scala> val rdd = sc.parallelize（list（'A'，'B'，'c'））scala> rdd.count（）`
结果：

    long = 3

**collect（）**目的：将RDD中的所有数据元素作为数组	
`scala> val rdd = sc.parallelize（list（'A'，'B'，'c'））scala> rdd.collect（）`
结果：

    Array [char] = Array（A，B，c）

**reduce（func）**目的：使用该函数聚合RDD中的数据元素，该函数需要两个参数并返回一个	
`scala> val rdd = sc.parallelize（list（1,2,3,4））scala> rdd.reduce（_ + _）`
结果：

    Int = 10

**take（n）**目的：：在RDD中获取前n个数据元素。由驱动程序计算。	`Scala> val rdd = sc.parallelize（list（1,2,3,4））scala> rdd.take（2）`
Result：

    Array [Int] = Array（1,2）

**foreach（func）**目的：为RDD中的每个数据元素执行功能。通常用于更新累加器（稍后讨论）或与外部系统交互。

    Scala> val rdd = sc.parallelize（list（1,2,3,4））scala> rdd.foreach（x => println（“％s * 10 =％s”。format（x，x * 10）） ）

结果：

    1 * 10 = 10 4 * 10 = 40 3 * 10 = 30 2 * 10 = 20

**first（）**目的：检索RDD中的第一个数据元素。（1）	

    scala> val rdd = sc.parallelize（list（1,2,3,4））scala> rdd.first（）

结果：

    Int = 1

**saveAsTextFile（path）**目的：将RDD的内容写入文本文件或一组文本文件到本地文件系统/ HDFS	

    scala> val hamlet = sc.textFile（“/ users / akuntamukkala / temp / gutenburg.txt”）scala> hamlet.filter（_。contains（“Shakespeare”））。saveAsTextFile（“/ users / akuntamukkala / temp / filtered”）

结果：

    akuntamukkala @ localhost〜/ temp / filtered $ ls _SUCCESS part-00000 part-00001

有关更详细的操作列表，请参阅：

http://spark.apache.org/docs/latest/programming-guide.html#actions







