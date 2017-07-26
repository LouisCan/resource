---
title: Java - 线程同步
date: 2017-07-16 18:12:57
tags: 多线程
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/java-%E7%BA%BF%E7%A8%8B%E5%90%8C%E6%AD%A5.png
keywords: Java - 线程同步,多线程,java
description: 当我们在程序中启动两个或多个线程时，可能会出现多个线程尝试访问同一个资源的情况，最后可能由于并发问题而产生不可预见的结果。例如，如果多个线程尝试在同一个文件中写入，那么它们可能会损坏数据，因为其中一个线程可以覆盖数据，或者当一个线程打开同一个文件时，另一个线程可能会关闭相同的文件。因此，需要同步多个线程的动作，并确保只有一个线程可以在给定时间点访问资源。这是使用称为监视器的概念实现的。
---


## Java - 线程同步

> 当我们在程序中启动两个或多个线程时，可能会出现多个线程尝试访问同一个资源的情况，最后可能由于并发问题而产生不可预见的结果。例如，如果多个线程尝试在同一个文件中写入，那么它们可能会损坏数据，因为其中一个线程可以覆盖数据，或者当一个线程打开同一个文件时，另一个线程可能会关闭相同的文件。

![](http://osewdhh4j.bkt.clouddn.com/java-%E7%BA%BF%E7%A8%8B%E5%90%8C%E6%AD%A5.png)

**因此，需要同步多个线程的动作，并确保只有一个线程可以在给定时间点访问资源。这是使用称为监视器的概念实现的。**Java中的每个对象与监视器相关联，线程可以锁定或解锁。一次只能有一个线程可能会在监视器上锁定。

Java编程语言提供了一种非常方便的创建线程并通过使用同步块同步其任务。您可以在此块内保留共享资源。以下是synchronized语句的一般形式 -

句法

    synchronized(objectidentifier) {
       // Access shared variables and other shared resources
    }

在这里，为**ObjectIdentifier**是一个对象，其锁与同步语句代表监视器相关联的参考。现在我们将看到两个例子，我们将使用两个不同的线程打印一个计数器。当线程不同步时，它们打印不按顺序的计数器值，但是当我们通过放入`synchronized（）`块打印计数器时，它会对两个线程依次打印计数器。

### 没有同步的多线程示例
这是一个简单的例子，它可以或不可能按顺序打印计数器值，并且每次运行它时，它会根据线程的CPU可用性产生不同的结果。

**例**
```java
class PrintDemo {
   public void printCount() {
      try {
         for(int i = 5; i > 0; i--) {
            System.out.println("Counter   ---   "  + i );
         }
      }catch (Exception e) {
         System.out.println("Thread  interrupted.");
      }
   }
}

class ThreadDemo extends Thread {
   private Thread t;
   private String threadName;
   PrintDemo  PD;

   ThreadDemo( String name,  PrintDemo pd) {
      threadName = name;
      PD = pd;
   }
   
   public void run() {
      PD.printCount();
      System.out.println("Thread " +  threadName + " exiting.");
   }

   public void start () {
      System.out.println("Starting " +  threadName );
      if (t == null) {
         t = new Thread (this, threadName);
         t.start ();
      }
   }
}

public class TestThread {
   public static void main(String args[]) {

      PrintDemo PD = new PrintDemo();

      ThreadDemo T1 = new ThreadDemo( "Thread - 1 ", PD );
      ThreadDemo T2 = new ThreadDemo( "Thread - 2 ", PD );

      T1.start();
      T2.start();

      // wait for threads to end
         try {
         T1.join();
         T2.join();
      }catch( Exception e) {
         System.out.println("Interrupted");
      }
   }
}
```
每次运行此程序时，都会产生不同的结果 -

**Output**

    Starting Thread - 1
    Starting Thread - 2
    Counter   ---   5
    Counter   ---   4
    Counter   ---   3
    Counter   ---   5
    Counter   ---   2
    Counter   ---   1
    Counter   ---   4
    Thread Thread - 1  exiting.
    Counter   ---   3
    Counter   ---   2
    Counter   ---   1
    Thread Thread - 2  exiting.

### 具有同步功能的多线程示例
这是同样的例子，它依次打印计数器值，每次运行它时，都产生相同的结果。

**例**
```java
class PrintDemo {
   public void printCount() {
      try {
         for(int i = 5; i > 0; i--) {
            System.out.println("Counter   ---   "  + i );
         }
      }catch (Exception e) {
         System.out.println("Thread  interrupted.");
      }
   }
}

class ThreadDemo extends Thread {
   private Thread t;
   private String threadName;
   PrintDemo  PD;

   ThreadDemo( String name,  PrintDemo pd) {
      threadName = name;
      PD = pd;
   }
   
   public void run() {
      synchronized(PD) {
         PD.printCount();
      }
      System.out.println("Thread " +  threadName + " exiting.");
   }

   public void start () {
      System.out.println("Starting " +  threadName );
      if (t == null) {
         t = new Thread (this, threadName);
         t.start ();
      }
   }
}

public class TestThread {

   public static void main(String args[]) {
      PrintDemo PD = new PrintDemo();

      ThreadDemo T1 = new ThreadDemo( "Thread - 1 ", PD );
      ThreadDemo T2 = new ThreadDemo( "Thread - 2 ", PD );

      T1.start();
      T2.start();

      // wait for threads to end
      try {
         T1.join();
         T2.join();
      }catch( Exception e) {
         System.out.println("Interrupted");
      }
   }
}
```
每次运行此程序时都会产生相同的结果 -

**Output**

    Starting Thread - 1
    Starting Thread - 2
    Counter   ---   5
    Counter   ---   4
    Counter   ---   3
    Counter   ---   2
    Counter   ---   1
    Thread Thread - 1  exiting.
    Counter   ---   5
    Counter   ---   4
    Counter   ---   3
    Counter   ---   2
    Counter   ---   1
    Thread Thread - 2  exiting.





