---
title: Hadoop安装
date: 2017-07-03 20:56:32
categories: 大数据
tags: hadoop
thumbnail: http://osewdhh4j.bkt.clouddn.com/hadoop.png
blogexcerpt: Hadoop是一个由Apache基金会所开发的分布式系统基础架构。用户可以在不了解分布式底层细节的情况下，开发分布式程序。充分利用集群的威力进行高速运算和存储。Hadoop的框架最核心的设计就是：HDFS和MapReduce。HDFS为海量的数据提供了存储，则MapReduce为海量的数据提供了计算。
---


**Hadoop**

**Hadoop是一个由Apache基金会所开发的分布式系统基础架构。**

>用户可以在不了解分布式底层细节的情况下，开发分布式程序。充分利用集群的威力进行高速运算和存储。
Hadoop实现了一个分布式文件系统（Hadoop Distributed File System），简称HDFS。

>HDFS有高容错性的特点，并且设计用来部署在低廉的（low-cost）硬件上；而且它提供高吞吐量（high throughput）来访问应用程序的数据，适合那些有着超大数据集（large data set）的应用程序。
HDFS放宽了（relax）POSIX的要求，可以以流的形式访问（streaming access）文件系统中的数据。

>Hadoop的框架最核心的设计就是：HDFS和MapReduce。HDFS为海量的数据提供了存储，则MapReduce为海量的数据提供了计算。

------

**安装hadoop**


**一.下载**
 为了获取Hadoop的发行版，从Apache的某个镜像服务器上下载最近的 稳定发行版
 **http://hadoop.apache.org/releases.html**
 
**二.运行Hadoop集群的准备工作**
解压所下载的Hadoop发行版。编辑 conf/hadoop-env.sh文件，至少需要将JAVA_HOME设置为Java安装根路径。


现在你可以用以下三种支持的模式中的一种启动Hadoop集群：

* 单机模式
* 伪分布式模式
* 完全分布式模式

**1.单机模式的操作方法**

默认情况下，Hadoop被配置成以非分布式模式运行的一个独立Java进程。这对调试非常有帮助。

下面的实例将已解压的 conf 目录拷贝作为输入，查找并显示匹配给定正则表达式的条目。输出写入到指定的output目录。 

```java
$ mkdir input 
$ cp conf/*.xml input 
$ bin/hadoop jar hadoop-*-examples.jar grep input output 'dfs[a-z.]+' 
$ cat output/*
```


**2.伪分布式模式的操作方法**

Hadoop可以在单节点上以所谓的伪分布式模式运行，此时每一个Hadoop守护进程都作为一个独立的Java进程运行。

配置
使用如下的 conf/hadoop-site.xml:

```java
<configuration>
  <property>
    <name>fs.default.name</name>
    <value>localhost:9000</value>
  </property>
  <property>
    <name>mapred.job.tracker</name>
    <value>localhost:9001</value>
  </property>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>
```

免密码ssh设置
现在确认能否不输入口令就用ssh登录localhost:
```
$ ssh localhost
```

如果不输入口令就无法用ssh登陆localhost，执行下面的命令：
```java
$ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa 
$ cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
```

执行
格式化一个新的分布式文件系统：
```
$ bin/hadoop namenode -format
```

启动Hadoop守护进程：
```
$ bin/start-all.sh
```

Hadoop守护进程的日志写入到 ${HADOOP_LOG_DIR} 目录 (默认是 ${HADOOP_HOME}/logs).

浏览NameNode和JobTracker的网络接口，它们的地址默认为：

```
NameNode - http://localhost:50070/
JobTracker - http://localhost:50030/
```
将输入文件拷贝到分布式文件系统：
```
$ bin/hadoop fs -put conf input
```

运行发行版提供的示例程序：
```
$ bin/hadoop jar hadoop-*-examples.jar grep input output 'dfs[a-z.]+'
```

查看输出文件：

将输出文件从分布式文件系统拷贝到本地文件系统查看：
```
$ bin/hadoop fs -get output output 
$ cat output/*
```

或者

在分布式文件系统上查看输出文件：
```
$ bin/hadoop fs -cat output/*
```

完成全部操作后，停止守护进程：
```
$ bin/stop-all.sh
```


**3.完全分布式模式的操作方法**

对Hadoop的配置通过conf/目录下的两个重要配置文件完成：

hadoop-default.xml  只读的默认配置。
hadoop-site.xml  集群特有的配置。


要配置Hadoop集群，你需要设置Hadoop守护进程的运行环境和Hadoop守护进程的运行参数。

Hadoop守护进程指NameNode/DataNode 和JobTracker/TaskTracker。

配置Hadoop守护进程的运行环境

管理员可在conf/hadoop-env.sh脚本内对Hadoop守护进程的运行环境做特别指定。

至少，你得设定JAVA_HOME使之在每一远端节点上都被正确设置。

管理员可以通过配置选项HADOOP_*_OPTS来分别配置各个守护进程。



守护进程  | 配置选项
----------|-----------
NameNode  |	HADOOP_NAMENODE_OPTS
DataNode  |	HADOOP_DATANODE_OPTS
SecondaryNamenode  |	HADOOP_SECONDARYNAMENODE_OPTS
JobTracker  |	HADOOP_JOBTRACKER_OPTS
TaskTracker |	HADOOP_TASKTRACKER_OPTS


例如，配置Namenode时,为了使其能够并行回收垃圾（parallelGC）， 要把下面的代码加入到hadoop-env.sh : 
```
export HADOOP_NAMENODE_OPTS="-XX:+UseParallelGC ${HADOOP_NAMENODE_OPTS}" 
```
其它可定制的常用参数还包括：

* HADOOP_LOG_DIR - 守护进程日志文件的存放目录。如果不存在会被自动创建。
* HADOOP_HEAPSIZE - 最大可用的堆大小，单位为MB。比如，1000MB。 这个参数用于设置hadoop守护进程的堆大小。缺省大小是1000MB。

配置Hadoop守护进程的运行参数
这部分涉及Hadoop集群的重要参数，这些参数在conf/hadoop-site.xml中指定。

![参数：conf/hadoop-site.xml](http://osewdhh4j.bkt.clouddn.com/20170703204511.png)
![大规模集群上运行sort基准测试(benchmark)时使用到的一些非缺省配置](http://osewdhh4j.bkt.clouddn.com/20170703204533.png)


**Slaves**

通常，你选择集群中的一台机器作为NameNode，另外一台不同的机器作为JobTracker。余下的机器即作为DataNode又作为TaskTracker，这些被称之为slaves。

在conf/slaves文件中列出所有slave的主机名或者IP地址，一行一个。

**日志**

Hadoop使用Apache log4j来记录日志，它由Apache Commons Logging框架来实现。编辑conf/log4j.properties文件可以改变Hadoop守护进程的日志配置（日志格式等）。

**历史日志**

作业的历史文件集中存放在hadoop.job.history.location，这个也可以是在分布式文件系统下的路径，其默认值为${HADOOP_LOG_DIR}/history。

jobtracker的web UI上有历史日志的web UI链接。

历史文件在用户指定的目录hadoop.job.history.user.location也会记录一份，这个配置的缺省值为作业的输出目录。
这些文件被存放在指定路径下的“_logs/history/”目录中。
因此，默认情况下日志文件会在“mapred.output.dir/_logs/history/”下。
如果将hadoop.job.history.user.location指定为值none，系统将不再记录此日志。

用户可使用以下命令在指定路径下查看历史日志汇总
```
$ bin/hadoop job -history output-dir 
```
这条命令会显示作业的细节信息，失败和终止的任务细节。 
关于作业的更多细节，比如成功的任务，以及对每个任务的所做的尝试次数等可以用下面的命令查看
```
$ bin/hadoop job -history all output-dir 
```
一但全部必要的配置完成，将这些文件分发到所有机器的HADOOP_CONF_DIR路径下，通常是${HADOOP_HOME}/conf。


**三.Hadoop的机架感知**

HDFS和Map/Reduce的组件是能够感知机架的。

NameNode和JobTracker通过调用管理员配置模块中的APIresolve来获取集群里每个slave的机架id。
该API将slave的DNS名称（或者IP地址）转换成机架id。
使用哪个模块是通过配置项topology.node.switch.mapping.impl来指定的。
模块的默认实现会调用topology.script.file.name配置项指定的一个的脚本/命令。 
如果topology.script.file.name未被设置，对于所有传入的IP地址，模块会返回/default-rack作为机架id。
在Map/Reduce部分还有一个额外的配置项mapred.cache.task.levels，该参数决定cache的级数（在网络拓扑中）。
例如，如果默认值是2，会建立两级的cache－ 一级针对主机（主机 -> 任务的映射）另一级针对机架（机架 -> 任务的映射）。

**四.启动Hadoop**
启动Hadoop集群需要启动HDFS集群和Map/Reduce集群。

格式化一个新的分布式文件系统：
```
$ bin/hadoop namenode -format
```

在分配的NameNode上，运行下面的命令启动HDFS：
```
$ bin/start-dfs.sh
```

bin/start-dfs.sh脚本会参照NameNode上${HADOOP_CONF_DIR}/slaves文件的内容，在所有列出的slave上启动DataNode守护进程。

在分配的JobTracker上，运行下面的命令启动Map/Reduce：
```
$ bin/start-mapred.sh
```

bin/start-mapred.sh脚本会参照JobTracker上${HADOOP_CONF_DIR}/slaves文件的内容，在所有列出的slave上启动TaskTracker守护进程。

**五.停止Hadoop**
在分配的NameNode上，执行下面的命令停止HDFS：
```
$ bin/stop-dfs.sh
```

bin/stop-dfs.sh脚本会参照NameNode上${HADOOP_CONF_DIR}/slaves文件的内容，在所有列出的slave上停止DataNode守护进程。

在分配的JobTracker上，运行下面的命令停止Map/Reduce：
```
$ bin/stop-mapred.sh 
```
bin/stop-mapred.sh脚本会参照JobTracker上${HADOOP_CONF_DIR}/slaves文件的内容，在所有列出的slave上停止TaskTracker守护进程。








