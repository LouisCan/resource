---
title: google云计算的三大核心技术
date: 2017-07-20 21:03:41
tags: hadoop
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/google1.png
keywords: java,hadoop,google,GFS
description: google云计算的三大核心技术：GFS，MapReduce，BigTable
---

## google云计算的三大核心技术

google云计算的三大核心技术：GFS，MapReduce，BigTable

![](http://osewdhh4j.bkt.clouddn.com/google2.png)

### GFS: 分布式文件系统。

适用于TB级超大文件存储。master节点是文件管理的大脑，负责存储和管理文件与物理块的映射，维护metafile，处理临时文件，调度chunk server等。chunk server是真正存储物理文件块。GFS定位于由廉价服务器构成的超大集群，假定单个服务器存储是不可靠地，易失的，因此GFS强调冗余和备份。每份文件块会同时存储于多个不同的chunk server。上层客户请求文件时，首先与master节点交互，获取相关信息，随后client将直接与相应的某个chunk server通信并获取文件。在开源产品中类似实现有HDFS。

### MapReduce：并行计算的核心技术框架。

使得上层应用软件可以专注于业务逻辑实现，同时利用到分布式并行计算的好处。Map接受和输出属性-值对，使得各节点工作进程可以并行计算它们的属性-值，并输出中间结果；Reduce化简，输入Map处理的中间结果，进行合并运算，最终输出结果文件，返回给上层应用。一个典型案例：编写一个应用对图书馆过去50年的文献，统计最大词频。MapReduce可以做的是，自动分割输入文件集合（任务分解），自动在多节点上克隆运算进程（map进程组和reduce进程组），并分别指派任务，最终映射和化简都完毕后，将处理结果文件返回给原始客户应用 --- 对上层应用很好的屏蔽了并行计算。在开源实现中，对应有Hadoop。

### BigTable：分布式的、稀疏的、多维的、易于扩展的、适用于海量数据的数据库。

它是非关系型数据库，尽管也沿用如表、行等传统概念。他的实质是key-value记录的集合。多维是说key有多个：行、列以及时间。稀疏是因为不同行的列可以完全不同。表、行可以自动分裂从而扩展。相同属性的列组成列族。相比而言，BigTable适合海量存储和非结构化数据（比如网络流量、多媒体、网页、日志等），操作大多数为读取和查询。而传统关系型数据库则易于实现复杂的结构化DML操作。典型案例是网页的存储：以反向URL为key，网页内容以及引用为列，同时网页更新的时间标记作为另一个键。开源实现类似的有HBase，HyperTable等。




