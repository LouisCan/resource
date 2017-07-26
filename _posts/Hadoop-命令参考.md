---
title: Hadoop - 命令参考
date: 2017-07-17 20:01:37
tags: hadoop
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/hadoop%E5%91%BD%E4%BB%A4.png
keywords: 大数据,hadoop,hadoop命令
description: 在`“$ HADOOP_HOME / bin / hadoop fs”`中有更多的命令比这里演示的更多，尽管这些基本操作将让您开始。运行./bin/hadoop dfs，没有其他参数将列出可以使用FsShell系统运行的所有命令。此外，如果您遇到困难，$ HADOOP_HOME / bin / hadoop fs -help commandName将显示有关操作的简短使用摘要。
---

## Hadoop - 命令参考

> 在`“$ HADOOP_HOME / bin / hadoop fs”`中有更多的命令比这里演示的更多，尽管这些基本操作将让您开始。运行./bin/hadoop dfs，没有其他参数将列出可以使用FsShell系统运行的所有命令。此外，如果您遇到困难，$ HADOOP_HOME / bin / hadoop fs -help commandName将显示有关操作的简短使用摘要。

![](http://osewdhh4j.bkt.clouddn.com/hadoop%E5%91%BD%E4%BB%A4.png)

所有操作的表格如下所示。以下约定用于参数：

    "<path>" means any file or directory name. 
    "<path>..." means one or more file or directory names. 
    "<file>" means any filename. 
    "<src>" and "<dest>" are path names in a directed operation. 
    "<localSrc>" and "<localDest>" are paths as above, but on the local file system. 

所有其他文件和路径名称都是指HDFS内的对象。

no|详细信息
----|----
1。	| **ls <path>** 列出由path指定的目录的内容，显示每个条目的名称，权限，所有者，大小和修改日期。
2。	| **lsr <path>** 行为像-ls，但递归显示路径的所有子目录中的条目。
3。	| **du <path>** 显示与路径匹配的所有文件的磁盘使用情况（以字节为单位）使用完整的HDFS协议前缀报告文件名。
4。	| **dus <path>** 如-du，但打印路径中所有文件/目录的磁盘使用情况的摘要。
5。	| **mv <src> <dest>** 在HDFS中将src指定的文件或目录移动到dest。
6。	| **cp <src> <dest>** 在HDFS中将src标识的文件或目录复制到dest。
7。	| **rm <path>** 删除由路径识别的文件或空目录。
8。	| **rmr <path>** 删除由路径识别的文件或目录。递归删除任何子条目（即路径的文件或子目录）。
9。	| **put <localSrc> <dest>** 将文件或目录从localSrc标识的本地文件系统复制到DFS内的dest。

no|详细信息
----|----
10。|	 **copyFromLocal <localSrc> <dest>** 相同的输入
11。|	 **moveFromLocal <localSrc> <dest>** 将文件或目录从localSrc标识的本地文件系统复制到HDFS内的dest，然后成功删除本地副本。
12。|	 **get [-crc] <src> <localDest>** 将由src标识的HDFS中的文件或目录复制到由localDest标识的本地文件系统路径。
13。|	 **getmerge <src> <localDest>** 检索与HDFS中的路径src匹配的所有文件，并将其复制到由localDest标识的本地文件系统中的单个合并文件。
14。|	 **cat <filen-ame>** 在stdout上显示文件名的内容。
15。|	 **copyToLocal <src> <localDest>** 与-get相同
16。|	 **moveToLocal <src> <localDest>** 像--get一样工作，但成功删除了HDFS副本。
17。|	 **mkdir <path>** 在HDFS中创建一个名为path的目录。 创建路径中缺少的任何父目录（例如，Linux中的mkdir -p）。
18。|	 **setrep [-R] [-w] rep <path>** 为通过代码的路径标识的文件设置目标复制因子。（实际的复制因素会随着时间的推移朝向目标）
19。|	 **touchz <path>** 在包含当前时间的路径上创建一个文件作为时间戳。如果文件已存在于路径中，则失败，除非该文件已经是大小0。
20。|	 **test - [ezd] <path>** 如果路径存在则返回1; 长度为零 或者是目录，否则为0。
21。|	 **stat [format] <path>** 打印有关路径的信息。格式是以块（％b），文件名（％n），块大小（％o），复制（％r）和修改日期（％y，％Y））接受文件大小的字符串。
22。|	 **tail [-f] <file2name>** 显示stdout上最后1KB的文件。
23。|	 **chmod [-R] mode,mode,... <path>...** 更改与路径标识的一个或多个对象相关联的文件权限。以R.模式递归执行更改为3位八进制模式，或{augo} +/- {rwxX}。假设没有指定范围，并且不应用umask。
24。|	 **chown [-R] [owner] [：[group]] <path> ...** 设置由path ....标识的文件或目录的拥有用户和/或组。如果指定了-R，则递归设置所有者。
25。|	 **chgrp [-R] group <path> ...** 设置由path ....标识的文件或目录的所有组。如果指定了-R，则递归设置组。
26。|	 **help <cmd-name>** 返回上面列出的其中一个命令的使用信息。你必须在cmd中省略前导的' - '字符。




