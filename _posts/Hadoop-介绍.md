---
title: Hadoop - 介绍
date: 2017-07-17 19:17:46
tags: hadoop
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/hadoop%E4%BB%8B%E7%BB%8D.png
keywords: 大数据,hadoop,Hadoop介绍
description: Hadoop是一个使用java编写的Apache开放源代码框架，它允许使用简单的编程模型跨大型计算机的大型数据集进行分布式处理。Hadoop框架工作的应用程序可以在跨计算机群集提供分布式存储和计算的环境中工作。Hadoop旨在从单一服务器扩展到数千台机器，每台机器都提供本地计算和存储。
---


## Hadoop - 介绍

> Hadoop是一个使用java编写的Apache开放源代码框架，它允许使用简单的编程模型跨大型计算机的大型数据集进行分布式处理。Hadoop框架工作的应用程序可以在跨计算机群集提供分布式存储和计算的环境中工作。Hadoop旨在从单一服务器扩展到数千台机器，每台机器都提供本地计算和存储。

![](http://osewdhh4j.bkt.clouddn.com/hadoop%E4%BB%8B%E7%BB%8D.png)

### Hadoop架构
Hadoop框架包括以下四个模块：

 - **Hadoop Common**：这些是其他Hadoop模块所需的Java库和实用程序。这些库提供文件系统和操作系统级抽象，并包含启动Hadoop所需的必要Java文件和脚本。
 - **Hadoop YARN**：这是作业调度和集群资源管理的框架。
 - **Hadoop分布式文件系统（HDFS）**：提供对应用程序数据的高吞吐量访问的分布式文件系统。
 - **Hadoop MapReduce**： 这是基于YARN的大型数据集并行处理系统。

我们可以使用下图来描述Hadoop框架中可用的这四个组件。
![](http://osewdhh4j.bkt.clouddn.com/hadoop_architecture.jpg)

自2012年以来，术语“Hadoop”通常不仅指向上述基本模块，而且还指向可以安装在Hadoop之上或之外的其他软件包，例如Apache Pig，Apache Hive，Apache HBase，Apache火花等

### MapReduce
`Hadoop MapReduce`是一个用于轻松编写应用程序的软件框架，它以可靠，容错的方式在大型集群（数千个节点）上处理大量数据并行处理商品硬件。

术语MapReduce实际上是指Hadoop程序执行的以下两个不同的任务：

 - **Map Task**：这是第一个任务，它接收输入数据并将其转换成一组数据，其中单个元素分解为元组（键/值对）。
 - **Reduce Task**：此任务将地图任务的输出作为输入，并将这些数据元组合并为较小的一组元组。reduce任务总是在map任务之后执行。

通常输入和输出都存储在文件系统中。该框架负责调度任务，监视它们并重新执行失败的任务。

`MapReduce框架`由单个主**JobTracker**和每个群集节点的一个从属**TaskTracker**组成。主管负责资源管理，跟踪资源消耗/可用性，并对从站上的作业组件任务进行调度，监控和重新执行故障任务。从站TaskTracker按照主机的指示执行任务，并定期向主设备提供任务状态信息。

JobTracker是Hadoop MapReduce服务的单点故障，这意味着如果JobTracker关闭，则所有正在运行的作业都将停止。

### Hadoop分布式文件系统
Hadoop可以直接与任何可安装的分布式文件系统（如本地FS，HFTP FS，S3 FS等）工作，但Hadoop使用的最常见的文件系统是Hadoop分布式文件系统（HDFS）。

Hadoop分布式文件系统（HDFS）基于Google文件系统（GFS），并提供一个分布式文件系统，旨在以可靠，容错的方式在大型计算机（数千台计算机）上运行小型计算机。

HDFS使用主/从架构，其中主机由管理文件系统元数据的单个`NameNode`和存储实际数据的一个或多个从属数据**节点组成**。

HDFS命名空间中的文件被分成几个块，这些块被存储在一组DataNodes中。NameNode确定块到DataNodes的映射。DataNodes负责文件系统的读写操作。他们还根据NameNode给出的指令来处理块创建，删除和复制。

HDFS提供了像任何其他文件系统一样的shell，并且可以使用命令列表与文件系统进行交互。这些shell命令将在一个单独的章节中以及适当的示例进行介绍。

### Hadoop如何工作？
**阶段1**
用户/应用程序可以通过指定以下项目向Hadoop（hadoop作业客户端）提交所需的进程：

 - 分布式文件系统中输入和输出文件的位置。
 - java类以jar文件的形式包含了map和reduce功能的实现。
 - 通过设置作业特定的不同参数来进行作业配置。

**阶段2**
然后，Hadoop作业客户端将作业（jar /可执行文件等）和配置提交给JobTracker，JobTracker负责将软件/配置分发到从站，调度任务和监视它们，向作业客户端提供状态和诊断信息。

**阶段3**
不同节点上的TaskTrackers根据MapReduce实现执行任务，并将reduce函数的输出存储到文件系统的输出文件中。

### Hadoop的优点

 - Hadoop框架允许用户快速编写和测试分布式系统。它是高效的，它自动分配数据并在机器上工作，反过来利用CPU核心的底层并行性。
 - Hadoop不依赖硬件提供容错和高可用性（FTHA），而是Hadoop库本身被设计为检测和处理应用层的故障。
 - 服务器可以动态添加或从集群中删除，Hadoop继续运行而不会中断。
 - Hadoop的另一大优点是，除了是开放源码，它是所有平台兼容的，因为它是基于Java的。



