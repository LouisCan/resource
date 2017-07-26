---
title: Java 8 - Lambda表达式
date: 2017-07-18 20:52:40
tags: java8
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/java8Lambda%E8%A1%A8%E8%BE%BE%E5%BC%8F.png
keywords: java8, Lambda表达式,lambda
description: Java 8 - Lambda表达式,Lambda表达式在Java 8中被引入，被称为Java 8的最大特征.Lambda表达式有助于功能编程，并且简化了开发工作。lambda表达式的特征在于以下语法 -parameter -> expression body
---


## Java 8 - Lambda表达式

Lambda表达式在Java 8中被引入，被称为Java 8的最大特征.Lambda表达式有助于功能编程，并且简化了开发工作。

![](http://osewdhh4j.bkt.clouddn.com/java8Lambda%E8%A1%A8%E8%BE%BE%E5%BC%8F.png)

### 句法
lambda表达式的特征在于以下语法 -

    parameter -> expression body

以下是lambda表达式的重要特征 -

 - **可选类型声明** - 不需要声明参数的类型。编译器可以从参数的值推断相同的值。
 - **关于参数的可选括号** - 不需要在括号中声明单个参数。对于多个参数，需要括号。
 - **可选的花括号** - 如果正文包含单个语句，则无需在表达式正文中使用花括号。
 - **可选返回关键字** - 如果正文具有单个表达式以返回值，编译器将自动返回值。需要花括号表示表达式返回值。

### Lambda表达式示例
使用编辑器创建以下Java程序，并保存在某些文件夹中，如C：\> JAVA。

**Java8Tester.java**
```java
public class Java8Tester {
   public static void main(String args[]){
      Java8Tester tester = new Java8Tester();
		
      //with type declaration
      MathOperation addition = (int a, int b) -> a + b;
		
      //with out type declaration
      MathOperation subtraction = (a, b) -> a - b;
		
      //with return statement along with curly braces
      MathOperation multiplication = (int a, int b) -> { return a * b; };
		
      //without return statement and without curly braces
      MathOperation division = (int a, int b) -> a / b;
		
      System.out.println("10 + 5 = " + tester.operate(10, 5, addition));
      System.out.println("10 - 5 = " + tester.operate(10, 5, subtraction));
      System.out.println("10 x 5 = " + tester.operate(10, 5, multiplication));
      System.out.println("10 / 5 = " + tester.operate(10, 5, division));
		
      //without parenthesis
      GreetingService greetService1 = message ->
      System.out.println("Hello " + message);
		
      //with parenthesis
      GreetingService greetService2 = (message) ->
      System.out.println("Hello " + message);
		
      greetService1.sayMessage("Mahesh");
      greetService2.sayMessage("Suresh");
   }
	
   interface MathOperation {
      int operation(int a, int b);
   }
	
   interface GreetingService {
      void sayMessage(String message);
   }
	
   private int operate(int a, int b, MathOperation mathOperation){
      return mathOperation.operation(a, b);
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -

    10 + 5 = 15
    10 - 5 = 5
    10 x 5 = 50
    10 / 5 = 2
    Hello Mahesh
    Hello Suresh

以下是上述示例中要考虑的要点。

 - Lambda表达式主要用于定义功能接口的内联实现，即仅使用单一方法的接口。在上面的例子中，我们使用了各种类型的lambda表达式来定义`MathOperation接口`的操作方法。然后我们定义了GreetingService的sayMessage的实现。
 - Lambda表达式消除了匿名类的需要，并为Java提供了非常简单而强大的功能编程功能。

### 范围
使用lambda表达式，您可以参考最终变量或有效的最终变量（仅分配一次）。如果第二次给变量赋值，则Lambda表达式会引发编译错误。

**范围示例**
使用编辑器创建以下Java程序，并保存在某些文件夹中，如C：\> JAVA。

**Java8Tester.java**
```java
public class Java8Tester {

   final static String salutation = "Hello! ";
   
   public static void main(String args[]){
      GreetingService greetService1 = message -> 
      System.out.println(salutation + message);
      greetService1.sayMessage("Mahesh");
   }
	
   interface GreetingService {
      void sayMessage(String message);
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -

    Hello! Mahesh


