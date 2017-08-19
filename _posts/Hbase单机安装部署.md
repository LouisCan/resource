---
title: Hbase单机安装部署
date: 2017-07-27 22:29:15
tags: hbase
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170805205407.png
keywords: hbase,Hbase单机安装部署
description: Hbase单机安装部署,Hbase官网下载地址http://www.apache.org/dyn/closer.cgi/hbase/
---

## Hbase单机安装部署

### 下载Hbase

Hbase官网下载地址

```
http://www.apache.org/dyn/closer.cgi/hbase/
```

### 解压

```
tar -zvxf hbase-0.94.27.tar.gz
cd hbase-0.94.27
```

![](http://osewdhh4j.bkt.clouddn.com/20170728094545.png)

### 配置

```
cd conf/
vi hbase-site.xml
```

```
<configuration>

  <property>
    <name>hbase.rootdir</name>
    <value>file:///usr/local/webserver/hbase-0.94.27/logs/site</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>


</configuration>

```

### 启动hbase

```
./bin/start-hbase.sh
```

![](http://osewdhh4j.bkt.clouddn.com/20170728101558.png)

### 查看Hbase

浏览器访问：http://localhost:60010/

![](http://osewdhh4j.bkt.clouddn.com/20170728101837.png)

### 操作Hbase Shell

```
./bin/hbase shell
```

![](http://osewdhh4j.bkt.clouddn.com/20170728102001.png)

![](http://osewdhh4j.bkt.clouddn.com/20170728102145.png)



