---
title: spring事务注解
date: 2017-06-28 21:32:21
tags: spring
categories: spring
thumbnail: http://osewdhh4j.bkt.clouddn.com/spring.png
description: 在service类前加上@Transactional，声明这个service所有方法需要事务管理。每一个业务方法开始时都会打开一个事务。Spring默认情况下会对运行期例外(RunTimeException)进行事务回滚。这个例外是**unchecked** ,如果遇到checked意外就不回滚.
---

**Spring事务的传播行为**

在service类前加上@Transactional，声明这个service所有方法需要事务管理。每一个业务方法开始时都会打开一个事务。 

Spring默认情况下会对运行期例外(RunTimeException)进行事务回滚。这个例外是##unchecked## 

如果遇到checked意外就不回滚。 

**改变默认规则：**

1 让checked例外也回滚：在整个方法前加上 `@Transactional(rollbackFor=Exception.class)` 

2 让unchecked例外不回滚： `@Transactional(notRollbackFor=RunTimeException.class)` 

3 不需要事务管理的(只查询的)方法：`@Transactional(propagation=Propagation.NOT_SUPPORTED)` 
例如：`@Transactional(propagation=Propagation.NOT_SUPPORTED,readOnly=true)，这样就做成一个只读事务，可以提高效率。`

**@Transactional-PROPAGATION**
事务的几种传播特性
1. PROPAGATION_REQUIRED: 如果存在一个事务，则支持当前事务。如果没有事务则开启
2. PROPAGATION_SUPPORTS: 如果存在一个事务，支持当前事务。如果没有事务，则非事务的执行
3. PROPAGATION_MANDATORY: 如果已经存在一个事务，支持当前事务。如果没有一个活动的事务，则抛出异常。
4. PROPAGATION_REQUIRES_NEW: 总是开启一个新的事务。如果一个事务已经存在，则将这个存在的事务挂起。
5. PROPAGATION_NOT_SUPPORTED: 总是非事务地执行，并挂起任何存在的事务。
6. PROPAGATION_NEVER: 总是非事务地执行，如果存在一个活动事务，则抛出异常
7. PROPAGATION_NESTED：如果一个活动的事务存在，则运行在一个嵌套的事务中. 如果没有活动事务, 
则按TransactionDefinition.PROPAGATION_REQUIRED 属性执行

**@Transactional-ISOLATION**
隔离级别
1. ISOLATION_DEFAULT： 这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别.
      另外四个与JDBC的隔离级别相对应
2. ISOLATION_READ_UNCOMMITTED： 这是事务最低的隔离级别，它充许令外一个事务可以看到这个事务未提交的数据。
      这种隔离级别会产生脏读，不可重复读和幻像读。
3. ISOLATION_READ_COMMITTED： 保证一个事务修改的数据提交后才能被另外一个事务读取。另外一个事务不能读取该事务未提交的数据
4. ISOLATION_REPEATABLE_READ： 这种事务隔离级别可以防止脏读，不可重复读。但是可能出现幻像读。
      它除了保证一个事务不能读取另一个事务未提交的数据外，还保证了避免下面的情况产生(不可重复读)。
5. ISOLATION_SERIALIZABLE 这是花费最高代价但是最可靠的事务隔离级别。事务被处理为顺序执行。
      除了防止脏读，不可重复读外，还避免了幻像读。 

其中的一些概念的说明：

  **脏读:**
指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。
因为这个数据是还没有提交的数据， 那么另外一 个事务读到的这个数据是脏数据，依据脏数据所做的操作可能是不正确的。

**不可重复读:**
指在一个事务内，多次读同一数据。
在这个事务还没有结束时，另外一个事务也访问该同一数据。 
那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的数据可能是不一样的。
这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。

 **幻觉读:**
指当事务不是独立执行时发生的一种现象，例如第一个事务对一个表中的数据进行了修改，这种修改涉及 到表中的全部数据行。
同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。
那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象发生了幻觉一样。



**@Transactional-timeout**

用于设置事务处理的时间长度，阻止可能出现的长时间的阻塞系统或者占用系统资源。单位为秒。如果超时设置事务回滚，并抛出TransactionTimedOutException异常。

Spring事务超时 = 事务开始时到最后一个Statement创建时时间 + 最后一个Statement的执行时超时时间（即其queryTimeout）。

**@Transactional--readOnly**

默认情况下是false，可以显示指定为true， 告诉程序该方法下使用的是只读操作，如果进行其他非读操作，则会跑出异常；
这个紧紧适用于只有readOnly标识的情况下，当其与propagation机制同时使用之时，则会出现只读设置被覆盖的情况，比如在required的情况下。
在使用 REQUIRED 传播模式时，会抛出一个只读连接异常。
使用 JDBC 时是这样。使用基于 ORM 的框架时，只读标志只是对数据库的一个提示，并且一条基于 ORM 框架的指令（本例中是 Hibernate）将对象缓存的 flush 模式设置为 NEVER，表示在这个工作单元中，该对象缓存不应与数据库同步。
不过，REQUIRED 传播模式会覆盖所有这些内容，允许事务启动并工作，就好像没有设置只读标志一样。

**@Transactional--rollbackForClassName/rollbackFor**

Spring默认情况下会对运行期例外(RunTimeException)进行事务回滚。这个例外是unchecked，如果遇到checked意外就不回滚。

用来指明回滚的条件是哪些异常类或者异常类名。用法比较简单，这些不再赘述。

**@Transactional--noRollbackForClassName/noRollbackFor**
用来指明在抛出特定异常的情况下，不进行数据库的事务回滚操作。
