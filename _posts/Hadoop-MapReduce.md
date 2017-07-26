---
title: Hadoop - MapReduce
date: 2017-07-17 20:32:37
tags: hadoop
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/MapReduce.png
keywords: 大数据,hadoop,MapReduce
description: MapReduce是基于java的分布式计算的处理技术和程序模型。MapReduce算法包含两个重要任务，即Map和Reduce。地图获取一组数据并将其转换为另一组数据，其中单个元素分解为元组（键/值对）。其次，减少任务，将地图的输出作为输入，并将这些数据元组合并成一组较小的元组。按照MapReduce名称的顺序，reduce任务总是在地图作业之后执行。
---

## Hadoop - MapReduce

MapReduce是一个框架，我们可以编写应用程序，以可靠的方式并行处理大量商品硬件的大量数据。

![](http://osewdhh4j.bkt.clouddn.com/MapReduce.png)

### 什么是MapReduce？
`MapReduce`是基于java的分布式计算的处理技术和程序模型。MapReduce算法包含两个重要任务，即Map和Reduce。地图获取一组数据并将其转换为另一组数据，其中单个元素分解为元组（键/值对）。其次，减少任务，将地图的输出作为输入，并将这些数据元组合并成一组较小的元组。按照MapReduce名称的顺序，reduce任务总是在地图作业之后执行。

MapReduce的主要优点是可以轻松地在多个计算节点上进行数据处理。在MapReduce模型下，数据处理原语称为映射器和还原器。将数据处理应用程序分解成映射器和还原器有时是不重要的。但是，一旦我们在MapReduce表单中编写应用程序，将应用程序扩展到集群中数以百计，甚至数万台计算机上的计算机只是配置更改。这种简单的可扩展性是吸引了许多程序员使用MapReduce模型。

### 算法

 - 一般MapReduce范例通常是将计算机发送到数据所在的地方！
 - MapReduce程序分三个阶段执行，分别是地图阶段，洗牌阶段和减少阶段。

**地图阶段**：地图或映射器的作业是处理输入数据。通常，输入数据是以文件或目录的形式存储在Hadoop文件系统（HDFS）中。输入文件逐行传递给映射器函数。映射器处理数据并创建几个小块数据。

**减少阶段**：这个阶段是洗牌阶段和减少阶段的组合。Reducer的工作是处理来自映射器的数据。在处理之后，它产生一组新的输出，将其存储在HDFS中。

 - 在MapReduce作业期间，Hadoop将Map和Reduce任务发送到集群中的相应服务器。
 - 该框架管理数据传递的所有细节，如发布任务，验证任务完成，以及在节点之间围绕集群复制数据。
 - 大多数计算发生在具有本地磁盘上的数据的节点上，从而减少网络流量。
 - 在完成给定任务后，集群收集并减少数据以形成适当的结果，并将其发送回Hadoop服务器。

![](http://osewdhh4j.bkt.clouddn.com/mapreduce_algorithm.jpg)

### MapReduce算法
输入和输出（Java Perspective）
MapReduce框架在`<key，value>`对上运行，即框架将作业的输入视为一组`<key，value>`对，并生成一组`<key，value>`对作为作业的输出，可以想象不同的类型。

关键和值类应该由框架以序列化的方式，因此需要实现Writable接口。另外，关键类必须实现Writable-Comparable接口，以便于框架进行排序。MapReduce作业的输入和输出类型：`（输入）<k1，v1> - > map - > <k2，v2> - > reduce - > <k3，v3>（输出）`。

 0 |输入|	产量
 ----|----|---
地图 |	`<k1​​，v1>`	|列表`（<k2，v2>）`
减少|`<k2，list（v2）>`|	列表（`<k3，v3>`）

###   术语

 - **PayLoad** - 应用程序实现Map和Reduce功能，并构成工作的核心。
 - **Mapper** - Mapper将输入键/值对映射到一组中间键/值对。
 - **NamedNode** - 管理Hadoop分布式文件系统（HDFS）的节点。
 - **DataNode** - 数据在进行任何处理之前提前呈现的节点。
 - **MasterNode** - JobTracker运行的节点，并接收来自客户端的作业请求。
 - **SlaveNode** - 运行Map和Reduce程序的节点。
 - **JobTracker** - 调度作业并跟踪分配作业到任务跟踪器。
 - **Task Tracker** - 跟踪任务并向JobTracker报告状态。
 - **Job**  - 程序是跨数据集执行映射器和还原器。
 - **Task**  - 在一片数据上执行Mapper或Reducer。
 - **Task Attempt** - 尝试在SlaveNode上执行任务的特定实例。

### 示例场景
以下是有关组织的电力消耗的数据。它包含每年的电力消耗和各年的年平均值。

![](http://osewdhh4j.bkt.clouddn.com/20170717201736.png)
如果以上数据作为输入，我们必须编写应用程序来处理它，并产生结果，例如查找最大使用年份，最小使用年数等。这是一个有限数量记录的程序员的过程。他们只需编写逻辑来产生所需的输出，并将数据传递给写入的应用程序。

但是，考虑到自形成以来，特定国家所有大型工业的电力消耗量的数据。

当我们编写应用程序来处理这样的批量数据时，

 - 他们需要很多时间来执行。
 - 当我们从数据源到网络服务器等移动数据时，会有一个沉重的网络流量。 为了解决这些问题，我们提供了MapReduce框架。

### 输入数据
上述数据保存为**sample.txt**并作为输入给出。输入文件如下图所示。

    1979   23   23   2   43   24   25   26   26   26   26   25   26  25 
    1980   26   27   28  28   28   30   31   31   31   30   30   30  29 
    1981   31   32   32  32   33   34   35   36   36   34   34   34  34 
    1984   39   38   39  39   39   41   42   43   40   39   38   38  40 
    1985   38   39   39  39   39   41   41   41   00   40   39   39  45 

示例程序
以下是使用MapReduce框架的样本数据的程序。
```java
package hadoop; 

import java.util.*; 

import java.io.IOException; 
import java.io.IOException; 

import org.apache.hadoop.fs.Path; 
import org.apache.hadoop.conf.*; 
import org.apache.hadoop.io.*; 
import org.apache.hadoop.mapred.*; 
import org.apache.hadoop.util.*; 

public class ProcessUnits 
{ 
   //Mapper class 
   public static class E_EMapper extends MapReduceBase implements 
   Mapper<LongWritable ,/*Input key Type */ 
   Text,                /*Input value Type*/ 
   Text,                /*Output key Type*/ 
   IntWritable>        /*Output value Type*/ 
   { 
      
      //Map function 
      public void map(LongWritable key, Text value, 
      OutputCollector<Text, IntWritable> output,   
      Reporter reporter) throws IOException 
      { 
         String line = value.toString(); 
         String lasttoken = null; 
         StringTokenizer s = new StringTokenizer(line,"\t"); 
         String year = s.nextToken(); 
         
         while(s.hasMoreTokens())
            {
               lasttoken=s.nextToken();
            } 
            
         int avgprice = Integer.parseInt(lasttoken); 
         output.collect(new Text(year), new IntWritable(avgprice)); 
      } 
   } 
   
   
   //Reducer class 
   public static class E_EReduce extends MapReduceBase implements 
   Reducer< Text, IntWritable, Text, IntWritable > 
   {  
   
      //Reduce function 
      public void reduce( Text key, Iterator <IntWritable> values, 
         OutputCollector<Text, IntWritable> output, Reporter reporter) throws IOException 
         { 
            int maxavg=30; 
            int val=Integer.MIN_VALUE; 
            
            while (values.hasNext()) 
            { 
               if((val=values.next().get())>maxavg) 
               { 
                  output.collect(key, new IntWritable(val)); 
               } 
            } 
 
         } 
   }  
   
   
   //Main function 
   public static void main(String args[])throws Exception 
   { 
      JobConf conf = new JobConf(ProcessUnits.class); 
      
      conf.setJobName("max_eletricityunits"); 
      conf.setOutputKeyClass(Text.class);
      conf.setOutputValueClass(IntWritable.class); 
      conf.setMapperClass(E_EMapper.class); 
      conf.setCombinerClass(E_EReduce.class); 
      conf.setReducerClass(E_EReduce.class); 
      conf.setInputFormat(TextInputFormat.class); 
      conf.setOutputFormat(TextOutputFormat.class); 
      
      FileInputFormat.setInputPaths(conf, new Path(args[0])); 
      FileOutputFormat.setOutputPath(conf, new Path(args[1])); 
      
      JobClient.runJob(conf); 
   } 
} 
```
将上述程序另存为`ProcessUnits.java`。下面说明程序的编译和执行。

### 流程单位编制和执行计划
让我们假设我们在Hadoop用户的主目录（例如/ home / hadoop）中。

按照以下步骤编译并执行上述程序。

**步骤1**
以下命令是创建一个目录来存储编译的java类。

    $ mkdir units 

**第2步**
下载Hadoop-core-1.2.1.jar，用于编译和执行MapReduce程序。访问以下链接`http://mvnrepository.com/artifact/org.apache.hadoop/hadoop-core/1.2.1`下载jar。让我们假设下载的文件夹是/ home / hadoop /。

**步骤3**
以下命令用于编译`ProcessUnits.java`程序并为程序创建一个jar。

    $ javac -classpath hadoop-core-1.2.1.jar -d units ProcessUnits.java 
    $ jar -cvf units.jar -C units/ . 

**步骤4**
以下命令用于在HDFS中创建输入目录。

    $HADOOP_HOME/bin/hadoop fs -mkdir input_dir 

**步骤5**
以下命令用于在HDFS的输入目录中复制名为sample.txt的输入文件。

    $HADOOP_HOME/bin/hadoop fs -put /home/hadoop/sample.txt input_dir 

**步骤6**
以下命令用于验证输入目录中的文件。

    $HADOOP_HOME/bin/hadoop fs -ls input_dir/ 

**步骤7**
以下命令用于通过从输入目录中获取输入文件来运行Eleunit_max应用程序。

    $HADOOP_HOME/bin/hadoop jar units.jar hadoop.ProcessUnits input_dir output_dir 

等待一段时间，直到文件被执行。执行后，如下图所示，输出将包含输入分割数，Map任务数，reducer任务数等。
```
INFO mapreduce.Job: Job job_1414748220717_0002 
completed successfully 
14/10/31 06:02:52 
INFO mapreduce.Job: Counters: 49 
File System Counters 
 
FILE: Number of bytes read=61 
FILE: Number of bytes written=279400 
FILE: Number of read operations=0 
FILE: Number of large read operations=0   
FILE: Number of write operations=0 
HDFS: Number of bytes read=546 
HDFS: Number of bytes written=40 
HDFS: Number of read operations=9 
HDFS: Number of large read operations=0 
HDFS: Number of write operations=2 Job Counters 


   Launched map tasks=2  
   Launched reduce tasks=1 
   Data-local map tasks=2  
   Total time spent by all maps in occupied slots (ms)=146137 
   Total time spent by all reduces in occupied slots (ms)=441   
   Total time spent by all map tasks (ms)=14613 
   Total time spent by all reduce tasks (ms)=44120 
   Total vcore-seconds taken by all map tasks=146137 
   
   Total vcore-seconds taken by all reduce tasks=44120 
   Total megabyte-seconds taken by all map tasks=149644288 
   Total megabyte-seconds taken by all reduce tasks=45178880 
   
Map-Reduce Framework 
 
Map input records=5  
   Map output records=5   
   Map output bytes=45  
   Map output materialized bytes=67  
   Input split bytes=208 
   Combine input records=5  
   Combine output records=5 
   Reduce input groups=5  
   Reduce shuffle bytes=6  
   Reduce input records=5  
   Reduce output records=5  
   Spilled Records=10  
   Shuffled Maps =2  
   Failed Shuffles=0  
   Merged Map outputs=2  
   GC time elapsed (ms)=948  
   CPU time spent (ms)=5160  
   Physical memory (bytes) snapshot=47749120  
   Virtual memory (bytes) snapshot=2899349504  
   Total committed heap usage (bytes)=277684224
     
File Output Format Counters 
 
   Bytes Written=40 

```
**步骤8**
以下命令用于验证输出文件夹中的结果文件。

    $HADOOP_HOME/bin/hadoop fs -ls output_dir/ 

**步骤9**
以下命令用于查看Part-00000文件中的输出。此文件由HDFS生成。

    $HADOOP_HOME/bin/hadoop fs -cat output_dir/part-00000 

以下是MapReduce程序生成的输出。

    1981    34 
    1984    40 
    1985    45 

**步骤10**
以下命令用于将输出文件夹从HDFS复制到本地文件系统进行分析。

    $HADOOP_HOME/bin/hadoop fs -cat output_dir/part-00000/bin/hadoop dfs get output_dir /home/hadoop 

### 重要命令
所有Hadoop命令都由$ HADOOP_HOME / bin / hadoop命令调用。没有任何参数运行Hadoop脚本会打印所有命令的描述。

    用法：hadoop [--config confdir]命令

下表列出了可用的选项及其说明。

选项	|描述
----|----
namenode -format	|	格式化DFS文件系统。
secondarynamenode	|	运行DFS辅助节点。
namenode	|	运行DFS namenode。
datanode	|	运行DFS datanode。
dfsadmin	|	运行DFS管理客户机。
mradmin	|	运行Map-Reduce管理客户端。
fsck	|	运行DFS文件系统检查实用程序。
fs	|	运行通用文件系统用户客户机。
balancer	|	运行集群平衡实用程序。
oiv	|	将离线fsimage查看器应用到fsimage。
fetchdt	|	从NameNode获取一个委托令牌。
jobtracker	|	运行MapReduce作业跟踪器节点。
pipes	|	运行管道工作。
tasktracker	|	运行MapReduce任务跟踪器节点。
historyserver	|	将作业历史记录服务器作为独立守护程序运行。
job	|	操纵MapReduce作业。
queue	|	获取有关JobQueues的信息。
version	|	打印版本。
jar <jar>	|	运行一个jar文件。
distcp <srcurl> <desturl>	|	递归复制文件或目录。
distcp2 <srcurl> <desturl>	|	DistCp版本2。
archive -archiveName NAME -p	|	创建hadoop存档。
<parent path> <src>* <dest>	|	
classpath	|	打印获取Hadoop jar和所需库所需的类路径。
daemonlog	|	获取/设置每个守护进程的日志级别



### 如何与MapReduce工作进行交互
用法：hadoop作业[GENERIC_OPTIONS]

以下是Hadoop作业中可用的通用选项。

GENERIC_OPTIONS	| 描述
----|----
-submit <job-file>	|	提交作业
-status <job-id>	|	打印地图并减少完成百分比和所有作业计数器。
-counter <job-id> <group-name> <countername>	|	打印计数器值。
-kill <job-id>	|	杀死了这个工作。
-events <job-id> <fromevent-#> <#-of-events>	|	打印给定范围的jobtracker收到的事件的详细信息。
-history [all] <jobOutputDir> - history < jobOutputDir>	|	打印作业详细信息，失败并杀死提示详细信息。可以通过指定[all]选项来查看有关作业的更多详细信息，例如为每个任务执行的成功任务和任务尝试。
-list[all]	|	显示所有作业。-list仅显示尚未完成的作业。
-kill-task <task-id>	|	杀死任务。杀死的任务不计入失败的尝试。
-fail-task <task-id>	|	无法完成任务 失败的任务会计入失败的尝试次数。
-set-priority <job-id> <priority>	|	更改作业的优先级。允许的优先级值为VERY_HIGH，HIGH，NORMAL，LOW，VERY_LOW




### 查看工作状态

    $ $HADOOP_HOME/bin/hadoop job -status <JOB-ID> 
    e.g. 
    $ $HADOOP_HOME/bin/hadoop job -status job_201310191043_0004 

### 查看工作输出历史

    $ $HADOOP_HOME/bin/hadoop job -history <DIR-NAME> 
    e.g. 
    $ $HADOOP_HOME/bin/hadoop job -history /user/expert/output 

### 杀死工作

    $ $HADOOP_HOME/bin/hadoop job -kill <JOB-ID> 
    e.g. 
    $ $HADOOP_HOME/bin/hadoop job -kill job_201310191043_0004 



