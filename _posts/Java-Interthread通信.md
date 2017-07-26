---
title: Java - Interthread通信
date: 2017-07-16 18:28:40
tags: 多线程
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/java-%20Interthread%E9%80%9A%E4%BF%A1.png
keywords: 多线程,java,Interthread通信
description: 如果你知道进程间通信，那么你很容易理解interthread通信。当您开发两个或多个线程交换一些信息的应用程序时，Interthread通信很重要。
---

## Java - Interthread通信

**如果你知道进程间通信，那么你很容易理解interthread通信。当您开发两个或多个线程交换一些信息的应用程序时，Interthread通信很重要。**

![](http://osewdhh4j.bkt.clouddn.com/java-%20Interthread%E9%80%9A%E4%BF%A1.png)

有三个简单的方法和一个小技巧，使线程通信成为可能。所有三种方法都列在下面 -

Sr.No. | 	方法和说明
----|----
1	 |**public void wait（）** 导致当前线程等到另一个线程调用`notify（）`。
2	 |**public void notify（）** 唤醒正在等待对象监视器的单个线程。
3	 |**public void notifyAll（）** 唤醒所有在同一个对象上调用`wait（）`的线程。

这些方法已被实现为Object中的最终方法，因此它们在所有类中都可用。所有这三种方法只能从**同步**上下文中调用。

例
这个例子显示了两个线程如何使用**wait（）**和**notify（）**方法进行通信。您可以使用相同的概念创建一个复杂的系统。
```java
class Chat {
   boolean flag = false;

   public synchronized void Question(String msg) {
      if (flag) {
         try {
            wait();
         }catch (InterruptedException e) {
            e.printStackTrace();
         }
      }
      System.out.println(msg);
      flag = true;
      notify();
   }

   public synchronized void Answer(String msg) {
      if (!flag) {
         try {
            wait();
         }catch (InterruptedException e) {
            e.printStackTrace();
         }
      }

      System.out.println(msg);
      flag = false;
      notify();
   }
}
```
```java
class T1 implements Runnable {
   Chat m;
   String[] s1 = { "Hi", "How are you ?", "I am also doing fine!" };

   public T1(Chat m1) {
      this.m = m1;
      new Thread(this, "Question").start();
   }

   public void run() {
      for (int i = 0; i < s1.length; i++) {
         m.Question(s1[i]);
      }
   }
}

class T2 implements Runnable {
   Chat m;
   String[] s2 = { "Hi", "I am good, what about you?", "Great!" };

   public T2(Chat m2) {
      this.m = m2;
      new Thread(this, "Answer").start();
   }

   public void run() {
      for (int i = 0; i < s2.length; i++) {
         m.Answer(s2[i]);
      }
   }
}
public class TestThread {
   public static void main(String[] args) {
      Chat m = new Chat();
      new T1(m);
      new T2(m);
   }
}
```
当上述程序被执行并执行时，会产生以下结果 -

    Hi
    Hi
    How are you ?
    I am good, what about you?
    I am also doing fine!
    Great!




