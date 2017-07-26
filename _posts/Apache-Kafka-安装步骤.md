---
title: Apache Kafka - 安装步骤
date: 2017-07-13 19:24:58
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/kafka%E5%AE%89%E8%A3%85%E6%AD%A5%E9%AA%A4.png
keywords:  Apache Kafka - 安装步骤,Apache Kafka,kafka安装
description: Apache Kafka教程  之 Apache Kafka - 安装步骤
---
## Apache Kafka教程  之 Apache Kafka - 安装步骤

![enter image description here](http://osewdhh4j.bkt.clouddn.com/kafka%E5%AE%89%E8%A3%85%E6%AD%A5%E9%AA%A4.png)

### Apache Kafka - 安装步骤

### 步骤1 - 验证Java安装

希望您现在已经在您的计算机上安装了Java，所以您只需使用以下命令进行验证。

    $ java -version

如果在您的计算机上成功安装了java，您可以看到已安装的Java版本。

**步骤1.1 - 下载JDK**
如果未下载Java，请通过以下链接下载最新版本的JDK，并下载最新版本。

http://www.oracle.com/technetwork/java/javase/downloads/index.html
现在最新版本是JDK 8u 60，文件是“jdk-8u60-linux-x64.tar.gz”。请在您的机器上下载该文件。

**步骤1.2 - 提取文件**
一般来说，正在下载的文件存储在下载文件夹中，使用以下命令进行验证并提取tar设置。

    $ cd /go/to/download/path
    $ tar -zxf jdk-8u60-linux-x64.gz

**步骤1.3 - 移动到选择目录**
要使java可用于所有用户，将提取的java内容移动到usr / local / java /文件夹。

    $ su
    password: (type password of root user)
    $ mkdir /opt/jdk
    $ mv jdk-1.8.0_60 /opt/jdk/

**步骤1.4 - 设置路径**
要设置路径和`JAVA_HOME`变量，请将以下命令添加到`〜/ .bashrc`文件中。

    export JAVA_HOME =/usr/jdk/jdk-1.8.0_60
    export PATH=$PATH:$JAVA_HOME/bin

现在将所有更改应用到当前运行的系统中。

    $ source ~/.bashrc

步骤1.5 - Java替代方案
使用以下命令更改Java Alternatives。

    update-alternatives --install /usr/bin/java java /opt/jdk/jdk1.8.0_60/bin/java 100

步骤1.6 - 现在使用步骤1中说明的验证命令（java -version）来验证java。

### 步骤2 - ZooKeeper框架安装
**步骤2.1 - 下载ZooKeeper**
要在您的机器上安装ZooKeeper框架，请访问以下链接并下载最新版本的ZooKeeper。

http://zookeeper.apache.org/releases.html
截至目前，ZooKeeper的最新版本为3.4.6（ZooKeeper-3.4.6.tar.gz）。

**步骤2.2 - 提取tar文件**
使用以下命令提取tar文件

    $ cd opt/
    $ tar -zxf zookeeper-3.4.6.tar.gz
    $ cd zookeeper-3.4.6
    $ mkdir data

**步骤2.3 - 创建配置文件**
使用命令vi“conf / zoo.cfg” 打开名为conf / zoo.cfg的配置文件，并将所有以下参数设置为起始点。

    $ vi conf/zoo.cfg
    tickTime=2000
    dataDir=/path/to/zookeeper/data
    clientPort=2181
    initLimit=5
    syncLimit=2

一旦配置文件成功保存并再次返回终端，就可以启动zookeeper服务器。

**步骤2.4 - 启动ZooKeeper服务器**

    $ bin/zkServer.sh start

执行此命令后，您会得到如下所示的响应：

    $ JMX enabled by default
    $ Using config: /Users/../zookeeper-3.4.6/bin/../conf/zoo.cfg
    $ Starting zookeeper ... STARTED

**步骤2.5 - 启动CLI**

    $ bin/zkCli.sh

键入上述命令后，您将连接到zookeeper服务器，并得到以下响应。

    Connecting to localhost:2181
    Welcome to ZooKeeper!
    WATCHER::
    WatchedEvent state:SyncConnected type: None path:null
    [zk: localhost:2181(CONNECTED) 0]

**步骤2.6 - 停止Zookeeper服务器**
连接服务器并执行所有操作后，可以使用以下命令停止zookeeper服务器 -

    $ bin/zkServer.sh stop

现在，您已经在您的机器上成功安装了Java和ZooKeeper。让我们看看安装Apache Kafka的步骤。

### 步骤3 - Apache Kafka安装
让我们继续以下步骤在您的机器上安装Kafka。

**步骤3.1 - 下载kafka**
要在您的机器上安装Kafka，请点击下面的链接 -

https://www.apache.org/dyn/closer.cgi?path=/kafka/0.9.0.0/kafka_2.11-0.9.0.0.tgz
现在最新的版本，即 - kafka_2.11_0.9.0.0.tgz将被下载到你的机器上。

**步骤3.2 - 提取tar文件**
使用以下命令提取tar文件 -

    $ cd opt/
    $ tar -zxf kafka_2.11.0.9.0.0 tar.gz
    $ cd kafka_2.11.0.9.0.0

现在您已经在您的机器上下载了最新版本的Kafka。

**步骤3.3 - 启动服务器**
您可以通过以下命令启动服务器 -

    $ bin/kafka-server-start.sh config/server.properties

服务器启动后，您将在屏幕上看到以下响应：

    $ bin/kafka-server-start.sh config/server.properties

    [2016-01-02 15:37:30,410] INFO KafkaConfig values:
    request.timeout.ms = 30000
    log.roll.hours = 168
    inter.broker.protocol.version = 0.9.0.X
    log.preallocate = false
    security.inter.broker.protocol = PLAINTEXT


### 步骤4 - 停止服务器
执行所有操作后，可以使用以下命令停止服务器 -

    $ bin/kafka-server-stop.sh config/server.properties





