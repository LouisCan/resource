---
title: Java 8 - Optional Class可选类
date: 2017-07-19 20:53:37
tags: java8
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/Java8-%E5%8F%AF%E9%80%89%E7%B1%BB.png
keywords: java,java8,lambda
description: 可选是一个容器对象，用于包含非空对象。可选对象用于表示null，缺少值。这个类有各种各样的实用方法，以方便代码处理值为'可用'或'不可用'，而不是检查空值。它在Java 8中引入，与Guava中的可选项相似。
---


## Java 8 - Optional Class可选类

可选是一个容器对象，用于包含非空对象。可选对象用于表示null，缺少值。这个类有各种各样的实用方法，以方便代码处理值为'可用'或'不可用'，而不是检查空值。它在Java 8中引入，与Guava中的可选项相似。

![](http://osewdhh4j.bkt.clouddn.com/Java8-%E5%8F%AF%E9%80%89%E7%B1%BB.png)

### Class Declaration
以下是java.util.Optional <T>类的声明-

    public final class Optional<T>
    extends Object

### 类方法
编号|	方法和说明
------|-----
1	|static <T> Optional<T> empty() 返回一个空的可选实例。
2	|boolean equals（Object obj） 指示某个其他对象是否等于此可选项。
3	|Optional<T> filter(Predicate<? super <T> predicate) 如果存在值并且该值与给定谓词匹配，则返回一个可选描述该值，否则返回一个空可选。
4 |	<U> Optional<U> flatMap(Function<? super T,Optional<U>> mapper) 如果一个值存在，它会将提供的可选轴承映射函数应用于该值，返回该结果，否则返回一个空可选。
5	|T get（） 如果此可选中存在值，则返回该值，否则将抛出NoSuchElementException。
6	|int hashCode（） 返回当前值的哈希码值（如果有的话），如果没有值，则返回0（零）。
7	|void ifPresent（Consumer <？super T> consumer） 如果存在值，它将使用该值调用指定的消费者，否则不执行任何操作。
8	|boolean isPresent（） 如果存在值，则返回true，否则返回false。
9|	<U>Optional<U> map(Function<? super T,? extends U> mapper) 如果存在值，则将提供的映射函数应用于其中，如果结果为非空，则返回一个可选描述结果。
10|	static <T> Optional<T> of(T value) 返回具有指定的当前非空值的可选。
11|	static <T> Optional<T> ofNullable(T value) 返回一个可选描述指定的值，如果非空，否则返回一个空可选。
12|	T orElse(T other) 返回值（如果存在），否则返回其他值。
13|	T orElseGet(Supplier<? extends T> other) 返回值（如果存在），否则调用其他值并返回该调用的结果。
14|	<X extends Throwable> T orElseThrow（Supplier <？extends X> exceptionSupplier） 返回包含的值（如果存在），否则将抛出由提供的供应商创建的异常。
15|	String toString（） 返回此可选的非空字符串表示，适用于调试。

**注 - 此类继承java.lang.Object类中的方法。**

### 可选实例
要了解在实践中如何使用Optional，让我们看看下面的例子。编写以下程序，执行和验证结果，以获得更多的洞察力。

**Java8Tester.java**
```java
import java.util.Optional;

public class Java8Tester {
   public static void main(String args[]){
   
      Java8Tester java8Tester = new Java8Tester();
      Integer value1 = null;
      Integer value2 = new Integer(10);
		
      //Optional.ofNullable - allows passed parameter to be null.
      Optional<Integer> a = Optional.ofNullable(value1);
		
      //Optional.of - throws NullPointerException if passed parameter is null
      Optional<Integer> b = Optional.of(value2);
      System.out.println(java8Tester.sum(a,b));
   }
	
   public Integer sum(Optional<Integer> a, Optional<Integer> b){
	
      //Optional.isPresent - checks the value is present or not
		
      System.out.println("First parameter is present: " + a.isPresent());
      System.out.println("Second parameter is present: " + b.isPresent());
		
      //Optional.orElse - returns the value if present otherwise returns
      //the default value passed.
      Integer value1 = a.orElse(new Integer(0));
		
      //Optional.get - gets the value, value should be present
      Integer value2 = b.get();
      return value1 + value2;
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -

    First parameter is present: false
    Second parameter is present: true
    10


