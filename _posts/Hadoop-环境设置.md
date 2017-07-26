---
title: Hadoop - 环境设置
date: 2017-07-17 19:34:07
tags: hadoop
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/hadoop%E7%8E%AF%E5%A2%83%E8%AE%BE%E7%BD%AE.png
keywords: 大数据,hadoop,Hadoop环境设置
description: Hadoop由GNU / Linux平台及其风格支持。因此，我们必须安装一个用于设置Hadoop环境的Linux操作系统。如果您的操作系统不是Linux，则可以在其中安装一个Virtualbox软件，并在Virtualbox中安装Linux。
---

## Hadoop - 环境设置

> Hadoop由GNU / Linux平台及其风格支持。因此，我们必须安装一个用于设置Hadoop环境的Linux操作系统。如果您的操作系统不是Linux，则可以在其中安装一个Virtualbox软件，并在Virtualbox中安装Linux。

![](http://osewdhh4j.bkt.clouddn.com/hadoop%E7%8E%AF%E5%A2%83%E8%AE%BE%E7%BD%AE.png)

### 预安装设置
在将Hadoop安装到Linux环境之前，我们需要使用ssh（安全Shell）设置Linux。按照以下步骤设置Linux环境。

**创建用户**
一开始，建议为Hadoop创建一个单独的用户，以将Hadoop文件系统从Unix文件系统中分离出来。按照以下步骤创建用户：

 - 使用命令“su”打开根目录。
 - 使用命令“useradd username”从root帐户创建一个用户。
 - 现在您可以使用命令“su username”打开现有的用户帐户。 打开Linux终端，并键入以下命令创建一个用户。

```java
$ su 
   password: 
# useradd hadoop 
# passwd hadoop 
   New passwd: 
   Retype new passwd 

```
### SSH安装和密钥生成
需要SSH设置来执行群集上的不同操作，如启动，停止，分布式守护程序shell操作。要验证Hadoop的不同用户，需要为Hadoop用户提供公钥/私钥对，并与不同的用户共享。

以下命令用于使用SSH生成键值对。将公钥表单`id_rsa.pub`复制到`authorized_keys`，并向所有者分别向`authorized_keys`文件提供读写权限。

    $ ssh-keygen -t rsa 
    $ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys 
    $ chmod 0600 ~/.ssh/authorized_keys 

### 安装Java
Java是Hadoop的主要前提。首先，您应该使用命令“java -version”来验证系统中是否存在java。java version命令的语法如下。

    $ java -version 

如果一切顺利，它会给你以下输出。

    java version "1.7.0_71" 
    Java(TM) SE Runtime Environment (build 1.7.0_71-b13) 
    Java HotSpot(TM) Client VM (build 25.0-b02, mixed mode)  

如果您的系统中没有安装java，请按照以下步骤安装java。

**步骤1**
通过访问以下链接http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads1880260.html下载java（JDK <最新版本 - X64.tar.gz）。

那么`jdk-7u71-linux-x64.tar.gz`将被下载到你的系统中。

**第2步**
一般来说，您将在下载文件夹中找到下载的java文件。验证并使用以下命令解压`jdk-7u71-linux-x64.gz`文件。

    $ cd Downloads/ 
    $ ls 
    jdk-7u71-linux-x64.gz 
    $ tar zxf jdk-7u71-linux-x64.gz 
    $ ls 
    jdk1.7.0_71   jdk-7u71-linux-x64.gz 

**步骤3**
为了使java可用于所有用户，您必须将其移动到位置`“/ usr / local /”`。打开root，然后键入以下命令。

```java
$ su 
password: 
# mv jdk1.7.0_71 /usr/local/ 
# exit 

```
**步骤4**
要设置PATH和JAVA_HOME变量，请将以下命令添加到`〜/ .bashrc`文件中。

    export JAVA_HOME=/usr/local/jdk1.7.0_71 
    export PATH=$PATH:$JAVA_HOME/bin 

现在将所有更改应用到当前运行的系统中。

    $ source ~/.bashrc

**步骤5**
使用以下命令配置java替代方案：
```java
# alternatives --install /usr/bin/java java usr/local/java/bin/java 2

# alternatives --install /usr/bin/javac javac usr/local/java/bin/javac 2

# alternatives --install /usr/bin/jar jar usr/local/java/bin/jar 2

# alternatives --set java usr/local/java/bin/java

# alternatives --set javac usr/local/java/bin/javac

# alternatives --set jar usr/local/java/bin/jar

```
现在如上所述从终端验证`java -version`命令。

### 下载Hadoop
使用以下命令从Apache软件基础下载并提取**Hadoop 2.4.1**。
```
$ su 
password: 
# cd /usr/local 
# wget http://apache.claz.org/hadoop/common/hadoop-2.4.1/ 
hadoop-2.4.1.tar.gz 
# tar xzf hadoop-2.4.1.tar.gz 
# mv hadoop-2.4.1/* to hadoop/ 
# exit 

```
### Hadoop操作模式
下载Hadoop后，您可以使用三种支持的模式之一来操作Hadoop集群：

 - **本地/独立模式**：在系统中下载Hadoop后，默认情况下，它以独立模式配置，可以作为单个java进程运行。
 - **伪分布式模式**：单机分布式仿真。每个Hadoop守护进程，如hdfs，Yarn ，MapReduce等都将作为单独的java进程运行。这种模式对于开发是有用的。
 - **完全分布式模式**：这种模式是以最少的两台或多台机器作为集群完全分配的。我们将在下一章中详细介绍这种模式。

### 以独立模式安装Hadoop
这里我们将讨论`Hadoop 2.4.1`在独立模式下的安装。

没有运行守护进程，一切都运行在单个JVM中。独立模式适合在开发过程中运行MapReduce程序，因为它易于测试和调试。

**设置Hadoop**
您可以通过将以下命令附加到〜/ .bashrc文件来设置Hadoop环境变量。

    export HADOOP_HOME=/usr/local/hadoop 

在继续进行之前，您需要确保Hadoop正常工作。只需发出以下命令：

    $ hadoop version 

如果您的设置一切正常，那么您应该看到以下结果：

    Hadoop 2.4.1 
    Subversion https://svn.apache.org/repos/asf/hadoop/common -r 1529768 
    Compiled by hortonmu on 2013-10-07T06:28Z 
    Compiled with protoc 2.5.0
    From source with checksum 79e53ce7994d1628b240f09af91e1af4 

这意味着您的Hadoop的独立模式设置正常。默认情况下，Hadoop被配置为在单个计算机上以非分布式模式运行。

**例**
我们来看一个Hadoop的简单例子。Hadoop安装提供以下示例MapReduce jar文件，该文件提供`MapReduce`的基本功能，可用于计算，如Pi值，给定文件列表中的字数等。

    $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.2.0.jar 

我们有一个输入目录，我们将推送几个文件，我们的要求是计算这些文件中的单词总数。要计算总字数，我们不需要编写我们的MapReduce，前提是.jar文件包含字数的实现。您可以尝试使用相同的.jar文件的其他示例; 只需通过hadoop-mapreduce-examples-2.2.0.jar文件发出以下命令来检查支持的MapReduce功能程序。

    $ hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduceexamples-2.2.0.jar 

**步骤1**
在输入目录中创建临时内容文件。您可以在任何想要工作的地方创建此输入目录。

    $ mkdir input 
    $ cp $HADOOP_HOME/*.txt input 
    $ ls -l input 

它将在您的输入目录中提供以下文件：

    total 24 
    -rw-r--r-- 1 root root 15164 Feb 21 10:14 LICENSE.txt 
    -rw-r--r-- 1 root root   101 Feb 21 10:14 NOTICE.txt
    -rw-r--r-- 1 root root  1366 Feb 21 10:14 README.txt 

这些文件已从Hadoop安装主目录中复制。对于您的实验，您可以拥有不同大小的文件。

**第2步**
让我们开始Hadoop进程来计算输入目录中可用文件中所有文件的总数，如下所示：

    $ hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduceexamples-2.2.0.jar  wordcount input output 

**步骤3**
步骤2将执行所需的处理并将输出保存在`output / part-r00000`文件中，您可以使用以下命令进行检查：

    $cat output/* 

它将列出所有这些单词以及输入目录中所有可用文件中可用的总计数。
```
"AS      4 
"Contribution" 1 
"Contributor" 1 
"Derivative 1
"Legal 1
"License"      1
"License");     1 
"Licensor"      1
"NOTICE”        1 
"Not      1 
"Object"        1 
"Source”        1 
"Work”    1 
"You"     1 
"Your")   1 
"[]"      1 
"control"       1 
"printed        1 
"submitted"     1 
(50%)     1 
(BIS),    1 
(C)       1 
(Don't)   1 
(ECCN)    1 
(INCLUDING      2 
(INCLUDING,     2 
.............

```
### 在虚拟分布式模式下安装Hadoop
按照以下步骤在伪分布式模式下安装Hadoop 2.4.1。

**步骤1：设置Hadoop**
您可以通过将以下命令附加到〜/ .bashrc文件来设置Hadoop环境变量。
```
export HADOOP_HOME=/usr/local/hadoop 
export HADOOP_MAPRED_HOME=$HADOOP_HOME 
export HADOOP_COMMON_HOME=$HADOOP_HOME 
export HADOOP_HDFS_HOME=$HADOOP_HOME 
export YARN_HOME=$HADOOP_HOME 
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native 
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin 
export HADOOP_INSTALL=$HADOOP_HOME 
```
现在将所有更改应用到当前运行的系统中。

    $ source ~/.bashrc 

**步骤2：Hadoop配置**
您可以在`“$ HADOOP_HOME / etc / hadoop”`位置找到所有Hadoop配置文件。需要根据您的Hadoop基础架构对这些配置文件进行更改。

    $ cd $HADOOP_HOME/etc/hadoop

为了在Java中开发Hadoop程序，您必须通过将JAVA_HOME值替换为系统中的java位置来重置hadoop-env.sh文件中的java环境变量。

    export JAVA_HOME=/usr/local/jdk1.7.0_71

以下是您必须编辑以配置Hadoop的文件列表。

**核心的`site.xml`**

该芯-的site.xml文件包含信息，诸如读/写缓冲器的用于Hadoop的实例的端口号，分配给文件系统的存储器，存储器限制，用于存储数据，和大小。

打开**core-site.xml**，并在`<configuration>，</ configuration>`标签之间添加以下属性。
```
<configuration>

   <property>
      <name>fs.default.name </name>
      <value> hdfs://localhost:9000 </value> 
   </property>
 
</configuration>
```
**HDFS-site.xml中**

在HDFS-的site.xml文件中包含的信息，如复制数据的价值，名称节点的路径，你的本地文件系统的数据节点的路径。这意味着您要存储Hadoop基础架构的地方。

让我们假设以下数据。

    dfs.replication (data replication value) = 1 
    (In the below given path /hadoop/ is the user name. 
    hadoopinfra/hdfs/namenode is the directory created by hdfs file system.) 
    namenode path = //home/hadoop/hadoopinfra/hdfs/namenode 
    (hadoopinfra/hdfs/datanode is the directory created by hdfs file system.) 
    datanode path = //home/hadoop/hadoopinfra/hdfs/datanode 

打开此文件，并在此文件的`<configuration> </ configuration>`标签之间添加以下属性。

    <configuration>
    
       <property>
          <name>dfs.replication</name>
          <value>1</value>
       </property>
        
       <property>
          <name>dfs.name.dir</name>
          <value>file:///home/hadoop/hadoopinfra/hdfs/namenode </value>
       </property>
        
       <property>
          <name>dfs.data.dir</name> 
          <value>file:///home/hadoop/hadoopinfra/hdfs/datanode </value> 
       </property>
           
    </configuration>

**注意**：在上述文件中，所有属性值都是用户定义的，您可以根据您的Hadoop基础架构进行更改。

**yarn-site.xml**

该文件用于将Yarn 配置为Hadoop。打开yarn-site.xml文件，并在此文件中的<configuration>，</ configuration>标签之间添加以下属性。

    <configuration>
     
       <property>
          <name>yarn.nodemanager.aux-services</name>
          <value>mapreduce_shuffle</value> 
       </property>
      
    </configuration>

**mapred-site.xml中**

该文件用于指定我们正在使用哪个MapReduce框架。默认情况下，Hadoop包含一个yarn-site.xml模板。首先，需要使用以下命令将该文件从**mapred-site.xml.template**复制到**mapred-site.xml**文件。

    $ cp mapred-site.xml.template mapred-site.xml 

打开mapred-site.xml文件，并在此文件中的<configuration>，</ configuration>标签之间添加以下属性。

    <configuration>
     
       <property> 
          <name>mapreduce.framework.name</name>
          <value>yarn</value>
       </property>
       
    </configuration>

### 验证Hadoop安装
以下步骤用于验证Hadoop安装。

**步骤1：命名节点设置**
使用命令`“hdfs namenode -format”`设置namenode，如下所示。

    $ cd ~ 
    $ hdfs namenode -format 

预期结果如下。
```
10/24/14 21:30:55 INFO namenode.NameNode: STARTUP_MSG: 
/************************************************************ 
STARTUP_MSG: Starting NameNode 
STARTUP_MSG:   host = localhost/192.168.1.11 
STARTUP_MSG:   args = [-format] 
STARTUP_MSG:   version = 2.4.1 
...
...
10/24/14 21:30:56 INFO common.Storage: Storage directory 
/home/hadoop/hadoopinfra/hdfs/namenode has been successfully formatted. 
10/24/14 21:30:56 INFO namenode.NNStorageRetentionManager: Going to 
retain 1 images with txid >= 0 
10/24/14 21:30:56 INFO util.ExitUtil: Exiting with status 0 
10/24/14 21:30:56 INFO namenode.NameNode: SHUTDOWN_MSG: 
/************************************************************ 
SHUTDOWN_MSG: Shutting down NameNode at localhost/192.168.1.11 
************************************************************/
```
**步骤2：验证Hadoop dfs**
以下命令用于启动dfs。执行此命令将启动您的Hadoop文件系统。

    $ start-dfs.sh 

预期产出如下：
```
10/24/14 21:37:56 
Starting namenodes on [localhost] 
localhost: starting namenode, logging to /home/hadoop/hadoop
2.4.1/logs/hadoop-hadoop-namenode-localhost.out 
localhost: starting datanode, logging to /home/hadoop/hadoop
2.4.1/logs/hadoop-hadoop-datanode-localhost.out 
Starting secondary namenodes [0.0.0.0]
```
**步骤3：验证Yarn 脚本**
以下命令用于启动Yarn 脚本。执行此命令将启动您的Yarn 守护程序。

    $ start-yarn.sh 

预期产出如下：
```
starting yarn daemons 
starting resourcemanager, logging to /home/hadoop/hadoop
2.4.1/logs/yarn-hadoop-resourcemanager-localhost.out 
localhost: starting nodemanager, logging to /home/hadoop/hadoop
2.4.1/logs/yarn-hadoop-nodemanager-localhost.out 
```
**步骤4：在浏览器上访问Hadoop**
访问Hadoop的默认端口号为50070.使用以下URL在浏览器上获取Hadoop服务。

**http://localhost:50070/**
在浏览器上访问Hadoop
![](http://osewdhh4j.bkt.clouddn.com/hadoop_on_browser.jpg)
**步骤5：验证集群的所有应用程序**
访问群集所有应用程序的默认端口号为8088.使用以下URL访问此服务。

**http://localhost:8088/**
Hadoop应用程序集群
![](http://osewdhh4j.bkt.clouddn.com/hadoop_application_cluster.jpg)





