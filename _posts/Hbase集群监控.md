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




