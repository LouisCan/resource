---
title: Hadoop - HDFS操作
date: 2017-07-17 19:51:18
tags: hadoop
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/HDFS%E6%93%8D%E4%BD%9C.png
keywords: 大数据,hadoop,HDFS操作
description: 启动HDFS,最初，您必须格式化配置的`HDFS文件系统`，打开`namenode`（HDFS服务器），然后执行以下命令。
---

## Hadoop - HDFS操作

![](http://osewdhh4j.bkt.clouddn.com/HDFS%E6%93%8D%E4%BD%9C.png)

### 启动HDFS
最初，您必须格式化配置的`HDFS文件系统`，打开`namenode`（HDFS服务器），然后执行以下命令。

    $ hadoop namenode -format 

格式化HDFS后，启动分布式文件系统。以下命令将启动namenode以及数据节点作为集群。

    $ start-dfs.sh 

### 列出HDFS中的文件
在服务器中加载信息后，我们可以使用'ls'找到目录中的文件列表，文件的状态。下面给出了可以传递给目录或文件名作为参数的ls的语法。

    $ $HADOOP_HOME/bin/hadoop fs -ls <args>

### 将数据插入HDFS
假设我们在本地系统中的文件名为file.txt中的数据，该文件应该保存在hdfs文件系统中。按照以下步骤在Hadoop文件系统中插入所需的文件。

**步骤1**
你必须创建一个输入目录。

    $ $HADOOP_HOME/bin/hadoop fs -mkdir /user/input 

**第2步**
使用put命令将数据文件从本地系统传输到Hadoop文件系统。

    $ $HADOOP_HOME/bin/hadoop fs -put /home/file.txt /user/input 

**步骤3**
您可以使用ls命令验证文件。

    $ $HADOOP_HOME/bin/hadoop fs -ls /user/input 

### 从HDFS检索数据
假设我们在HDFS中有一个名为outfile的文件。以下是从Hadoop文件系统中检索所需文件的简单演示。

**步骤1**
最初，使用cat命令查看HDFS中的数据。

    $ $HADOOP_HOME/bin/hadoop fs -cat /user/output/outfile 

**第2步**
使用get命令将文件从HDFS获取到本地文件系统。

    $ $HADOOP_HOME/bin/hadoop fs -get /user/output/ /home/hadoop_tp/ 

### 关闭HDFS
您可以使用以下命令关闭HDFS。

    $ stop-dfs.sh 


