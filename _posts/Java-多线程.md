---
title: Java - 多线程
date: 2017-07-16 17:35:48
tags: 多线程
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/java%E5%A4%9A%E7%BA%BF%E7%A8%8Btitle.png
keywords: java多线程,多线程,java,Runnable,Thread
description: Java是一种多线程编程语言，这意味着我们可以使用Java开发多线程程序。多线程程序包含两个或多个可同时运行的部件，每个部件可以同时处理不同的任务，从而最佳地利用可用资源，特别是当您的计算机有多个CPU时。
---

## Java - 多线程

> Java是一种多线程编程语言，这意味着我们可以使用Java开发多线程程序。多线程程序包含两个或多个可同时运行的部件，每个部件可以同时处理不同的任务，从而最佳地利用可用资源，特别是当您的计算机有多个CPU时。

![](http://osewdhh4j.bkt.clouddn.com/java%E5%A4%9A%E7%BA%BF%E7%A8%8Btitle.png)

根据定义，多任务是当多个进程共享诸如CPU的公共处理资源时。多线程将多任务的概念扩展到可以将单个应用程序中的特定操作细分为单个线程的应用程序。每个线程可以并行运行。OS不仅在不同的应用程序之间划分处理时间，而且在应用程序中的每个线程之间划分处理时间。

**多线程使您能够以同一程序同时进行多个活动的方式进行写入。**

### 线程的生命周期
线程在其生命周期中经历了各个阶段。例如，线程诞生，启动，运行，然后死亡。下图显示了线程的完整生命周期。

![](http://osewdhh4j.bkt.clouddn.com/Thread_Life_Cycle.jpg)

以下是生命周期的阶段 -

 - **New**  - 新线程在新的状态下开始其生命周期。直到程序启动线程为止，它保持在这种状态。它也被称为`a born thread`。
 - **Runnable** - 新诞生的线程启动后，该线程可以运行。该状态的线程被认为正在执行其任务。
 - **Waiting** - 有时线程会转换到等待状态，而线程等待另一个线程执行任务。只有当另一个线程发信号通知等待线程才能继续执行时，线程才转回到可运行状态。
 - **Timed Waiting**- 可运行的线程可以在指定的时间间隔内进入定时等待状态。当该时间间隔到期或发生等待的事件时，此状态的线程将转换回可运行状态。
 - **Terminated (Dead)** - 可执行线程在完成任务或以其他方式终止时进入终止状态。

### 线程优先级
每个Java线程都有一个优先级，可以帮助操作系统确定安排线程的顺序。

Java线程优先级在`MIN_PRIORITY`（常数为1）和`MAX_PRIORITY`（常数为10）之间的范围内。默认情况下，每个线程都被赋予优先级`NORM_PRIORITY`（常数为5）。

具有较高优先级的线程对于一个程序来说更重要，应该在低优先级线程之前分配处理器时间。然而，线程优先级不能保证线程执行的顺序，并且依赖于平台。

### 通过实现Runnable接口创建一个线程
如果您的类旨在作为线程执行，那么您可以通过**实现Runnable接口**来实现此目的。您将需要遵循三个基本步骤 -

**步骤1**
作为第一步，您需要实现由Runnable接口提供的`run（）`方法。该方法为线程提供了一个入口点，您将把完整的业务逻辑放在此方法中。以下是run（）方法的简单语法 -

    public void run( )

**步骤2**
作为第二步，您将使用以下构造函数实例化一个Thread对象 -

    Thread(Runnable threadObj, String threadName);

其中，`threadObj`是实现`Runnable接口`的类的实例，`threadName`是给予新线程的名称。

**步骤3**
一旦创建了一个线程对象，您可以通过调用`start（）`方法启动它，该方法执行对`run（）`方法的调用。以下是一个简单的语法`start（）`方法 -

    void start();

例
这是一个创建一个新线程并开始运行的示例 -
```java
class RunnableDemo implements Runnable {
   private Thread t;
   private String threadName;
   
   RunnableDemo( String name) {
      threadName = name;
      System.out.println("Creating " +  threadName );
   }
   
   public void run() {
      System.out.println("Running " +  threadName );
      try {
         for(int i = 4; i > 0; i--) {
            System.out.println("Thread: " + threadName + ", " + i);
            // Let the thread sleep for a while.
            Thread.sleep(50);
         }
      }catch (InterruptedException e) {
         System.out.println("Thread " +  threadName + " interrupted.");
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
```
```java
public class TestThread {

   public static void main(String args[]) {
      RunnableDemo R1 = new RunnableDemo( "Thread-1");
      R1.start();
      
      RunnableDemo R2 = new RunnableDemo( "Thread-2");
      R2.start();
   }   
}
```
这将产生以下结果 -

    Output
    Creating Thread-1
    Starting Thread-1
    Creating Thread-2
    Starting Thread-2
    Running Thread-1
    Thread: Thread-1, 4
    Running Thread-2
    Thread: Thread-2, 4
    Thread: Thread-1, 3
    Thread: Thread-2, 3
    Thread: Thread-1, 2
    Thread: Thread-2, 2
    Thread: Thread-1, 1
    Thread: Thread-2, 1
    Thread Thread-1 exiting.
    Thread Thread-2 exiting.

### 通过扩展线程类创建一个线程
创建线程的第二种方法是创建一个新类，使用以下两个简单的步骤来扩展Thread类。这种方法在处理使用Thread类中可用的方法创建的多个线程时提供了更多的灵活性。

**步骤1**
您将需要覆盖Thread类中可用的run（）方法。该方法为线程提供了一个入口点，您将把完整的业务逻辑放在此方法中。以下是run（）方法的简单语法 -

    public void run( )

**第2步**
一旦创建了Thread对象，您可以通过调用start（）方法启动它，该方法执行对run（）方法的调用。以下是一个简单的语法start（）方法 -

    void start( );

例
这是以前的程序重写来扩展Thread -
```java
class ThreadDemo extends Thread {
   private Thread t;
   private String threadName;
   
   ThreadDemo( String name) {
      threadName = name;
      System.out.println("Creating " +  threadName );
   }
   
   public void run() {
      System.out.println("Running " +  threadName );
      try {
         for(int i = 4; i > 0; i--) {
            System.out.println("Thread: " + threadName + ", " + i);
            // Let the thread sleep for a while.
            Thread.sleep(50);
         }
      }catch (InterruptedException e) {
         System.out.println("Thread " +  threadName + " interrupted.");
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
```
```java
public class TestThread {

   public static void main(String args[]) {
      ThreadDemo T1 = new ThreadDemo( "Thread-1");
      T1.start();
      
      ThreadDemo T2 = new ThreadDemo( "Thread-2");
      T2.start();
   }   
}
```
这将产生以下结果 -

    Output
    Creating Thread-1
    Starting Thread-1
    Creating Thread-2
    Starting Thread-2
    Running Thread-1
    Thread: Thread-1, 4
    Running Thread-2
    Thread: Thread-2, 4
    Thread: Thread-1, 3
    Thread: Thread-2, 3
    Thread: Thread-1, 2
    Thread: Thread-2, 2
    Thread: Thread-1, 1
    Thread: Thread-2, 1
    Thread Thread-1 exiting.
    Thread Thread-2 exiting.

### 线程方法
以下是Thread类中可用的重要方法的列表。

Sr.No.	| 方法和说明
----|------
1	| **public void start（）** 在单独的执行路径中启动线程，然后调用此Thread对象上的`run（）`方法。
2	| **public void run（）** 如果使用单独的Runnable目标实例化了此Thread对象，则在该Runnable对象上调用run（）方法。
3	| **public final void setName（String name）** 更改Thread对象的名称。还有一个用于检索名称的getName（）方法。
4	| **public final void setPriority（int priority）** 设置此Thread对象的优先级。可能的值在1到10之间。
5	| **public final void setDaemon（boolean on）** `true`的参数表示此线程作为`守护线程`。
6	| **public final void join（long millisec）** 当前线程在第二个线程上调用此方法，导致当前线程阻塞，直到第二个线程终止或指定的毫秒数通过。
7	| **public void interrupt（）** 中断这个线程，导致它由于任何原因被阻止而继续执行。
8	| **public final boolean isAlive（）** 如果线程是活着的，那么线程启动之后，运行到完成之前的任何时间都将返回true。

以前的方法在一个特定的Thread对象上被调用。Thread类中的以下方法是静态的。调用其中一个静态方法对当前运行的线程执行操作。

Sr.No.	| 方法和说明
----|-----
1	 |**public static void yield（）** 导致当前运行的线程屈服于任何其他正在等待调度的优先级的其他线程。
2	 |**public static void sleep（long millisec）** 使当前正在运行的线程至少阻塞指定的毫秒数。
3	 |**public static boolean holdingLock（Object x）** 如果当前线程持有给定对象的锁，则返回true。
4	 |**public static Thread currentThread（）** 返回对当前正在运行的线程的引用，该线程是调用此方法的线程。
5	| **public static void dumpStack（）** 打印当前正在运行的线程的堆栈跟踪，这在调试多线程应用程序时非常有用。

**例**
以下`ThreadClassDemo`程序演示了Thread类的一些这些方法。考虑一个类**DisplayMessage**实现**Runnable** -

```java
// File Name : DisplayMessage.java
// Create a thread to implement Runnable

public class DisplayMessage implements Runnable {
   private String message;
   
   public DisplayMessage(String message) {
      this.message = message;
   }
   
   public void run() {
      while(true) {
         System.out.println(message);
      }
   }
}
```
以下是扩展Thread类的另一个类 -
```java
// File Name : GuessANumber.java
// Create a thread to extentd Thread

public class GuessANumber extends Thread {
   private int number;
   public GuessANumber(int number) {
      this.number = number;
   }
   
   public void run() {
      int counter = 0;
      int guess = 0;
      do {
         guess = (int) (Math.random() * 100 + 1);
         System.out.println(this.getName() + " guesses " + guess);
         counter++;
      } while(guess != number);
      System.out.println("** Correct!" + this.getName() + "in" + counter + "guesses.**");
   }
}
```
以下是主程序，它利用上面定义的类 -
```java
// File Name : ThreadClassDemo.java
public class ThreadClassDemo {

   public static void main(String [] args) {
      Runnable hello = new DisplayMessage("Hello");
      Thread thread1 = new Thread(hello);
      thread1.setDaemon(true);
      thread1.setName("hello");
      System.out.println("Starting hello thread...");
      thread1.start();
      
      Runnable bye = new DisplayMessage("Goodbye");
      Thread thread2 = new Thread(bye);
      thread2.setPriority(Thread.MIN_PRIORITY);
      thread2.setDaemon(true);
      System.out.println("Starting goodbye thread...");
      thread2.start();

      System.out.println("Starting thread3...");
      Thread thread3 = new GuessANumber(27);
      thread3.start();
      try {
         thread3.join();
      }catch(InterruptedException e) {
         System.out.println("Thread interrupted.");
      }
      System.out.println("Starting thread4...");
      Thread thread4 = new GuessANumber(75);
      
      thread4.start();
      System.out.println("main() is ending...");
   }
}
```
这将产生以下结果。您可以一次又一次地尝试这个例子，每次都会得到不同的结果。

    Output
    Starting hello thread...
    Starting goodbye thread...
    Hello
    Hello
    Hello
    Hello
    Hello
    Hello
    Goodbye
    Goodbye
    Goodbye
    Goodbye
    Goodbye




