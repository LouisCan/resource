---
title: Java 8-概述
date: 2017-07-18 20:37:43
tags: java8
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/java8%E6%A6%82%E8%BF%B0.png
keywords: java8,Lambda表达式
description: JAVA 8（又名jdk 1.8）是JAVA编程语言开发的主要版本。其初始版本于2014年3月18日发布。随着Java 8版本的发布，Java为功能编程提供了支持，新的JavaScript引擎，用于日期时间操纵的新API，新的流API等。java 8中添加了几十项功能，其中最重要的功能如下-Lambda表达式,方法引用
---

## Java 8 - 概述

JAVA 8（又名jdk 1.8）是JAVA编程语言开发的主要版本。其初始版本于2014年3月18日发布。随着Java 8版本的发布，Java为功能编程提供了支持，新的JavaScript引擎，用于日期时间操纵的新API，新的流API等。

![](http://osewdhh4j.bkt.clouddn.com/java8%E6%A6%82%E8%BF%B0.png)

### 新功能
Java 8中添加了几十项功能，其中最重要的功能如下：

 - **Lambda表达式** - 向Java增加功能处理能力。
 - **方法引用** - 通过名称引用函数，而不是直接调用它们。使用函数作为参数。
 - **默认方法** - 具有默认方法实现的接口。
 - **新工具** - 添加新的编译器工具和实用程序，如“jdeps”来计算依赖关系。
 - **Stream API** - 新的流API，便于管道处理。
 - **日期时间API** - 改进的日期时间API。
 - **可选** - 强调正确处理空值的最佳做法。
 - **Nashorn，JavaScript Engine** - 一个基于Java的引擎来执行JavaScript代码。

除了这些新功能，在编译器和JVM级别都进行了许多功能增强。

### 编程风格
Java 8预计会改变程序员编写程序的方式。对于Java 7和Java 8的简单比较，我们来看一下使用Java 7和Java 8语法编写的排序程序。
```java
import java.util.Collections;
import java.util.List;
import java.util.ArrayList;
import java.util.Comparator;

public class Java8Tester {
   public static void main(String args[]){
   
      List<String> names1 = new ArrayList<String>();
      names1.add("Mahesh ");
      names1.add("Suresh ");
      names1.add("Ramesh ");
      names1.add("Naresh ");
      names1.add("Kalpesh ");
		
      List<String> names2 = new ArrayList<String>();
      names2.add("Mahesh ");
      names2.add("Suresh ");
      names2.add("Ramesh ");
      names2.add("Naresh ");
      names2.add("Kalpesh ");
		
      Java8Tester tester = new Java8Tester();
      System.out.println("Sort using Java 7 syntax: ");
		
      tester.sortUsingJava7(names1);
      System.out.println(names1);
      System.out.println("Sort using Java 8 syntax: ");
		
      tester.sortUsingJava8(names2);
      System.out.println(names2);
   }
   
   //sort using java 7
   private void sortUsingJava7(List<String> names){   
      Collections.sort(names, new Comparator<String>() {
         @Override
         public int compare(String s1, String s2) {
            return s1.compareTo(s2);
         }
      });
   }
   
   //sort using java 8
   private void sortUsingJava8(List<String> names){
      Collections.sort(names, (s1, s2) -> s1.compareTo(s2));
   }
}
```
该程序应该产生以下输出 -
```
Sort using Java 7 syntax:
[ Kalpesh Mahesh Naresh Ramesh Suresh ]
Sort using Java 8 syntax:
[ Kalpesh Mahesh Naresh Ramesh Suresh ]
```
这里**sortUsingJava8（）**方法使用具有lambda表达式作为参数的排序函数来获取排序标准。

这些所有功能都包含在Java 8中，我们不需要安装Java 8以外的任何东西。





