---
title: Java 8 - Streams
date: 2017-07-19 20:42:30
tags: java8
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/Java8Streams.png
keywords: java,java8,lambda
description: Stream是Java 8中引入的一个新的抽象层。使用流，您可以以类似于SQL语句的声明方式来处理数据。例如，考虑以下SQL语句 -SELECT max(salary), employee_id, employee_name FROM Employee上述SQL表达式自动返回最高受薪雇员的详细信息，而不对开发人员的结尾进行任何计算。在Java中使用集合框架，开发人员必须使用循环并进行重复检查。另一个问题是效率; 由于多核处理器可以放心使用，因此Java开发人员必须编写并行代码处理，这可能非常容易出错。为了解决这些问题，Java 8引入了流的概念，让开发人员以声明方式处理数据，并利用多核架构，而无需为其编写任何特定的代码。
---

## Java 8 - Streams

Stream是Java 8中引入的一个新的抽象层。使用流，您可以以类似于SQL语句的声明方式来处理数据。例如，考虑以下SQL语句 -

    SELECT max(salary), employee_id, employee_name FROM Employee

上述SQL表达式自动返回最高受薪雇员的详细信息，而不对开发人员的结尾进行任何计算。在Java中使用集合框架，开发人员必须使用循环并进行重复检查。另一个问题是效率; 由于多核处理器可以放心使用，因此Java开发人员必须编写并行代码处理，这可能非常容易出错。

为了解决这些问题，Java 8引入了流的概念，让开发人员以声明方式处理数据，并利用多核架构，而无需为其编写任何特定的代码。

![](http://osewdhh4j.bkt.clouddn.com/Java8Streams.png)

### 什么是流？
**Stream**表示来自源的对象序列，它支持聚合操作。以下是一个流的特点 -

 - **元素序列** - 流以顺序方式提供特定类型的元素集合。流根据需要获取/计算元素。它从不存储元素。
 - **Source** - Stream将收集，数组或I / O资源作为输入源。
 - **集合操作** - 流支持聚合操作，如过滤器，映射，限制，减少，查找，匹配等。
 - **流水线** - 大多数流操作返回流本身，以便其结果可以流水线化。这些操作称为中间操作，它们的功能是输入，处理它们并将输出返回到目标。collect（）方法是终端操作，通常在流水线操作结束时存在，以标记流的结尾。
 - **自动迭代** - 流操作在提供的源元素内部进行迭代，与需要显式迭代的集合相反。

### 生成流
使用Java 8，Collection接口有两种方法来生成一个Stream -

 - **stream（）** - 返回以集合为源的顺序流。
 - **parallelStream（）** - 返回一个并行流考虑集合作为其源。

```
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
```
### forEach
Stream提供了一种新的方法“forEach”来迭代流的每个元素。以下代码段显示如何使用forEach打印10个随机数。
```
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```
### map
“map”方法用于将每个元素映射到其对应的结果。以下代码片段使用地图打印数字的独特方块。
```
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
//get list of unique squares
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
```
### filter
“过滤器”方法用于根据标准消除元素。以下代码段使用过滤器打印空字符串的计数。
```
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
//get count of empty string
int count = strings.stream().filter(string -> string.isEmpty()).count();
```
### limit
'limit'方法用于减小流的大小。以下代码段显示如何使用limit打印10个随机数。
```
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```
### sorted
'sorted'方法用于对流进行排序。以下代码段显示如何以排序顺序打印10个随机数。
```
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
```
### Parallel Processing
parallelStream是用于并行处理的流的替代。看看下面的代码段，使用parallelStream打印一个空字符串的数量。
```
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
//get count of empty string
int count = strings.parallelStream().filter(string -> string.isEmpty()).count();
```
在顺序和并行流之间切换是非常容易的。

### Collectors
Collectors用于将处理结果与流的元素相结合。收集器可用于返回列表或字符串。
```
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());

System.out.println("Filtered List: " + filtered);
String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(", "));
System.out.println("Merged String: " + mergedString);
```
### Statistics
使用Java 8，引入统计收集器以在流处理完成时计算所有统计信息。
```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);

IntSummaryStatistics stats = integers.stream().mapToInt((x) -> x).summaryStatistics();

System.out.println("Highest number in List : " + stats.getMax());
System.out.println("Lowest number in List : " + stats.getMin());
System.out.println("Sum of all numbers : " + stats.getSum());
System.out.println("Average of all numbers : " + stats.getAverage());
```
### Stream Example
使用您所选择的任何编辑器（例如C：\> JAVA）创建以下Java程序。

**Java8Tester.java**
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.IntSummaryStatistics;
import java.util.List;
import java.util.Random;
import java.util.stream.Collectors;
import java.util.Map;

public class Java8Tester {
   public static void main(String args[]){
      System.out.println("Using Java 7: ");
		
      // Count empty strings
      List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
      System.out.println("List: " +strings);
      long count = getCountEmptyStringUsingJava7(strings);
		
      System.out.println("Empty Strings: " + count);
      count = getCountLength3UsingJava7(strings);
		
      System.out.println("Strings of length 3: " + count);
		
      //Eliminate empty string
      List<String> filtered = deleteEmptyStringsUsingJava7(strings);
      System.out.println("Filtered List: " + filtered);
		
      //Eliminate empty string and join using comma.
      String mergedString = getMergedStringUsingJava7(strings,", ");
      System.out.println("Merged String: " + mergedString);
      List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
		
      //get list of square of distinct numbers
      List<Integer> squaresList = getSquares(numbers);
      System.out.println("Squares List: " + squaresList);
      List<Integer> integers = Arrays.asList(1,2,13,4,15,6,17,8,19);
		
      System.out.println("List: " +integers);
      System.out.println("Highest number in List : " + getMax(integers));
      System.out.println("Lowest number in List : " + getMin(integers));
      System.out.println("Sum of all numbers : " + getSum(integers));
      System.out.println("Average of all numbers : " + getAverage(integers));
      System.out.println("Random Numbers: ");
		
      //print ten random numbers
      Random random = new Random();
		
      for(int i=0; i < 10; i++){
         System.out.println(random.nextInt());
      }
		
      System.out.println("Using Java 8: ");
      System.out.println("List: " +strings);
		
      count = strings.stream().filter(string->string.isEmpty()).count();
      System.out.println("Empty Strings: " + count);
		
      count = strings.stream().filter(string -> string.length() == 3).count();
      System.out.println("Strings of length 3: " + count);
		
      filtered = strings.stream().filter(string ->!string.isEmpty()).collect(Collectors.toList());
      System.out.println("Filtered List: " + filtered);
		
      mergedString = strings.stream().filter(string ->!string.isEmpty()).collect(Collectors.joining(", "));
      System.out.println("Merged String: " + mergedString);
		
      squaresList = numbers.stream().map( i ->i*i).distinct().collect(Collectors.toList());
      System.out.println("Squares List: " + squaresList);
      System.out.println("List: " +integers);
		
      IntSummaryStatistics stats = integers.stream().mapToInt((x) ->x).summaryStatistics();
		
      System.out.println("Highest number in List : " + stats.getMax());
      System.out.println("Lowest number in List : " + stats.getMin());
      System.out.println("Sum of all numbers : " + stats.getSum());
      System.out.println("Average of all numbers : " + stats.getAverage());
      System.out.println("Random Numbers: ");
		
      random.ints().limit(10).sorted().forEach(System.out::println);
		
      //parallel processing
      count = strings.parallelStream().filter(string -> string.isEmpty()).count();
      System.out.println("Empty Strings: " + count);
   }
	
   private static int getCountEmptyStringUsingJava7(List<String> strings){
      int count = 0;
		
      for(String string: strings){
		
         if(string.isEmpty()){
            count++;
         }
      }
      return count;
   }
	
   private static int getCountLength3UsingJava7(List<String> strings){
      int count = 0;
		
      for(String string: strings){
		
         if(string.length() == 3){
            count++;
         }
      }
      return count;
   }
	
   private static List<String> deleteEmptyStringsUsingJava7(List<String> strings){
      List<String> filteredList = new ArrayList<String>();
		
      for(String string: strings){
		
         if(!string.isEmpty()){
             filteredList.add(string);
         }
      }
      return filteredList;
   }
	
   private static String getMergedStringUsingJava7(List<String> strings, String separator){
      StringBuilder stringBuilder = new StringBuilder();
		
      for(String string: strings){
		
         if(!string.isEmpty()){
            stringBuilder.append(string);
            stringBuilder.append(separator);
         }
      }
      String mergedString = stringBuilder.toString();
      return mergedString.substring(0, mergedString.length()-2);
   }
	
   private static List<Integer> getSquares(List<Integer> numbers){
      List<Integer> squaresList = new ArrayList<Integer>();
		
      for(Integer number: numbers){
         Integer square = new Integer(number.intValue() * number.intValue());
			
         if(!squaresList.contains(square)){
            squaresList.add(square);
         }
      }
      return squaresList;
   }
	
   private static int getMax(List<Integer> numbers){
      int max = numbers.get(0);
		
      for(int i=1;i < numbers.size();i++){
		
         Integer number = numbers.get(i);
			
         if(number.intValue() > max){
            max = number.intValue();
         }
      }
      return max;
   }
	
   private static int getMin(List<Integer> numbers){
      int min = numbers.get(0);
		
      for(int i=1;i < numbers.size();i++){
         Integer number = numbers.get(i);
		
         if(number.intValue() < min){
            min = number.intValue();
         }
      }
      return min;
   }
	
   private static int getSum(List numbers){
      int sum = (int)(numbers.get(0));
		
      for(int i=1;i < numbers.size();i++){
         sum += (int)numbers.get(i);
      }
      return sum;
   }
	
   private static int getAverage(List<Integer> numbers){
      return getSum(numbers) / numbers.size();
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Testeras，

    $java Java8Tester

应该产生以下结果 -
```
Using Java 7:
List: [abc, , bc, efg, abcd, , jkl]
Empty Strings: 2
Strings of length 3: 3
Filtered List: [abc, bc, efg, abcd, jkl]
Merged String: abc, bc, efg, abcd, jkl
Squares List: [9, 4, 49, 25]
List: [1, 2, 13, 4, 15, 6, 17, 8, 19]
Highest number in List : 19
Lowest number in List : 1
Sum of all numbers : 85
Average of all numbers : 9
Random Numbers:
-1279735475
903418352
-1133928044
-1571118911
628530462
18407523
-881538250
-718932165
270259229
421676854
Using Java 8:
List: [abc, , bc, efg, abcd, , jkl]
Empty Strings: 2
Strings of length 3: 3
Filtered List: [abc, bc, efg, abcd, jkl]
Merged String: abc, bc, efg, abcd, jkl
Squares List: [9, 4, 49, 25]
List: [1, 2, 13, 4, 15, 6, 17, 8, 19]
Highest number in List : 19
Lowest number in List : 1
Sum of all numbers : 85
Average of all numbers : 9.444444444444445
Random Numbers:
-1009474951
-551240647
-2484714
181614550
933444268
1227850416
1579250773
1627454872
1683033687
1798939493
Empty Strings: 2
```



