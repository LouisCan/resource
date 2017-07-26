---
title: Hadoop - 多节点集群
date: 2017-07-17 20:57:07
tags: hadoop
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/%E5%A4%9A%E8%8A%82%E7%82%B9%E9%9B%86%E7%BE%A4.png
keywords: 大数据,hadoop,多节点集群
description: 本章介绍了分布式环境中Hadoop多节点集群的设置.
---

## Hadoop - 多节点集群

本章介绍了分布式环境中Hadoop多节点集群的设置。

![](http://osewdhh4j.bkt.clouddn.com/%E5%A4%9A%E8%8A%82%E7%82%B9%E9%9B%86%E7%BE%A4.png)

由于整个集群无法演示，我们使用三个系统（一个主站和两个从站）来解释Hadoop集群环境; 下面给出了他们的IP地址。

 - Hadoop Master：192.168.1.15（hadoop-master）
 - Hadoop从站：192.168.1.16（hadoop-slave-1）
 - Hadoop从站：192.168.1.17（hadoop-slave-2）

按照以下步骤进行Hadoop多节点集群的设置。

### 安装Java
Java是Hadoop的主要前提。首先，您应该使用“java -version”来验证系统中是否存在java。java version命令的语法如下。

    $ java -version

如果一切正常，它将给你以下输出。

    java version "1.7.0_71" 
    Java(TM) SE Runtime Environment (build 1.7.0_71-b13) 
    Java HotSpot(TM) Client VM (build 25.0-b02, mixed mode)

如果您的系统中没有安装java，请按照给定的步骤安装java。

**步骤1**
下载java（JDK - X64.tar.gz）访问以下链接http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html

那么jdk-7u71-linux-x64.tar.gz将被下载到你的系统中。

**第2步**
一般来说，您将在下载文件夹中找到下载的java文件。验证并使用以下命令解压jdk-7u71-linux-x64.gz文件。

    $ cd Downloads/
    $ ls
    jdk-7u71-Linux-x64.gz
    $ tar zxf jdk-7u71-Linux-x64.gz
    $ ls
    jdk1.7.0_71 jdk-7u71-Linux-x64.gz

**步骤3**
为了使java可用于所有用户，您必须将其移动到位置“/ usr / local /”。打开根，然后键入以下命令。
```
$ su
password:
# mv jdk1.7.0_71 /usr/local/
# exit

```
**步骤4**
要设置PATH和JAVA_HOME变量，请将以下命令添加到〜/ .bashrc文件中。

    export JAVA_HOME=/usr/local/jdk1.7.0_71
    export PATH=PATH:$JAVA_HOME/bin

现在如上所述从终端验证java -version命令。遵循上述过程，并在所有集群节点中安装java。

### 创建用户帐号

在主系统和从属系统上创建系统用户帐户以使用Hadoop安装。
```
# useradd hadoop 
# passwd hadoop
```
### 映射节点
您必须在所有节点的/ etc /文件夹中编辑主机文件，指定每个系统的IP地址及其主机名。
```
# vi /etc/hosts
enter the following lines in the /etc/hosts file.
192.168.1.109 hadoop-master 
192.168.1.145 hadoop-slave-1 
192.168.56.1 hadoop-slave-2
```
### 配置基于密钥的登录
在每个节点中设置ssh，以便他们可以彼此通信，而无需提示输入密码。
```
# su hadoop 
$ ssh-keygen -t rsa 
$ ssh-copy-id -i ~/.ssh/id_rsa.pub tutorialspoint@hadoop-master 
$ ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop_tp1@hadoop-slave-1 
$ ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop_tp2@hadoop-slave-2 
$ chmod 0600 ~/.ssh/authorized_keys 
$ exit
```
### 安装Hadoop
在主服务器中，使用以下命令下载并安装Hadoop。
```
# mkdir /opt/hadoop 
# cd /opt/hadoop/ 
# wget http://apache.mesi.com.ar/hadoop/common/hadoop-1.2.1/hadoop-1.2.0.tar.gz 
# tar -xzf hadoop-1.2.0.tar.gz 
# mv hadoop-1.2.0 hadoop
# chown -R hadoop /opt/hadoop 
# cd /opt/hadoop/hadoop/
```
**配置Hadoop**
您必须通过进行以下更改来配置Hadoop服务器。

核心的`site.xml`
打开**core-site.xml**文件，如下所示进行编辑。
```
<configuration>
   <property> 
      <name>fs.default.name</name> 
      <value>hdfs://hadoop-master:9000/</value> 
   </property> 
   <property> 
      <name>dfs.permissions</name> 
      <value>false</value> 
   </property> 
</configuration>
```
**HDFS-site.xml中**
打开hdfs-site.xml文件，如下所示进行编辑。
```
<configuration>
   <property> 
      <name>dfs.data.dir</name> 
      <value>/opt/hadoop/hadoop/dfs/name/data</value> 
      <final>true</final> 
   </property> 

   <property> 
      <name>dfs.name.dir</name> 
      <value>/opt/hadoop/hadoop/dfs/name</value> 
      <final>true</final> 
   </property> 

   <property> 
      <name>dfs.replication</name> 
      <value>1</value> 
   </property> 
</configuration>
```
**mapred-site.xml中**
打开mapred-site.xml文件，如下所示进行编辑。
```
<configuration>
   <property> 
      <name>mapred.job.tracker</name> 
      <value>hadoop-master:9001</value> 
   </property> 
</configuration>
```
**hadoop-env.sh**
打开`hadoop-env.sh`文件并编辑JAVA_HOME，HADOOP_CONF_DIR和HADOOP_OPTS，如下所示。

**注意**：根据您的系统配置设置JAVA_HOME。

    export JAVA_HOME=/opt/jdk1.7.0_17 export HADOOP_OPTS=-Djava.net.preferIPv4Stack=true export HADOOP_CONF_DIR=/opt/hadoop/hadoop/conf

### 在从站服务器上安装Hadoop
按照给定的命令，在所有从属服务器上安装Hadoop。
```
# su hadoop 
$ cd /opt/hadoop 
$ scp -r hadoop hadoop-slave-1:/opt/hadoop 
$ scp -r hadoop hadoop-slave-2:/opt/hadoop
```
### 在主服务器上配置Hadoop
打开主服务器并按照给定的命令进行配置。
```
# su hadoop 
$ cd /opt/hadoop/hadoop
```
**配置主节点**

    $ vi etc/hadoop/masters
    hadoop-master

**配置从站节点**

    $ vi etc/hadoop/slaves
    hadoop-slave-1 
    hadoop-slave-2

**Hadoop Master上的格式名称节点**
```
# su hadoop 
$ cd /opt/hadoop/hadoop 
$ bin/hadoop namenode –format
11/10/14 10:58:07 INFO namenode.NameNode: STARTUP_MSG: /************************************************************ 
STARTUP_MSG: Starting NameNode 
STARTUP_MSG: host = hadoop-master/192.168.1.109 
STARTUP_MSG: args = [-format] 
STARTUP_MSG: version = 1.2.0 
STARTUP_MSG: build = https://svn.apache.org/repos/asf/hadoop/common/branches/branch-1.2 -r 1479473; compiled by 'hortonfo' on Mon May 6 06:59:37 UTC 2013 
STARTUP_MSG: java = 1.7.0_71 ************************************************************/ 11/10/14 10:58:08 INFO util.GSet: Computing capacity for map BlocksMap editlog=/opt/hadoop/hadoop/dfs/name/current/edits
………………………………………………….
………………………………………………….
…………………………………………………. 11/10/14 10:58:08 INFO common.Storage: Storage directory /opt/hadoop/hadoop/dfs/name has been successfully formatted. 11/10/14 10:58:08 INFO namenode.NameNode: 
SHUTDOWN_MSG: /************************************************************ SHUTDOWN_MSG: Shutting down NameNode at hadoop-master/192.168.1.15 ************************************************************/
```
### 启动Hadoop服务
以下命令是启动Hadoop-Master上的所有Hadoop服务。

    $ cd $HADOOP_HOME/sbin
    $ start-all.sh

在Hadoop集群中添加新的DataNode
下面给出了将新节点添加到Hadoop集群时要遵循的步骤。

**联网**
使用一些适当的网络配置将新节点添加到现有的Hadoop集群。假设以下网络配置。

对于新节点配置：

    IP address : 192.168.1.103 
    netmask : 255.255.255.0
    hostname : slave3.in

### 添加用户和SSH访问
**添加用户**
在新节点上，添加“hadoop”用户，并使用以下命令将Hadoop用户的密码设置为“hadoop123”或任何您想要的内容。

    useradd hadoop
    passwd hadoop

设置从主机到新从站的密码减少连接。

**在主机上执行以下操作**
```
mkdir -p $HOME/.ssh 
chmod 700 $HOME/.ssh 
ssh-keygen -t rsa -P '' -f $HOME/.ssh/id_rsa 
cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys 
chmod 644 $HOME/.ssh/authorized_keys
Copy the public key to new slave node in hadoop user $HOME directory
scp $HOME/.ssh/id_rsa.pub hadoop@192.168.1.103:/home/hadoop/
```
**在从站上执行以下操作**
登录hadoop。如果没有，请登录hadoop用户。

    su hadoop ssh -X hadoop@192.168.1.103

将公钥的内容复制到文件`“$ HOME / .ssh / authorized_keys”`中，然后通过执行以下命令来更改相同的权限。

    cd $HOME
    mkdir -p $HOME/.ssh 
    chmod 700 $HOME/.ssh
    cat id_rsa.pub >>$HOME/.ssh/authorized_keys 
    chmod 644 $HOME/.ssh/authorized_keys

从主机检查ssh登录。现在检查是否可以将ssh连接到新节点，而不需要主节点的密码。

    ssh hadoop@192.168.1.103 or hadoop@slave3

### 设置新节点的主机名
您可以在文件/ etc / sysconfig / network中设置主机名

    On new slave3 machine
    NETWORKING=yes 
    HOSTNAME=slave3.in

要使更改生效，请重新启动计算机或运行hostname命令到具有相应主机名的新计算机（重新启动是一个很好的选择）。

在slave3节点机上：

主机slave3.in

在群集的所有机器上使用以下行更新/ etc / hosts：

    192.168.1.102 slave3.in slave3

现在尝试用主机名ping机，检查是否解析为IP。

新节点机：

    ping master.in

### 在新节点上启动DataNode
使用$ HADOOP_HOME / bin / hadoop-daemon.sh脚本手动启动datanode守护程序。它将自动联系主（NameNode）并加入群集。我们还应该将新节点添加到主服务器中的conf / slaves文件中。基于脚本的命令将识别新节点。

**登录到新节点**

    su hadoop or ssh -X hadoop@192.168.1.103

使用以下命令在新添加的从节点上启动HDFS

    ./bin/hadoop-daemon.sh start datanode

检查新节点上jps命令的输出。看起来如下

    $ jps
    7141 DataNode
    10312 Jps

### 从Hadoop集群中删除DataNode
当运行时，我们可以在一个群集中即时删除一个节点，而不会丢失任何数据。HDFS提供了一个退役功能，可以确保安全地执行删除节点。要使用它，请按照以下步骤：

**步骤1：登录掌握**
登录到安装Hadoop的主机用户。

    $ su hadoop

**步骤2：更改集群配置**
在启动集群前必须配置排除文件。将一个名为dfs.hosts.exclude的密钥添加到我们的$ HADOOP_HOME / etc / hadoop / hdfs-site.xml文件中。与该键相关联的值提供了NameNode本地文件系统上的文件的完整路径，该文件包含不允许连接到HDFS的计算机列表。

例如，将这些行添加到etc / hadoop / hdfs-site.xml文件中。
```
<property> 
   <name>dfs.hosts.exclude</name> 
   <value>/home/hadoop/hadoop-1.2.1/hdfs_exclude.txt</value> 
   <description>DFS exclude</description> 
</property>
```
**步骤3：确定主机退役**
每台要退役的机器应该被添加到由hdfs_exclude.txt标识的文件中，每行一个域名。这将阻止它们连接到NameNode。的内容“/home/hadoop/hadoop-1.2.1/hdfs_exclude.txt”文件如下所示，如果你想删除DataNode2。

    slave2.in

**步骤4：强制配置重新加载**
运行命令“$ HADOOP_HOME / bin / hadoop dfsadmin -refreshNodes”，不带引号。

    $ $HADOOP_HOME/bin/hadoop dfsadmin -refreshNodes

这将强制NameNode重新读取其配置，包括新更新的“excludes”文件。它将在一段时间内停止节点，从而允许将每个节点的块的时间复制到计划保持活动的机器上。

在slave2.in上，检查jps命令输出。一段时间后，您将看到DataNode进程自动关闭。

**步骤5：关闭节点**
退役过程完成后，退役硬件可以安全关闭进行维护。运行report命令到dfsadmin来检查停用状态。以下命令将描述停用节点和连接的节点到集群的状态。

    enter code here

    $ $HADOOP_HOME/bin/hadoop dfsadmin -report

**步骤6：编辑再次排除文件**
一旦机器已经停用，它们可以从“排除”文件中删除。再次运行`“$ HADOOP_HOME / bin / hadoop dfsadmin -refreshNodes`”会将排除的文件读回到NameNode中; 允许DataNodes在维护完成后重新加入集群，或者再次在集群中需要额外的容量。

特别注意：如果遵循上述过程，并且任务跟踪器进程仍然在节点上运行，则需要关闭它。一种方法是像上面的步骤那样断开机器的连接。主人将自动识别过程，并将其声明为死亡。没有必要遵循相同的过程来删除任务跟踪器，因为它与DataNode相比并不重要。DataNode包含您要安全删除的数据，而不会丢失任何数据。

任务跟踪器可以在任何时候通过以下命令在运行中运行/关闭。

    $ $HADOOP_HOME/bin/hadoop-daemon.sh stop tasktracker $HADOOP_HOME/bin/hadoop-daemon.






