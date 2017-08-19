---
title: Hbase架构与原理
date: 2017-08-11 15:18:16
tags: hbase
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/hbase.png
keywords: hbase,Hbase架构与原理
description: HBase是一个分布式的、面向列的开源数据库，该技术来源于 Fay Chang所撰写的Google论文“Bigtable：一个结构化数据的分布式存储系统”。就像Bigtable利用了Google文件系统（File System）所提供的分布式数据存储一样，HBase在Hadoop之上提供了类似于Bigtable的能力。HBase是Apache的Hadoop项目的子项目。HBase不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是HBase基于列的而不是基于行的模式。
---

## Hbase架构与原理

> HBase是一个分布式的、面向列的开源数据库，该技术来源于 Fay Chang所撰写的Google论文“Bigtable：一个结构化数据的分布式存储系统”。就像Bigtable利用了Google文件系统（File System）所提供的分布式数据存储一样，HBase在Hadoop之上提供了类似于Bigtable的能力。HBase是Apache的Hadoop项目的子项目。HBase不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是HBase基于列的而不是基于行的模式。

### 1、Hadoop生太圈

![](http://osewdhh4j.bkt.clouddn.com/Dpz74XZ.jpg)
hadoop所有应用都是构建于hdfs（它提供高可靠的底层存储支持，几乎已经成为分布式文件存储系统事实上的工业标准）之上的分布式列存储系统，主要用于海量结构化数据存储。通过Hadoop生态圈，可以看到HBase的身影，可见HBase在Hadoop的生态圈是扮演这一个重要的角色那就是  实时、分布式、高维数据 的数据存储；

### 2、HBase简介 

– HBase – Hadoop Database，是一个高可靠性、高性能、面向列、可伸缩、 实时读写的分布式数据库
– 利用Hadoop HDFS作为其文件存储系统,利用Hadoop MapReduce来处理HBase中的海量数据,利用Zookeeper作为其分布式协同服务
– 主要用来存储非结构化和半结构化的松散数据（列存NoSQL数据库）

> Hbase特性：
>
 1. 强一致性读写: HBase 不是 "最终一致性(eventually consistent)" 数据存储这让它很适合高速计数聚合类任务。
 2. 自动分片(Automatic sharding):HBase表通过region分布在集群中。数据增长时，region会自动分割并重新分布。
 3. RegionServer 自动故障转移
 4. Hadoop/HDFS 集成: HBase 支持本机外HDFS 作为它的分布式文件系统。
 5. MapReduce: HBase 通过MapReduce支持大并发处理， HBase 可以同时做源和目标.
 6. Java 客户端 API: HBase 支持易于使用的 Java API 进行编程访问.
 7. Thrift/REST API:HBase 也支持Thrift和 REST 作为非Java 前端.
 8. Block Cache 和 Bloom Filters: 对于大容量查询优化， HBase支持 Block Cache 和 Bloom Filters。
 9. 运维管理: HBase提供内置网页用于运维视角和JMX 度量.

### 3、HBase数据模型

以关系型数据的思维下会感觉，上面的表格是一个5列4行的数据表格，但是在HBase中这种理解是错误的，其实在HBase中上面的表格只是一行数据；

**Row Key:**

 - 决定一行数据的唯一标识

 - RowKey是按照字典顺序排序的。

 - Row key最多只能存储64k的字节数据。

**Column Family列族（CF1、CF2、CF3） & qualifier列：**

 - HBase表中的每个列都归属于某个列族，列族必须作为表模式(schema) 定义的一部分预先给出。如create ‘test’, ‘course’；
 - 列名以列族作为前缀，每个“列族”都可以有多个列成员(column，每个列族中可以存放几千~上千万个列)；如 CF1:q1, CF2:qw,新的列族成员（列）可以随后按需、动态加入，Family下面可以有多个Qualifier，所以可以简单的理解为，HBase中的列是二级列,也就是说Family是第一级列，Qualifier是第二级列。两个是父子关系。
 - 权限控制、存储以及调优都是在列族层面进行的；
 - HBase把同一列族里面的数据存储在同一目录下，由几个文件保存。
 - 目前为止HBase的列族能能够很好处理最多不超过3个列族。

**Timestamp时间戳：**

 - 在HBase每个cell存储单元对同一份数据有多个版本，根据唯一的时间戳来区分每个版本之间的差异，不同版本的数据按照时间倒序排序，最新的数据版本排在最前面。
 - 时间戳的类型是64位整型。
 - 时间戳可以由HBase(在数据写入时自动)赋值，此时时间戳是精确到毫 秒的当前系统时间。
 - 时间戳也可以由客户显式赋值，如果应用程序要避免数据版本冲突， 就必须自己生成具有唯一性的时间戳。

**Cell单元格：**

 - 由行和列的坐标交叉决定；

 - 单元格是有版本的（由时间戳来作为版本）；

 - 单元格的内容是未解析的字节数组（Byte[]），cell中的数据是没有类型的，全部是字节码形式存贮。

 - 由{row key，column(=<family> +<qualifier>)，version}唯一确定的单元。

### 4、HBase体系架构

![](http://osewdhh4j.bkt.clouddn.com/183233-20160229221255392-1652546646.jpg)

**Client**
 - 包含访问HBase的接口并维护cache来加快对HBase的访问

**Zookeeper**

 - 保证任何时候，集群中只有一个master
 - 存贮所有Region的寻址入口。
 - 实时监控Region server的上线和下线信息。并实时通知Master
 - 存储HBase的schema和table元数据

**Master**

 - 为Region server分配region
 - 负责Region server的负载均衡
 - 发现失效的Region server并重新分配其上的region
 - 管理用户对table的增删改操作

**RegionServer**

 - Region server维护region，处理对这些region的IO请求
 - Region server负责切分在运行过程中变得过大的region　

 **HLog(WAL log)：**

 - HLog文件就是一个普通的Hadoop Sequence File，Sequence File 的Key是HLogKey对象，HLogKey中记录了写入数据的归属信息，除了table和 region名字外，同时还包括sequence number和timestamp，timestamp是” 写入时间”，sequence number的起始值为0，或者是最近一次存入文件系 统中sequence number。
 - HLog SequeceFile的Value是HBase的KeyValue对象，即对应HFile中的 KeyValue

**Region**

 - HBase自动把表水平划分成多个区域(region)，每个region会保存一个表里面某段连续的数据；每个表一开始只有一个region，随着数据不断插入表，region不断增大，当增大到一个阀值的时候，region就会等分会 两个新的region（裂变）；
 - 当table中的行不断增多，就会有越来越多的region。这样一张完整的表 被保存在多个Regionserver上。

**Memstore 与 storefile**

 - 一个region由多个store组成，一个store对应一个CF（列族）
 - store包括位于内存中的memstore和位于磁盘的storefile写操作先写入memstore，当memstore中的数据达到某个阈值，hregionserver会启动flashcache进程写入storefile，每次写入形成单独的一个storefile
 - 当storefile文件的数量增长到一定阈值后，系统会进行合并（minor、 major compaction），在合并过程中会进行版本合并和删除工作 （majar），形成更大的storefile。
 - 当一个region所有storefile的大小和超过一定阈值后，会把当前的region分割为两个，并由hmaster分配到相应的regionserver服务器，实现负载均衡。
 - 客户端检索数据，先在memstore找，找不到再找storefile
 - HRegion是HBase中分布式存储和负载均衡的最小单元。最小单元就表示不同的HRegion可以分布在不同的HRegion server上。
 - HRegion由一个或者多个Store组成，每个store保存一个columns family。
 - 每个Strore又由一个memStore和0至多个StoreFile组成。

![](http://osewdhh4j.bkt.clouddn.com/183233-20160229221859080-1333186051.png)

### 5.Hbase优化
 
**1.预先分区**

默认情况下，在创建 HBase 表的时候会自动创建一个 Region 分区，当导入数据的时候，所有的 HBase 客户端都向这一个 Region 写数据，直到这个 Region 足够大了才进行切分。一种可以加快批量写入速度的方法是通过预先创建一些空的 Regions，这样当数据写入 HBase 时，会按照 Region 分区情况，在集群内做数据的负载均衡。

**2.Rowkey优化**

HBase 中 Rowkey 是按照字典序存储，因此，设计 Rowkey 时，要充分利用排序特点，将经常一起读取的数据存储到一块，将最近可能会被访问的数据放在一块。

此外，Rowkey 若是递增的生成，建议不要使用正序直接写入 Rowkey，而是采用 reverse 的方式反转Rowkey，使得 Rowkey 大致均衡分布，这样设计有个好处是能将 RegionServer 的负载均衡，否则容易产生所有新数据都在一个 RegionServer 上堆积的现象，这一点还可以结合 table 的预切分一起设计。

**3.减少列族数量**

不要在一张表里定义太多的 ColumnFamily。目前 Hbase 并不能很好的处理超过 2~3 个 ColumnFamily 的表。因为某个 ColumnFamily 在 flush 的时候，它邻近的 ColumnFamily 也会因关联效应被触发 flush，最终导致系统产生更多的 I/O。

**4.缓存策略**

创建表的时候，可以通过 HColumnDescriptor.setInMemory(true) 将表放到 RegionServer 的缓存中，保证在读取的时候被 cache 命中。

**5.设置存储生命期**

创建表的时候，可以通过 `HColumnDescriptor.setTimeToLive(int timeToLive)` 设置表中数据的存储生命期，过期数据将自动被删除。

**6.硬盘配置**

每台 RegionServer 管理 10~1000 个 Regions，每个 Region 在 1~2G，则每台 Server 最少要 10G，最大要1000*2G=2TB，考虑 3 备份，则要 6TB。方案一是用 3 块 2TB 硬盘，二是用 12 块 500G 硬盘，带宽足够时，后者能提供更大的吞吐率，更细粒度的冗余备份，更快速的单盘故障恢复。

**7.分配合适的内存给RegionServer服务**

在不影响其他服务的情况下，越大越好。例如在 HBase 的 conf 目录下的 hbase-env.sh 的最后添加 `export HBASE_REGIONSERVER_OPTS="-Xmx16000m$HBASE_REGIONSERVER_OPTS”`

其中 16000m 为分配给 RegionServer 的内存大小。

**8.写数据的备份数**

备份数与读性能成正比，与写性能成反比，且备份数影响高可用性。有两种配置方式，一种是将 hdfs-site.xml拷贝到 hbase 的 conf 目录下，然后在其中添加或修改配置项 dfs.replication 的值为要设置的备份数，这种修改对所有的 HBase 用户表都生效，另外一种方式，是改写 HBase 代码，让 HBase 支持针对列族设置备份数，在创建表时，设置列族备份数，默认为 3，此种备份数只对设置的列族生效。

**9.WAL（预写日志）**

可设置开关，表示 HBase 在写数据前用不用先写日志，默认是打开，关掉会提高性能，但是如果系统出现故障(负责插入的 RegionServer 挂掉)，数据可能会丢失。配置 WAL 在调用 JavaAPI 写入时，设置 Put 实例的WAL，调用 Put.setWriteToWAL(boolean)。

**10. 批量写**

HBase 的 Put 支持单条插入，也支持批量插入，一般来说批量写更快，节省来回的网络开销。在客户端调用JavaAPI 时，先将批量的 Put 放入一个 Put 列表，然后调用 HTable 的 Put(Put 列表) 函数来批量写。

**11. 客户端一次从服务器拉取的数量**

通过配置一次拉去的较大的数据量可以减少客户端获取数据的时间，但是它会占用客户端内存。有三个地方可进行配置：

1）在 HBase 的 conf 配置文件中进行配置 `hbase.client.scanner.caching`；

2）通过调用` HTable.setScannerCaching(intscannerCaching)` 进行配置；

3）通过调用` Scan.setCaching(intcaching)` 进行配置。三者的优先级越来越高。

**12. RegionServer的请求处理I/O线程数**

较少的 IO 线程适用于处理单次请求内存消耗较高的 Big Put 场景 (大容量单次 Put 或设置了较大 cache 的Scan，均属于 Big Put) 或 ReigonServer 的内存比较紧张的场景。

较多的 IO 线程，适用于单次请求内存消耗低，TPS 要求 (每秒事务处理量 (TransactionPerSecond)) 非常高的场景。设置该值的时候，以监控内存为主要参考。

在 hbase-site.xml 配置文件中配置项为 `hbase.regionserver.handler.count`。

**13. Region的大小设置**

配置项为 `hbase.hregion.max.filesize`，所属配置文件为 `hbase-site.xml`.，默认大小 **256M**。

在当前 ReigonServer 上单个 Reigon 的最大存储空间，单个 Region 超过该值时，这个 Region 会被自动 split成更小的 Region。小 Region 对 split 和 compaction 友好，因为拆分 Region 或 compact 小 Region 里的StoreFile 速度很快，内存占用低。缺点是 split 和 compaction 会很频繁，特别是数量较多的小 Region 不停地split, compaction，会导致集群响应时间波动很大，Region 数量太多不仅给管理上带来麻烦，甚至会引发一些Hbase 的 bug。一般 512M 以下的都算小 Region。大 Region 则不太适合经常 split 和 compaction，因为做一次 compact 和 split 会产生较长时间的停顿，对应用的读写性能冲击非常大。

此外，大 Region 意味着较大的 StoreFile，compaction 时对内存也是一个挑战。如果你的应用场景中，某个时间点的访问量较低，那么在此时做 compact 和 split，既能顺利完成 split 和 compaction，又能保证绝大多数时间平稳的读写性能。compaction 是无法避免的，split 可以从自动调整为手动。只要通过将这个参数值调大到某个很难达到的值，比如 100G，就可以间接禁用自动 split(RegionServer 不会对未到达 100G 的 Region 做split)。再配合 RegionSplitter 这个工具，在需要 split 时，手动 split。手动 split 在灵活性和稳定性上比起自动split 要高很多，而且管理成本增加不多，比较推荐 online 实时系统使用。内存方面，小 Region 在设置memstore 的大小值上比较灵活，大 Region 则过大过小都不行，过大会导致 flush 时 app 的 IO wait 增高，过小则因 StoreFile 过多影响读性能。

**14.操作系统参数**

Linux系统最大可打开文件数一般默认的参数值是1024,如果你不进行修改并发量上来的时候会出现“Too Many Open Files”的错误，导致整个HBase不可运行，你可以用ulimit -n 命令进行修改，或者修改/etc/security/limits.conf和/proc/sys/fs/file-max 的参数，具体如何修改可以去Google 关键字 “linux limits.conf ”

**15.Jvm配置**

修改 hbase-env.sh 文件中的配置参数，根据你的机器硬件和当前操作系统的JVM(32/64位)配置适当的参数

`HBASE_HEAPSIZE 4000` HBase使用的 JVM 堆的大小

`HBASE_OPTS "‐server ‐XX:+UseConcMarkSweepGC"JVM GC 选项`

`HBASE_MANAGES_ZKfalse` 是否使用Zookeeper进行分布式管理

**16. 持久化**

重启操作系统后HBase中数据全无，你可以不做任何修改的情况下，创建一张表，写一条数据进行，然后将机器重启，重启后你再进入HBase的shell中使用 list 命令查看当前所存在的表，一个都没有了。是不是很杯具？没有关系你可以在hbase/conf/hbase-default.xml中设置hbase.rootdir的值，来设置文件的保存位置指定一个文件夹，例如：<value>file:///you/hbase-data/path</value>，你建立的HBase中的表和数据就直接写到了你的磁盘上，同样你也可以指定你的分布式文件系统HDFS的路径例如:hdfs://NAMENODE_SERVER:PORT/HBASE_ROOTDIR，这样就写到了你的分布式文件系统上了。

**17. 缓冲区大小**

`hbase.client.write.buffer`

这个参数可以设置写入数据缓冲区的大小，当客户端和服务器端传输数据，服务器为了提高系统运行性能开辟一个写的缓冲区来处理它，这个参数设置如果设置的大了，将会对系统的内存有一定的要求，直接影响系统的性能。

**18. 扫描目录表**

`hbase.master.meta.thread.rescanfrequency`

定义多长时间HMaster对系统表 root 和 meta 扫描一次，这个参数可以设置的长一些，降低系统的能耗。

**19. split/compaction时间间隔**

`hbase.regionserver.thread.splitcompactcheckfrequency`

这个参数是表示多久去RegionServer服务器运行一次split/compaction的时间间隔，当然split之前会先进行一个compact操作.这个compact操作可能是minorcompact也可能是major compact.compact后,会从所有的Store下的所有StoreFile文件最大的那个取midkey.这个midkey可能并不处于全部数据的mid中.一个row-key的下面的数据可能会跨不同的HRegion。

**20. 缓存在JVM堆中分配的百分比**

`hfile.block.cache.size`

指定HFile/StoreFile 缓存在JVM堆中分配的百分比，默认值是0.2，意思就是20%，而如果你设置成0，就表示对该选项屏蔽。

**21. ZooKeeper客户端同时访问的并发连接数**

`hbase.zookeeper.property.maxClientCnxns`

这项配置的选项就是从zookeeper中来的，表示ZooKeeper客户端同时访问的并发连接数，ZooKeeper对于HBase来说就是一个入口这个参数的值可以适当放大些。

**22. memstores占用堆的大小参数配置**

`hbase.regionserver.global.memstore.upperLimit`

在RegionServer中所有memstores占用堆的大小参数配置，默认值是0.4，表示40%，如果设置为0，就是对选项进行屏蔽。

**23. Memstore中缓存写入大小**

`hbase.hregion.memstore.flush.size`

Memstore中缓存的内容超过配置的范围后将会写到磁盘上，例如：删除操作是先写入MemStore里做个标记，指示那个value, column 或 family等下是要删除的，HBase会定期对存储文件做一个major compaction，在那时HBase会把MemStore刷入一个新的HFile存储文件中。如果在一定时间范围内没有做major compaction，而Memstore中超出的范围就写入磁盘上了。


       

