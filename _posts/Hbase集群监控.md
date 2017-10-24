---
title: Hbase集群监控
date: 2017-08-10 12:18:02
tags: hbase
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170802094224.png
keywords: hbase,hbase Jmx,HBASE集群监控
description: 监控每个regionServer的总请求数，readRequestsCount，writeRequestCount，region分裂，region合并，Store
---

## Hbase集群监控

### Hbase Jmx监控
监控每个regionServer的总请求数，readRequestsCount，writeRequestCount，region分裂，region合并，Store

**数据来源：**
```
/jmx?qry=Hadoop:service=HBase,name=RegionServer,sub=Server
```

**设计：**

> - 1.定时调度Hbase Jmx去捞取数据，数据存放在Mysql，最新的一条数据存放到redis缓存中查（设置过期时间5分钟）并插入数据库中(定时每五分钟调度一次)
> - 2.每次获取Jmx数据后，从redis中获取5分钟前的数据，进行计算获取5分钟内的数据并保存到数据库中

**查看详细图片：**

![](http://osewdhh4j.bkt.clouddn.com/20170810111735.png)

![](http://osewdhh4j.bkt.clouddn.com/20170810111830.png)
![](http://osewdhh4j.bkt.clouddn.com/20170810111848.png)
![](http://osewdhh4j.bkt.clouddn.com/20170810112603.png)

### Hbase对每张表的读写监控

**数据来源：**

> 通过Hbase Java Api
 - 连接HBASE`org.apache.hadoop.hbase.client.Connection connection`
 - 然后获取org.apache.hadoop.hbase.client.Admin admin = connection.getAdmin();
 - 得到HBASE中的regionServer集合，
 - 获取每个regionServer中RegionsLoad()；
 - 遍历RegionLoad获取每张表的Table Region
 

**查看详细图片：**

![](http://osewdhh4j.bkt.clouddn.com/20170810134044.png)

![](http://osewdhh4j.bkt.clouddn.com/20170810134349.png)
![](http://osewdhh4j.bkt.clouddn.com/20170810134434.png)
![](http://osewdhh4j.bkt.clouddn.com/20170810134743.png)


### jmx常用监控指标

  |  	监控指标	  |  	范围	  |  	指标含义	  |  
  |  	  ----  	  |  	  ----  	  |  	  ----  	  |  
  |  	OpenFileDescriptorCount	  |  	Regionserver本机	  |  	当前机器打开文件数	  |  
  |  	FreePhysicalMemorySize	  |  	Regionserver本机	  |  	空虚物理内存大小	  |  
  |  	AvailableProcessors	  |  	Regionserver本机	  |  	可用cpu个数	  |  
  |  	Region前缀--storeCount	  |  	单个region	  |  	Store个数	  |  
  |  	Region前缀--storeFileCount	  |  	单个region	  |  	Storefile个数	  |  
  |  	Region前缀--memStoreSize	  |  	单个region	  |  	Memstore大小	  |  
  |  	Region前缀--storeFileSize	  |  	单个region	  |  	Storefile大小	  |  
  |  	Region前缀--compactionsCompletedCount	  |  	单个region	  |  	合并完成次数	  |  
  |  	Region前缀--numBytesCompactedCount	  |  	单个region	  |  	合并文件总大小	  |  
  |  	Region前缀-- numFilesCompactedCount	  |  	单个region	  |  	合并完成文件个数	  |  
  |  	totalRequestCount	  |  	Regionserver	  |  	总请求数	  |  
  |  	readRequestCount	  |  	Regionserver	  |  	读请求数	  |  
  |  	writeRequestCount	  |  	Regionserver	  |  	写请求数	  |  
  |  	compactedCellsCount	  |  	Regionserver	  |  	合并cell个数	  |  
  |  	majorCompactedCellsCount	  |  	Regionserver	  |  	大合并cell个数	  |  
  |  	flushedCellsSize	  |  	Regionserver	  |  	flush到磁盘的大小	  |  
  |  	blockedRequestCount	  |  	Regionserver	  |  	因memstore大于阈值而引发flush的次数	  |  
  |  	splitRequestCount	  |  	Regionserver	  |  	region分裂请求次数	  |  
  |  	splitSuccessCounnt	  |  	Regionserver	  |  	region分裂成功次数	  |  
  |  	slowGetCount	  |  	Regionserver	  |  	请求完成时间超过1000ms的次数	  |  
  |  	numOpenConnections	  |  	Regionserver	  |  	该regionserver打开的连接数	  |  
  |  	numActiveHandler	  |  	Regionserver	  |  	rpc handler数	  |  
  |  	receivedBytes	  |  	Regionserver	  |  	收到数据量	  |  
  |  	sentBytes	  |  	Regionserver	  |  	发出数据量	  |  
  |  	HeapMemoryUsage --->>>used	  |  	Regionserver	  |  	堆内存使用量	  |  
  |  	SyncTime_mean	  |  	Regionserver	  |  	WAL写hdfs的平均时间	  |  
  |  	regionCount	  |  	Regionserver	  |  	Regionserver管理region数量	  |  
  |  	memStoreSize	  |  	Regionserver	  |  	Regionserver管理的总memstoresize	  |  
  |  	storeFileSize	  |  	Regionserver	  |  	该Regionserver管理的storefile大小	  |  
  |  	staticIndexSize	  |  	Regionserver	  |  	该regionserver所管理的表索引大小	  |  
  |  	storeFileCount	  |  	Regionserver	  |  	该regionserver所管理的storefile个数	  |  
  |  	hlogFileSize	  |  	Regionserver	  |  	WAL文件大小	  |  
  |  	hlogFileCount	  |  	Regionserver	  |  	WAL文件个数	  |  
  |  	storeCount	  |  	Regionserver	  |  	该regionserver所管理的store个数	  |  
  |  	Name: java.lang:type=MemoryPool,name=Par Eden Space CollectionUsage—>>used	  |  	Regionserver	  |  	Eden区使用空间大小	  |  
  |  	Name: java.lang:type=MemoryPool,name=CMS Old Gen	  |  	Regionserver	  |  	老年代内存大小	  |  
  |  	Name: java.lang:type=MemoryPool,name=Par Survivor Space CollectionUsageà> used	  |  	Regionserver	  |  	Survivor内存大小	  |  
  |  	GcTimeMillis	  |  	Regionserver	  |  	GC总时间	  |  
  |  	GcTimeMillisParNew	  |  	Regionserver	  |  	ParNew GC时间	  |  
  |  	GcCount	  |  	Regionserver	  |  	GC总次数	  |  
  |  	GcCountConcurrentMarkSweep	  |  	Regionserver	  |  	ConcurrentMarkSweep总次数	  |  
  |  	GcTimeMillisConcurrentMarkSweep	  |  	Regionserver	  |  	ConcurrentMarkSweep GC时间	  |  
  |  	ThreadsBlocked	  |  	Regionserver	  |  	Block线程数	  |  
  |  	ThreadsWaiting	  |  	Regionserver	  |  	等待线程数	  |  






