---
title: Java8-Functional Interfaces函数式接口
date: 2017-07-19 20:23:27
tags: java8
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/java8%E5%87%BD%E6%95%B0%E5%BC%8F%E6%8E%A5%E5%8F%A3.png
keywords: java,java8,lambda
description: Functional Interfaces具有单一的功能。例如，使用具有单个方法“compareTo”的Comparable接口进行比较。Java 8已经定义了很多功能接口，可以在lambda表达式中广泛使用。以下是java.util.Function包中定义的功能接口的列表。
---

## Java8-Functional Interfaces函数式接口

`Functional Interfaces`具有单一的功能。例如，使用具有单个方法“compareTo”的Comparable接口进行比较。Java 8已经定义了很多功能接口，可以在lambda表达式中广泛使用。以下是java.util.Function包中定义的功能接口的列表。

![](http://osewdhh4j.bkt.clouddn.com/java8%E5%87%BD%E6%95%B0%E5%BC%8F%E6%8E%A5%E5%8F%A3.png)

编号	| 接口和说明
----|------
1	|BiConsumer <T，U> 表示接受两个输入参数的操作，不返回结果。
2	|BiFunction<T,U,R> 表示接受两个参数并产生结果的函数。
3	|BinaryOperator <T> 表示对同一类型的两个操作数的操作，产生与操作数相同类型的结果。
4	|BiPredicate <T，U> 表示两个参数的谓词（布尔值函数）。
5	|BooleanSupplier 代表布尔值结果的供应商。
6	|Consumer<T> 表示接受单个输入参数并且不返回结果的操作。
7	|DoubleBinaryOperator 代表对两个双值操作数的操作，并产生双重值。
8	|DoubleConsumer 表示接受单个双值参数并且不返回结果的操作。
9	|DoubleFunction <R> 表示接受双值参数并产生结果的函数。
10	|DoublePredicate 表示一个双值参数的谓词（布尔值函数）。
11	|DoubleSupplier 代表双重业绩的供应商。
12	|DoubleToIntFunction 表示接受双值参数并产生int值结果的函数。
13	|DoubleToLongFunction 表示接受双值参数并产生长期值结果的函数。
14	|DoubleUnaryOperator 表示对单个双值操作数产生双重值结果的操作。
15	|Function<T,R> 表示接受一个参数并产生结果的函数。
16	|IntBinaryOperator 表示对两个int值操作数的操作，并产生一个int值的结果。
17	|IntConsumer 表示接受单个int值参数并且不返回任何结果的操作。
18	|IntFunction <R> 表示一个接受int值参数并产生结果的函数。
19	|IntPredicate 表示一个int值参数的谓词（布尔值函数）。
20	|IntSupplier 代表着一个有价值的结果供应商。
21	|IntToDoubleFunction 表示接受一个int值参数并产生一个双值结果的函数。
22	|IntToLongFunction 表示接受一个int值参数并产生一个长效结果的函数。
23	|IntUnaryOperator 表示对单个int值操作数产生一个int值的结果的操作。
24	|LongBinaryOperator 代表对两个长期操作数的操作，并产生长期的结果。
25	|LongConsumer 表示接受单个长值参数并且不返回结果的操作。
26	|LongFunction <R> 表示接受长期参数并产生结果的函数。
27	|LongPredicate 表示一个长值参数的谓词（布尔值函数）。
28	|LongSupplier 代表着长期业绩的供应商。
29	|LongToDoubleFunction 表示接受长期参数并产生双重值结果的函数。
30	|LongToIntFunction 表示接受长值参数并产生int值结果的函数。
31	|LongUnaryOperator 代表产生长期效果的单个长期操作数的操作。
32	|ObjDoubleConsumer <T> 表示接受对象值和双值参数的操作，不返回任何结果。
33	|ObjIntConsumer <T> 表示接受对象值和int值参数的操作，并且不返回任何结果。
34	|ObjLongConsumer <T> 表示接受对象值和长期参数的操作，不返回任何结果。
35	|Predicate<T> 表示一个参数的谓词（布尔值函数）。
36	|Supplier<T> 代表结果供应商。
37	|ToDoubleBiFunction <T，U> 表示接受两个参数并产生双值结果的函数。
38	|ToDoubleFunction <T> 表示产生双值结果的函数。
39	|ToIntBiFunction <T，U> 表示一个接受两个参数并产生一个int值结果的函数。
40	|ToIntFunction <T> 表示产生一个int值结果的函数。
41	|ToLongBiFunction <T，U> 表示接受两个参数并产生长效结果的函数。
42	|ToLongFunction <T> 表示产生长期效果的函数。
43	|UnaryOperator <T> 表示对单个操作数产生与其操作数相同类型的结果的操作。


### Functional Interfaces示例
Predicate <T>接口是一个Functional Interfaces，方法test（Object）返回一个布尔值。该接口表示对象被测试为true或false。

要更清楚地了解，请在代码编辑器中编写以下程序并验证结果。

**Java8Tester.java**
```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;

public class Java8Tester {
   public static void main(String args[]){
      List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
		
      // Predicate<Integer> predicate = n -> true
      // n is passed as parameter to test method of Predicate interface
      // test method will always return true no matter what value n has.
		
      System.out.println("Print all numbers:");
		
      //pass n as parameter
      eval(list, n->true);
		
      // Predicate<Integer> predicate1 = n -> n%2 == 0
      // n is passed as parameter to test method of Predicate interface
      // test method will return true if n%2 comes to be zero
		
      System.out.println("Print even numbers:");
      eval(list, n-> n%2 == 0 );
		
      // Predicate<Integer> predicate2 = n -> n > 3
      // n is passed as parameter to test method of Predicate interface
      // test method will return true if n is greater than 3.
		
      System.out.println("Print numbers greater than 3:");
      eval(list, n-> n > 3 );
   }
	
   public static void eval(List<Integer> list, Predicate<Integer> predicate) {
      for(Integer n: list) {
		
         if(predicate.test(n)) {
            System.out.println(n + " ");
         }
      }
   }
}
```
这里我们已经通过了Predicate接口，它接受一个输入并返回Boolean。

### 验证结果
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -
```
Print all numbers:
1
2
3
4
5
6
7
8
9
Print even numbers:
2
4
6
8
Print numbers greater than 3:
4
5
6
7
8
9
```




