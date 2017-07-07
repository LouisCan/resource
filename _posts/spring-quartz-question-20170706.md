---
title: spring quartz question-20170706
date: 2017-07-06 21:51:34
tags: spring
categories: spring
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170706211340.png
blogexcerpt: 使用quartz中遇到的问题，Caused by org.quartz.JobPersistenceException  Couldn't store job  FUNCTION ××××.EMPTY_BLOB does not exist
---


使用quartz遇到的一些问题

### 1.JobPersistenceException异常

```java
Caused by: org.quartz.JobPersistenceException: 
Couldn't store job: FUNCTION ××××.EMPTY_BLOB does not exist [See nested exception: 
com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException:
 FUNCTION ××××.EMPTY_BLOB does not exist]
```

 这个异常原因是：我用的数据库是Mysql，而配置文件中org.quartz.jobStore.driverDelegateClass设置的是Oracle的
 修改方法：

```java
 org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.StdJDBCDelegate
```


### 2.org.quartz.ObjectAlreadyExistsException异常

```java
Caused by: org.quartz.ObjectAlreadyExistsException: Unable to store Job : 'DEFAULT.JOB_CLEAN_HISTORY_LOG', because one already exists with this identification.
```

这个错误提示唯一的标识已经存在
 我出现这个错误是发生在：
 把项目打包放在一个tomcat下启动正常
 当把项目放在第二个tomcat下同时启动时，就出现了这个错误
 **解决办法：**
 在初始化调度的时候clean一下
 

```
Scheduler scheduler = new StdSchedulerFactory().getScheduler();
scheduler.clear();
```
### 3. 集群环境下行为不一致

```java
This scheduler instance (localhost1490343168982) is still active but was recovered by another instance in the cluster. This may cause inconsistent behavior.
```
原因
这种与群集有关的错误的最可能原因是具有系统时钟未同步的群集节点。 
解决办法
确保您的群集节点与同一个NTP服务器定期同步，或找到其他方法来保持同步。
 




