---
title: Web压力测试工具webbench
date: 2017-07-05 21:39:47
tags: 测试
categories: 测试
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170705214256.png
blogexcerpt: webbench首先fork出多个子进程，每个子进程都循环做web访问测试。子进程把访问的结果通过pipe告诉父进程，父进程做最终的统计结果。
---

## 基本原理

 webbench首先fork出多个子进程，每个子进程都循环做web访问测试。子进程把访问的结果通过pipe告诉父进程，父进程做最终的统计结果。

### 安装webbench

```
wget http://www.ha97.com/code/webbench-1.5.tar.gz
tar zxvf webbench-1.5.tar.gz 
cd webbench-1.5
make
sudo make install
```

安装后输入`webbench`

![webbench](http://osewdhh4j.bkt.clouddn.com/20170705214956.png)


### 测试

webbench [option]... URL
-t ： 运行webbench的时间。
-c ： 子进程的个数。
-f ： 不等待返回结果。
-h ： 帮助。


```
webbench -c 100 -t 60 http://blogxinxiucan.sh1.newtouch.com/
```

![测试结果](http://osewdhh4j.bkt.clouddn.com/20170705215440.png)


**说明**
Speed：一分钟请求1292次，每秒处理数据量：498516字节
Requests：处理的请求中成功1292，失败0











