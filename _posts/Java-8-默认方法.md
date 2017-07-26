---
title: Java 8 - 默认方法
date: 2017-07-19 20:32:01
tags: java8
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/Java8-%E9%BB%98%E8%AE%A4%E6%96%B9%E6%B3%95.png
keywords: java,java8,lambda
description: Java 8在接口中引入了默认方法实现的新概念。添加此功能以实现向后兼容，从而可以使用旧接口来利用Java 8的lambda表达能力。例如，“List”或“Collection”接口没有“forEach”方法声明。因此，添加这种方法将简单地打破收集框架的实现。Java 8引入了默认方法，使List / Collection接口可以具有forEach方法的默认实现，实现这些接口的类不需要实现。
---

## Java 8 - 默认方法

Java 8在接口中引入了默认方法实现的新概念。添加此功能以实现向后兼容，从而可以使用旧接口来利用Java 8的lambda表达能力。例如，“List”或“Collection”接口没有“forEach”方法声明。因此，添加这种方法将简单地打破收集框架的实现。Java 8引入了默认方法，使List / Collection接口可以具有forEach方法的默认实现，实现这些接口的类不需要实现。

![](http://osewdhh4j.bkt.clouddn.com/Java8-%E9%BB%98%E8%AE%A4%E6%96%B9%E6%B3%95.png)

### 句法
```
public interface vehicle {
   default void print(){
      System.out.println("I am a vehicle!");
   }
}
```
### 多个默认值
使用接口中的默认功能，类可能使用相同的默认方法实现两个接口。以下代码解释了如何解决这种歧义。
```
public interface vehicle {
   default void print(){
      System.out.println("I am a vehicle!");
   }
}

public interface fourWheeler {
   default void print(){
      System.out.println("I am a four wheeler!");
   }
}
```
第一个解决方案是创建一个覆盖默认实现的自己的方法。
```
public class car implements vehicle, fourWheeler {
   default void print(){
      System.out.println("I am a four wheeler car vehicle!");
   }
}
```
第二个解决方案是使用super调用指定接口的默认方法。
```
public class car implements vehicle, fourWheeler {
   default void print(){
      vehicle.super.print();
   }
}
```
### 静态默认方法
接口也可以具有来自Java 8的静态帮助程序。
```java
public interface vehicle {
   default void print(){
      System.out.println("I am a vehicle!");
   }
	
   static void blowHorn(){
      System.out.println("Blowing horn!!!");
   }
}
```
### 默认方法示例
让我们看一个例子来更清楚地说明默认方法。请在代码编辑器中编写以下程序，了解并验证结果。

**Java8Tester.java**
```java
public class Java8Tester {
   public static void main(String args[]){
      Vehicle vehicle = new Car();
      vehicle.print();
   }
}

interface Vehicle {
   default void print(){
      System.out.println("I am a vehicle!");
   }
	
   static void blowHorn(){
      System.out.println("Blowing horn!!!");
   }
}

interface FourWheeler {
   default void print(){
      System.out.println("I am a four wheeler!");
   }
}

class Car implements Vehicle, FourWheeler {
   public void print(){
      Vehicle.super.print();
      FourWheeler.super.print();
      Vehicle.blowHorn();
      System.out.println("I am a car!");
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -

    I am a vehicle!
    I am a four wheeler!
    Blowing horn!!!
    I am a car!




