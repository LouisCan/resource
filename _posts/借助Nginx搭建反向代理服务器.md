---
title: 借助Nginx搭建反向代理服务器
date: 2017-09-08 23:44:00
tags: 网络
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170908235002.png
keywords: 借助Nginx搭建反向代理服务器
description: 反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个服务器。
---

## 借助Nginx搭建反向代理服务器

【大型网站技术实践】初级篇：借助Nginx搭建反向代理服务器

### 一、反向代理：Web服务器的“经纪人”
**1.1 反向代理初印象**
反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个服务器。

![](http://osewdhh4j.bkt.clouddn.com/20170908232233.png)

　　从上图可以看出：反向代理服务器位于网站机房，代理网站Web服务器接收Http请求，对请求进行转发。

**1.2 反向代理的作用**
　　①保护网站安全：任何来自Internet的请求都必须先经过代理服务器；

![](http://osewdhh4j.bkt.clouddn.com/20170908232247.png)

　　②通过配置缓存功能加速Web请求：可以缓存真实Web服务器上的某些静态资源，减轻真实Web服务器的负载压力；

![](http://osewdhh4j.bkt.clouddn.com/20170908232259.png)

　　③实现负载均衡：充当负载均衡服务器均衡地分发请求，平衡集群中各个服务器的负载压力；
　　![](http://osewdhh4j.bkt.clouddn.com/20170908232312.png)

lb

### 二、初识Nginx：简单却不平凡

**2.1 Nginx是神马？**


　　**Nginx**是一款轻量级的网页服务器、反向代理器以及电子邮件代理服务器。其将源代码以类BSD许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。

> Source：Nginx（发音同engine x），它是由俄罗斯程序员Igor Sysoev所开发的。起初是供俄国大型的门户网站及搜索引擎Rambler（俄语：Рамблер）使用。此软件BSD-like协议下发行，可以在UNIX、GNU/Linux、BSD、Mac OS X、Solaris，以及Microsoft Windows等操作系统中运行。

　　说到Web服务器，Apache服务器和IIS服务器是两大巨头；但是运行速度更快、更灵活的对手：Nginx 正在迎头赶上。

**2.2 Nginx的应用现状**
　　Nginx 已经在俄罗斯最大的门户网站── Rambler Media（www.rambler.ru）上运行了3年时间，同时俄罗斯超过20%的虚拟主机平台采用Nginx作为反向代理服务器。

![](http://osewdhh4j.bkt.clouddn.com/20170908232643.png)

　　在国内，已经有 淘宝、新浪博客、新浪播客、网易新闻、六间房、56.com、Discuz!、水木社区、豆瓣、YUPOO、海内、迅雷在线 等多家网站使用 Nginx 作为Web服务器或反向代理服务器。

**2.3 Nginx的核心特点**
　　**（1）跨平台**：Nginx 可以在大多数 Unix like OS编译运行，而且也有Windows的移植版本；

　　**（2）配置异常简单**：非常容易上手。配置风格跟程序开发一样，神一般的配置；

　　**（3）非阻塞、高并发连接**：数据复制时，磁盘I/O的第一阶段是非阻塞的。官方测试能够支撑5万并发连接，在实际生产环境中跑到2～3万并发连接数。（这得益于Nginx使用了最新的epoll模型）；

> PS：对于一个Web服务器来说，首先看一个请求的基本过程：建立连接---接收数据---发送数据，在系统底层看来 ：上述过程（建立连接---接收数据---发送数据）在系统底层就是读写事件。

> **①**如果采用阻塞调用的方式，当读写事件没有准备好时，必然不能够进行读写事件，那么久只好等待，等事件准备好了，才能进行读写事件，那么请求就会被耽搁 。

> **②**既然没有准备好阻塞调用不行，那么采用非阻塞调用方式。非阻塞就是：事件马上返回，告诉你事件还没准备好呢，你慌什么，过会再来吧。好吧，你过一会，再来检查一下事件，直到事件准备好了为止，在这期间，你就可以先去做其它事情，然后再来看看事件好了没。虽然不阻塞了，但你得不时地过来检查一下事件的状态，你可以做更多的事情了，但带来的开销也是不小的。

　　**（4）事件驱动**：通信机制采用epoll模型，支持更大的并发连接。

> ①非阻塞通过不断检查事件的状态来判断是否进行读写操作，这样带来的开销很大，因此就有了异步非阻塞的事件处理机制。这种机制让你可以同时监控多个事件，调用他们是阻塞的，但可以设置超时时间，在超时时间之内，如果有事件准备好了，就返回。这种机制解决了上面阻塞调用与非阻塞调用的两个问题。

> ②以epoll模型为例：当事件没有准备好时，就放入epoll(队列)里面。如果有事件准备好了，那么就去处理；如果事件返回的是EAGAIN，那么继续将其放入epoll里面。从而，只要有事件准备好了，我们就去处理它，只有当所有事件都没有准备好时，才在epoll里面等着。这样，我们就可以并发处理大量的并发了，当然，这里的并发请求，是指未处理完的请求，线程只有一个，所以同时能处理的请求当然只有一个了，只是在请求间进行不断地切换而已，切换也是因为异步事件未准备好，而主动让出的。这里的切换是没有任何代价，你可以理解为循环处理多个准备好的事件，事实上就是这样的。

> ③与多线程方式相比，这种事件处理方式是有很大的优势的，不需要创建线程，每个请求占用的内存也很少，没有上下文切换，事件处理非常的轻量级，并发数再多也不会导致无谓的资源浪费（上下文切换）。对于IIS服务器，每个请求会独占一个工作线程，当并发数上到几千时，就同时有几千的线程在处理请求了。这对操作系统来说，是个不小的挑战：因为线程带来的内存占用非常大，线程的上下文切换带来的cpu开销很大，自然性能就上不去，从而导致在高并发场景下性能下降严重。

> **总结：通过异步非阻塞的事件处理机制，Nginx实现由进程循环处理多个准备好的事件，从而实现高并发和轻量级。** 

　　**（5）Master/Worker结构**：一个master进程，生成一个或多个worker进程。

![](http://osewdhh4j.bkt.clouddn.com/20170908232841.png)

> PS：Master-Worker设计模式核心思想是将原来串行的逻辑并行化，并将逻辑拆分成很多独立模块并行执行。其中主要包含两个主要组件Master和Worker，Master主要将逻辑进行拆分，拆分为互相独立的部分，同时维护了Worker队列，将每个独立部分下发到多个Worker并行执行，Worker主要进行实际逻辑计算，并将结果返回给Master。

> **问**：nginx采用这种进程模型有什么好处？

> **答**：采用独立的进程，可以让互相之间不会影响，一个进程退出后，其它进程还在工作，服务不会中断，Master进程则很快重新启动新的Worker进程。当然，Worker进程的异常退出，肯定是程序有bug了，异常退出，会导致当前Worker上的所有请求失败，不过不会影响到所有请求，所以降低了风险。

　　**（6）内存消耗小**：处理大并发的请求内存消耗非常小。在3万并发连接下，开启的10个Nginx 进程才消耗150M内存（15M*10=150M）。

　　**（7）内置的健康检查功能**：如果 Nginx 代理的后端的某台 Web 服务器宕机了，不会影响前端访问。

　　**（8）节省带宽**：支持 GZIP 压缩，可以添加浏览器本地缓存的 Header 头。

　　**（9）稳定性高**：用于反向代理，宕机的概率微乎其微。
　　
　

### 三、构建实战：Nginx+IIS构筑Web服务器集群的负载均衡

　　这里我们主要在Windows环境下，通过将同一个Web网站部署到不同服务器的IIS上，再通过一个统一的Nginx反响代理服务器对外提供统一访问接入，实现一个最简化的反向代理和负载均衡服务。但是，受限于实验条件，我们这里主要在一台计算机上进行反向代理、IIS集群的模拟，具体的实验环境如下图所示：我们将nginx服务和web网站都部署在一台计算机上，nginx监听http80端口，而web网站分别以不同的端口号（这里是8050及8060）部署在同一个IIS服务器上，用户访问localhost时，nginx作为反向代理将请求均衡地转发给两个IIS中不同端口的Web应用程序进行处理。虽然实验环境很简单而且有限，但是对于一个简单的负载均衡效果而言，本文是可以达到并且展示的。
　![](http://osewdhh4j.bkt.clouddn.com/20170908233034.png)


**3.1 准备一个ASP.NET网站部署到IIS服务器集群中**
　　（1）在VS中新建一个ASP.NET Web应用程序，但是为了在一台计算机上展示效果，我们将这个Web程序复制一份，并修改两个Web程序的Default.aspx，让其的首页显示不同的一点信息。这里Web1展示的是“The First Web：”，而Web2展示的则是“The Second Web”。

![](http://osewdhh4j.bkt.clouddn.com/20170908233116.png)


　　（2）调试运行，看看两个网站的效果如何？

　　①Web1的展示效果：
![](http://osewdhh4j.bkt.clouddn.com/20170908233149.png)


　　②Web2的展示效果：

![](http://osewdhh4j.bkt.clouddn.com/20170908233201.png)

　　③部署到IIS中，分配不同的端口号：这里我选择了Web1:8050，Web2:8060

![](http://osewdhh4j.bkt.clouddn.com/20170908233212.png)

　　（3）**总结：在真实环境中，构建Web应用服务器集群的实现是将同一个Web应用程序部署到Web服务器集群中的多个Web服务器上。**

**3.2 下载Nginx并部署到服务器中作为自启动的Windows服务**
　　（1）到Nginx官网下载Nginx的Windows版本：http://nginx.org/en/download.html（这里我们使用nginx/Windows-1.4.7版本进行实验，本文底部有下载地址）

　　（2）解压到磁盘任意目录，例如这里我解压到了：D:\Servers\nginx-1.4.7

　　（3）启动、停止和重新加载服务：通过cmd以守护进程方式启动`nginx.exe：start nginx.exe`，停止服务：`nginx -s stop`，重新加载配置：`nginx -s  reload`；



　　（4）每次以cmd方式启动Nginx服务不符合实际要求，于是我们想到将其注册为Windows服务，并设置为自动启动模式。这里，我们使用一个不错的小程序：“Windows Service Wrapper”，将nginx.exe注册为Windows服务，具体的步凑如下：

　　①下载最新版的 Windows Service Wrapper 程序，比如我下载的名称是 "winsw-1.8-bin.exe"（本文底部有下载地址），然后把它命名成你想要的名字（比如: "nginx-service.exe"，当然，你也可以不改名）

　　②将重命名后的 nginx-service.exe 复制到 nginx 的安装目录（比如，我这里是 "D:\Servers\nginx-1.4.7"）

　　③在同一个目录下创建一个Windows Service Wrapper 的XML配置文件，名称必须与第一步重命名时使用的名称一致（比如我这里是 "nginx-service.xml",  如果，你没有重命名，则应该是 "winsw-1.8-bin.xml"），这个XML的内容如下：

```
<?xml version="1.0" encoding="UTF-8" ?>
<service>
<id>nginx</id>
<name>Nginx Service</name>
<description>High Performance Nginx Service</description>
<executable>D:\Servers\nginx-1.4.7\nginx.exe</executable>
<logpath>D:\Servers\nginx-1.4.7\</logpath>
<logmode>roll</logmode>
<depend></depend>
<startargument>-p D:\Servers\nginx-1.4.7</startargument>
<stopargument>-p D:\Servers\nginx-1.4.7 -s stop</stopargument>
</service>
```


　　④在命令行下执行以下命令，以便将其注册成Windows服务：`nginx-service.exe install`



　　⑤接下来就可以在Windows服务列表看到Nginx服务了，这里我们可以将其设置为自动启动了：

![](http://osewdhh4j.bkt.clouddn.com/20170908233534.png)


　　（5）总结：在Windows环境中，要对外提供的Windows服务一般都要将其启动类型设置为自动。

### 3.3 修改Nginx核心配置文件nginx.conf
　　（1）进程数与每个进程的最大连接数：

　　　　•nginx进程数，建议设置为等于CPU总核心数
　　　　
　　　　•单个进程最大连接数，那么该服务器的最大连接数=连接数*进程数

![](http://osewdhh4j.bkt.clouddn.com/20170908233635.png)

　　（2）Nginx的基本配置：

　　　　•监听端口一般都为http端口：80;
　　　　•域名可以有多个，用空格隔开：例如 server_name www.ha97.com ha97.com;

![](http://osewdhh4j.bkt.clouddn.com/20170908233647.png)
　　（3）负载均衡列表基本配置：

　　　　•location / {}：对aspx后缀的进行负载均衡请求，假如我们要对所有的aspx后缀的文件进行负载均衡时，可以这样写：location ~ .*\.aspx$ {}

　　　　•proxy_pass：请求转向自定义的服务器列表，这里我们将请求都转向标识为http://cuitccol.com的负载均衡服务器列表；


![](http://osewdhh4j.bkt.clouddn.com/20170908233657.png)
　　　　•在负载均衡服务器列表的配置中，weight是权重，可以根据机器配置定义权重（如果某台服务器的硬件配置十分好，可以处理更多的请求，那么可以为其设置一个比较高的weight；而有一台的服务器的硬件配置比较差，那么可以将前一台的weight配置为weight=2，后一台差的配置为weight=1）。weigth参数表示权值，权值越高被分配到的几率越大；


![](http://osewdhh4j.bkt.clouddn.com/20170908233707.png)

　　（4）**总结：最基本的Nginx配置差不多就是上面这些内容，当然仅仅是最基础的配置。（详细的配置内容请下载底部的nginx-1.4.7详细查看）**

**3.4 添加Nginx对于静态文件的缓存配置**
　　为了提高响应速度，减轻真实服务器的负载，对于静态资源我们可以在反向代理服务器中进行缓存，这也是反向代理服务器的一个重要的作用。

　　（1）缓存静态资源之图片文件

　　root /nginx-1.4.7/staticresources/image：对于配置中提到的jpg/png等文件均定为到/nginx-1.4.7/staticresources/image文件夹中进行寻找匹配并将文件返回；

　　expires 7d：过期时效为7天，静态文件不怎么更新，过期时效可以设大一点，如果频繁更新，则可以设置得小一点；

　　TIPS：下面的样式、脚本缓存配置同这里一样，只是定位的文件夹不一样而已，不再赘述。

![](http://osewdhh4j.bkt.clouddn.com/20170908233948.png)

　　（2）缓存静态资源之样式文件


![](http://osewdhh4j.bkt.clouddn.com/20170908233957.png)

　　（3）缓存静态资源之脚本文件


![](http://osewdhh4j.bkt.clouddn.com/20170908234007.png)

　　（4）在nginx服务文件夹中创建静态资源文件夹，并要缓存的静态文件拷贝进去：这里我主要将Web程序中用到的image、css以及js文件拷贝了进去；


![](http://osewdhh4j.bkt.clouddn.com/20170908234018.png)

　　（5）**总结：通过配置静态文件的缓存设置，对于这些静态文件的请求可以直接从反向代理服务器中直接返回，而无需再将这些静态资源请求转发到具体的Web服务器进行处理了，可以提高响应速度，减轻真实Web服务器的负载压力。**




**学习小结**

　　**在本文中，借助了Nginx这个神器简单地在Windows环境下搭建了一个反向代理服务，并模拟了一个IIS服务器集群的负载均衡效果。从这个DEMO中，我们可以简单地感受到反向代理为我们所做的事情，并体会负载均衡是怎么一回事。但是，在目前大多数的应用中，都会将Nginx部署在Linux服务器中，并且会做一些针对负载均衡的优化配置，这里我们所做的仅仅就是一个小小的使用而已（just修改一下配置文件）。不过，万丈高楼平地起，前期的小小体会，也会帮助我们向后期的深入学习奠定一点点的基础。**


参考资料

（1）丁胖胖，《图解正向代理、反向代理与透明代理》：http://z00w00.blog.51cto.com/515114/1031287

（2）特种兵-AK47，《正向代理与反向代理的区别》：http://blog.csdn.net/m13666368773/article/details/8060481

（3）百度百科，Nginx：http://baike.baidu.com/view/926025.htm?fr=aladdin

（4）51CTO，《Nginx安装配置与服务搭建专题》：http://os.51cto.com/art/201111/304611.htm

（5）红黑联盟，《Nginx配置文件nginx.conf中文详解总结》：http://www.2cto.com/os/201212/176520.html

（6）360doc，《Linux下同步模式、异步模式、阻塞调用与非阻塞调用总结》：http://www.360doc.com/content/13/0117/12/5073814_260691714.shtml （好文一篇，值得阅读）

（7）e路相扶，《同步、异步、阻塞与非阻塞》：http://www.cnblogs.com/zhangjun516/archive/2013/04/17/3025902.html

（8）feitianxuxue，《处理大并发之对异步非阻塞的理解》：http://blog.csdn.net/feitianxuxue/article/details/8936802

附件下载

（1）nginx-1.4.7：http://pan.baidu.com/s/1dD2C2zB

（2）winsw-1.8-bin.exe：http://pan.baidu.com/s/1kTihzk7

（3）SimpleNginxDemo：http://pan.baidu.com/s/1bnq5oYz


### 原文链接

[http://www.cnblogs.com/edisonchou/p/4126742.html](http://www.cnblogs.com/edisonchou/p/4126742.html)


