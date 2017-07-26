---
title: Java 8 - 方法引用
date: 2017-07-18 21:00:17
tags: java8
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/java8%E6%96%B9%E6%B3%95%E5%BC%95%E7%94%A8.png
keywords: java8,方法引用,Java 8 - 方法引用
description: 方法引用有助于通过名称来指向方法。使用::(双冒号）符号描述方法引用。方法参考可以用于指出以下类型的方法 -  - **静态方法**
 - **实例方法**
 - **使用新的运算符的构造函数（TreeSet::new）**
---

## Java 8 - 方法引用

方法引用有助于通过名称来指向方法。使用::（双冒号）符号描述方法引用。方法参考可以用于指出以下类型的方法 -

![](http://osewdhh4j.bkt.clouddn.com/java8%E6%96%B9%E6%B3%95%E5%BC%95%E7%94%A8.png)

 - **静态方法**
 - **实例方法**
 - **使用新的运算符的构造函数（TreeSet :: new）**

### 方法参考实例
我们来看一下方法引用的例子，以获得更清晰的图像。在代码编辑器中编写以下程序，并与结果进行匹配。

**Java8Tester.java**
```java
import java.util.List;
import java.util.ArrayList;

public class Java8Tester {
   public static void main(String args[]){
      List names = new ArrayList();
		
      names.add("Mahesh");
      names.add("Suresh");
      names.add("Ramesh");
      names.add("Naresh");
      names.add("Kalpesh");
		
      names.forEach(System.out::println);
   }
}
```
这里我们已经通过`System.out :: println`方法作为静态方法引用。

**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -

    Mahesh
    Suresh
    Ramesh
    Naresh
    Kalpesh


