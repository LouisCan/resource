---
title: Java - 线程死锁
date: 2017-07-16 18:37:49
tags: 多线程
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/Java-%E7%BA%BF%E7%A8%8B%E6%AD%BB%E9%94%81.png
keywords: java,Java - 线程死锁,多线程,死锁
description: 死锁描述了两个或多个线程被永久阻塞的情况，等待彼此。当多个线程需要相同的锁定但以不同的顺序获取时，会发生死锁。Java多线程程序可能会遇到死锁状况，因为synchronized关键字会导致执行线程在等待与指定对象相关联的锁定或监视时阻止。
---

## Java - 线程死锁

> 死锁描述了两个或多个线程被永久阻塞的情况，等待彼此。当多个线程需要相同的锁定但以不同的顺序获取时，会发生死锁。Java多线程程序可能会遇到死锁状况，因为synchronized关键字会导致执行线程在等待与指定对象相关联的锁定或监视时阻止。

![](http://osewdhh4j.bkt.clouddn.com/Java-%E7%BA%BF%E7%A8%8B%E6%AD%BB%E9%94%81.png)

这是一个例子。



**例**
```java
public class TestThread {
   public static Object Lock1 = new Object();
   public static Object Lock2 = new Object();
   
   public static void main(String args[]) {
      ThreadDemo1 T1 = new ThreadDemo1();
      ThreadDemo2 T2 = new ThreadDemo2();
      T1.start();
      T2.start();
   }
   
   private static class ThreadDemo1 extends Thread {
      public void run() {
         synchronized (Lock1) {
            System.out.println("Thread 1: Holding lock 1...");
            
            try { Thread.sleep(10); }
            catch (InterruptedException e) {}
            System.out.println("Thread 1: Waiting for lock 2...");
            
            synchronized (Lock2) {
               System.out.println("Thread 1: Holding lock 1 & 2...");
            }
         }
      }
   }
   private static class ThreadDemo2 extends Thread {
      public void run() {
         synchronized (Lock2) {
            System.out.println("Thread 2: Holding lock 2...");
            
            try { Thread.sleep(10); }
            catch (InterruptedException e) {}
            System.out.println("Thread 2: Waiting for lock 1...");
            
            synchronized (Lock1) {
               System.out.println("Thread 2: Holding lock 1 & 2...");
            }
         }
      }
   } 
}
```
当您编译并执行上述程序时，您会发现死锁情况，以下是程序生成的输出 -

**Output**

    Thread 1: Holding lock 1...
    Thread 2: Holding lock 2...
    Thread 1: Waiting for lock 2...
    Thread 2: Waiting for lock 1...

上述程序将永久挂起，因为两个线程都不能继续进行，等待彼此释放锁定，所以您可以按CTRL + C退出程序。

**死锁解决方案示例**
让我们改变锁定的顺序并运行相同的程序，看看这两个线程是否仍然相互等待 -

**例**
```java
public class TestThread {
   public static Object Lock1 = new Object();
   public static Object Lock2 = new Object();
   
   public static void main(String args[]) {
      ThreadDemo1 T1 = new ThreadDemo1();
      ThreadDemo2 T2 = new ThreadDemo2();
      T1.start();
      T2.start();
   }
   
   private static class ThreadDemo1 extends Thread {
      public void run() {
         synchronized (Lock1) {
            System.out.println("Thread 1: Holding lock 1...");
            
            try {
               Thread.sleep(10);
            }catch (InterruptedException e) {}
            System.out.println("Thread 1: Waiting for lock 2...");
            
            synchronized (Lock2) {
               System.out.println("Thread 1: Holding lock 1 & 2...");
            }
         }
      }
   }
   private static class ThreadDemo2 extends Thread {
      public void run() {
         synchronized (Lock1) {
            System.out.println("Thread 2: Holding lock 1...");
           
            try {
               Thread.sleep(10);
            }catch (InterruptedException e) {}
            System.out.println("Thread 2: Waiting for lock 2...");
            
            synchronized (Lock2) {
               System.out.println("Thread 2: Holding lock 1 & 2...");
            }
         }
      }
   } 
}
```
所以只是改变锁的顺序防止程序进入死锁情况并完成以下结果 -

**Output**

    Thread 1: Holding lock 1...
    Thread 1: Waiting for lock 2...
    Thread 1: Holding lock 1 & 2...
    Thread 2: Holding lock 1...
    Thread 2: Waiting for lock 2...
    Thread 2: Holding lock 1 & 2...

上面的例子只是让这个概念清楚，然而，这是一个复杂的概念，你应该深入了解它，然后再开发应用程序来处理死锁情况。



