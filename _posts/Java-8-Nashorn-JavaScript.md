---
title: Java 8 - Nashorn JavaScript
date: 2017-07-19 21:00:16
tags: java8
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/Java8Nashorn%20JavaScript.png
keywords: java,java8,lambda
description: 使用Java 8，Nashorn，引入了一个大大改进的JavaScript引擎来替代现有的Rhino。Nashorn提供2到10倍的性能，因为它直接编译内存中的代码，并将字节码传递给JVM。Nashorn使用在Java 7中引入的invokedynamics功能来提高性能。
---

## Java 8 - Nashorn JavaScript

使用Java 8，Nashorn，引入了一个大大改进的JavaScript引擎来替代现有的Rhino。Nashorn提供2到10倍的性能，因为它直接编译内存中的代码，并将字节码传递给JVM。Nashorn使用在Java 7中引入的invokedynamics功能来提高性能。

![](http://osewdhh4j.bkt.clouddn.com/Java8Nashorn%20JavaScript.png)

### JJS
对于Nashorn引擎，JAVA 8引入了一个新的命令行工具jjs来在控制台执行JavaScript代码。

### 解释js文件
在c：\> JAVA文件夹中创建并保存文件sample.js。

    sample.js
    print('Hello World!');

打开控制台并使用以下命令。

    $jjs sample.js

它将产生以下输出：

    Hello World!

jjs在交互模式
打开控制台并使用以下命令。

    $jjs
    jjs> print("Hello, World!")
    Hello, World!
    jjs> quit()
    >>

**通过参数**
打开控制台并使用以下命令。

    $jjs -- a b c
    jjs> print('letters: ' +arguments.join(", "))
    letters: a, b, c
    jjs>

**从Java调用JavaScript**
使用ScriptEngineManager，可以在Java中调用和解释JavaScript代码。

**例**
使用您所选择的任何编辑器（例如C：\> JAVA）创建以下Java程序。

**Java8Tester.java**
```java
import javax.script.ScriptEngineManager;
import javax.script.ScriptEngine;
import javax.script.ScriptException;

public class Java8Tester {
   public static void main(String args[]){
   
      ScriptEngineManager scriptEngineManager = new ScriptEngineManager();
      ScriptEngine nashorn = scriptEngineManager.getEngineByName("nashorn");
		
      String name = "Mahesh";
      Integer result = null;
      
      try {
         nashorn.eval("print('" + name + "')");
         result = (Integer) nashorn.eval("10 + 2");
         
      }catch(ScriptException e){
         System.out.println("Error executing script: "+ e.getMessage());
      }
      
      System.out.println(result.toString());
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

应该产生以下结果 -

    Mahesh
    12

### 从JavaScript调用Java
以下示例说明如何在java脚本中导入和使用Java类 -

**sample.js**
```
var BigDecimal = Java.type('java.math.BigDecimal');

function calculate(amount, percentage) {

   var result = new BigDecimal(amount).multiply(
   new BigDecimal(percentage)).divide(new BigDecimal("100"), 2, BigDecimal.ROUND_HALF_EVEN);
   
   return result.toPlainString();
}

var result = calculate(568000000000000000023,13.9);
print(result);
```
打开控制台并使用以下命令。

    $jjs sample.js

它应该产生以下输出 -

    78952000000000000003.20



