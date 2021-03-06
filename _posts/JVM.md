---
title: JVM
date: 2017-09-05 01:05:51
tags: java
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170805205407.png
keywords: java,JVM
description: VM是Java Virtual Machine（Java虚拟机）的缩写，JVM是一种用于计算设备的规范，它是一个虚构出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。Java语言的一个非常重要的特点就是与平台的无关性。而使用Java虚拟机是实现这一特点的关键。一般的高级语言如果要在不同的平台上运行，至少需要编译成不同的目标代码。而引入Java语言虚拟机后，Java语言在不同平台上运行时不需要重新编译。Java语言使用Java虚拟机屏蔽了与具体平台相关的信息，使得Java语言编译程序只需生成在Java虚拟机上运行的目标代码（字节码），就可以在多种平台上不加修改地运行。Java虚拟机在执行字节码时，把字节码解释成具体平台上的机器指令执行。这就是Java的能够“一次编译，到处运行”的原因。
---

## JVM

JVM是Java Virtual Machine（Java虚拟机）的缩写，JVM是一种用于计算设备的规范，它是一个虚构出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。
Java语言的一个非常重要的特点就是与平台的无关性。而使用Java虚拟机是实现这一特点的关键。一般的高级语言如果要在不同的平台上运行，至少需要编译成不同的目标代码。而引入Java语言虚拟机后，Java语言在不同平台上运行时不需要重新编译。Java语言使用Java虚拟机屏蔽了与具体平台相关的信息，使得Java语言编译程序只需生成在Java虚拟机上运行的目标代码（字节码），就可以在多种平台上不加修改地运行。Java虚拟机在执行字节码时，把字节码解释成具体平台上的机器指令执行。这就是Java的能够“一次编译，到处运行”的原因。

### JAVA内存区域及使用分配

![](http://osewdhh4j.bkt.clouddn.com/20170905145350.png)

**1、程序计数器**
程序计数器（Program CounterRegister）是一块较小的内存空间，它的作用可以看做是当前线程所执行的字节码的行号指示器。此内存区域是唯一一个在Java 虚拟机规范中没有规定任何    OutOfMemoryError 情况的区域。

**2、Java 虚拟机栈**
每个方法被执行的时候都会同时创建一个栈帧(Stack Frame）用于存储局部变量表、操作栈、动态链接、方法出口等信息。每一个方法被调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程。

**3、本地方法栈**
本地方法栈（Native MethodStacks）与虚拟机栈所发挥的作用是非常相似的，其区别不过是虚拟机栈为虚拟机执行Java 方法（也就是字节码）服务，而本地方法栈则是为虚拟机使用到的Native 方法服务。
    
**4、Java 堆**
此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。这一点在Java 虚拟机规范中的描述是：所有的对象实例以及数组都要在堆上分配，不是“绝对”的。
Java堆是垃圾收集器管理的主要区域，按照分代收集算法的划分，堆内存空间可以继续细分为年轻代，老年代。年轻代又可以划分为较大的Eden区，两个同等大小的From Survivor,To Survivor区。默认的Eden区和Survivor区的大小比例为8:1:1，这个比例可以调节。在为新创建的对象分配内存的时候先将对象分配到Eden区和From Survivor区，在立即回收时，会将Eden区和Survivor区还存活的对象复制到To Survivor区中，如果To Survivor区的大小不能容纳存活的对象，会把存活的对象分配到老年区。总体来说，新创建的小对象会放在年轻代，年轻代的对象大多在下一次垃圾回收时被回收，老年代存储大的对象和存活时间长的对象。
![](http://osewdhh4j.bkt.clouddn.com/20170905153736.png)

> - 永久代（Perm）：主要保存class，method，field等对象，该空间大小，取决于系统启动加载类的数量，一般该区域内存溢出均是启动时溢出。java.lang.OutOfMemoryError: PermGen space
> - 老年代（Old）：一般是经过多次垃圾回收（GC）没有被回收掉的对象
> - 新生代（Eden）：新创建的对象，
> - 新生代（Survivor0）：经过垃圾回收（GC）后，没有被回收掉的对象
> - 新生代（Survivor1）：同Survivor0相同，大小空间也相同，同一时刻Survivor0和Survivor1只有一个在用，一个为空

**JVM堆的配置**

> **1. JVM运行时堆的大小** 　　
    -Xms堆的最小值 　　
    -Xmx堆空间的最大值
> **2. 新生代堆空间大小调整** 　　
    -XX:NewSize新生代的最小值 　　
    -XX:MaxNewSize新生代的最大值 　　
    -XX:NewRatio设置新生代与老年代在堆空间的大小
    -XX:SurvivorRatio新生代中Eden所占区域的大小
> **3. 永久代大小调整** 　　
    -XX:MaxPermSize
> **4. 其他** 　  
    -XX:MaxTenuringThreshold,设置将新生代对象转到老年代时需要经过多少次垃圾回收，但是仍然没有被回收

    
**5、方法区（非堆）**
方法区（Method Area）与Java 堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

**6、直接内存**
直接内存（Direct Memory）并不是虚拟机运行时数据区的一部分，也不是Java虚拟机规范中定义的内存区域，但是这部分内存也被频繁地使用，而且也可能导致OutOfMemoryError 异常出现，所以我们放到这里一起讲解。
在JDK 1.4 中新加入了NIO（New Input/Output）类，引入了一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，它可以使用Native函数库直接分配堆外内存，然后通过一个存储在Java堆里面的DirectByteBuffer 对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在Java 堆和Native 堆中来回复制数据。


### GC的三种收集算法的原理和特点，用途，优化思路

三种垃圾收集算法：复制算法，标记-清除算法、标记-整理算法

**标记-清除算法**：首先标记出所有需要回收的对象，标记完成后统一回收所有被标记的对象。缺点：标记和清除两个过程效率都不高；标记清楚后会产生空间碎片，空间碎片导致分配较大对象时可能提前出发垃圾回收。

**复制算法**：将可用内存分为两个区域，每次只使用其中一块，当使用的那一块内存用完时，将还存活的对象复制到另外一块内存中，然后把已使用过的内存空间一次清理掉。优点：解决的空间碎片问题，实现简单。缺点：将内存缩小为两块，内存使用率不高。复制操作频繁效率变低。

**标记-整理算法**：可回收对象标记后，让所有存活的对象向一端移动，然后清理掉边界以外的内存。优点：不会产生空间碎片，比复制算法提高了内存空间利用率。

`复制算法用在年轻代的垃圾回收中，标记整理和标记清除算法用在老年代垃圾回收的收集器中`。

### 类加载过程：加载、验证、准备、解析、初始化

虚拟机的类加载机制就是把描述类的数据从Class文件（或者其他途径）加载到内存，并对数据进行校验、转换解析和初始化，最终形成可以被虚拟机直接使用的Java类型。

**加载**：
    1.通过一个类的全限定名获取定义此类的二进制字节流
    2、将这个字节流所戴晓的静态结构转化为方法区的运行时数据结构
    3、在内存中生成一个代表这个类的Class对象，作为方法区这个类的各种数据的访问入口。

**验证**：
1、文件格式验证，保证输入的字节流在格式上符合Class文件的格式规范，保证输入的字节流能正确的解析，只有通过这个验证，字节流才会存储在方法区之内
2、元数据验证，对类的元数据进行语义校验，保证类描述的信息符合Java语言规范。比如验证类的是否实现了父类或者接口中的方法等
3、字节码验证，通过数据流和控制流的分析，确保类的方法符合逻辑，不会在运行时对虚拟机产生危害
4、符号引用校验，发生在解析阶段，确保解析阶段将符号引用转化为直接饮用的正常执行。

**准备**：正式为类变量（static）分配内存，并设置类变量初始值（数据类型的零值），这些变量所使用的内存在方法区中分配。

**解析**：虚拟机将常量池内的符号引用转化为直接饮用，解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用点限定符7类符号引用进行。

**初始化**：初始化阶段才真正执行类中定义的Java代码，初始化阶段是执行类构造器<init>方法的过程。<init>方法（类构造器）是由编译器自动收集类中的静态变量和静态代码块合并产生的。子类和父类的初始化过程优先级为：父类类构造器->子类类构造器->父类对象构造函数->子类对象构造函数。类中静态类变量和静态代码块是按照在类中定义的顺序执行的。

### jstat 命令查看jvm的GC情况

语法结构：

```
Usage: jstat -help|-options
       jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]
```

参数解释：
Options — 选项，我们一般使用 -gcutil 查看gc情况
vmid    — VM的进程号，即当前运行的java进程号
interval– 间隔时间，单位为秒或者毫秒
count   — 打印次数，如果缺省则打印无数次

![](http://osewdhh4j.bkt.clouddn.com/20170905162346.png)
S0C：年轻代中第一个survivor（幸存区）的容量 (字节)         
S1C：年轻代中第二个survivor（幸存区）的容量 (字节)         
S0U：年轻代中第一个survivor（幸存区）目前已使用空间 (字节)         
S1U：年轻代中第二个survivor（幸存区）目前已使用空间 (字节)         
EC：年轻代中Eden（伊甸园）的容量 (字节)         
EU：年轻代中Eden（伊甸园）目前已使用空间 (字节)         
OC：Old代的容量 (字节)         
OU：Old代目前已使用空间 (字节)         
PC：Perm(持久代)的容量 (字节)         
PU：Perm(持久代)目前已使用空间 (字节)         
YGC：从应用程序启动到采样时年轻代中gc次数         
YGCT：从应用程序启动到采样时年轻代中gc所用时间(s)         
FGC：从应用程序启动到采样时old代(全gc)gc次数         
FGCT：从应用程序启动到采样时old代(全gc)gc所用时间(s)         
GCT：从应用程序启动到采样时gc用的总时间(s)   

![](http://osewdhh4j.bkt.clouddn.com/20170905162455.png)

S0  — Heap上的 Survivor space 0 区已使用空间的百分比
S1  — Heap上的 Survivor space 1 区已使用空间的百分比
E   — Heap上的 Eden space 区已使用空间的百分比
O   — Heap上的 Old space 区已使用空间的百分比
P   — Perm space 区已使用空间的百分比
YGC — 从应用程序启动到采样时发生 Young GC 的次数
YGCT– 从应用程序启动到采样时 Young GC 所用的时间(单位秒)
FGC — 从应用程序启动到采样时发生 Full GC 的次数
FGCT– 从应用程序启动到采样时 Full GC 所用的时间(单位秒)
GCT — 从应用程序启动到采样时用于垃圾回收的总时间(单位秒)

其它参数：
NGCMN：年轻代(young)中初始化(最小)的大小 (字节)         
NGCMX：年轻代(young)的最大容量 (字节)         
NGC：年轻代(young)中当前的容量 (字节)         
OGCMN：old代中初始化(最小)的大小 (字节)         
OGCMX：old代的最大容量 (字节)         
OGC：old代当前新生成的容量 (字节)         
PGCMN：perm代中初始化(最小)的大小 (字节)         
PGCMX：perm代的最大容量 (字节)           
PGC：perm代当前新生成的容量 (字节)    
S0：年轻代中第一个survivor（幸存区）已使用的占当前容量百分比         
S1：年轻代中第二个survivor（幸存区）已使用的占当前容量百分比         
E：年轻代中Eden（伊甸园）已使用的占当前容量百分比         
O：old代已使用的占当前容量百分比         
P：perm代已使用的占当前容量百分比         
S0CMX：年轻代中第一个survivor（幸存区）的最大容量 (字节)         
S1CMX ：年轻代中第二个survivor（幸存区）的最大容量 (字节)         
ECMX：年轻代中Eden（伊甸园）的最大容量 (字节)         
DSS：当前需要survivor（幸存区）的容量 (字节)（Eden区已满）         
TT： 持有次数限制         
MTT ： 最大持有次数限制

### Jstack/Jmap .... 有时间更新










