---
title: Java 8 - New Date/Time API
date: 2017-07-19 21:15:57
tags: java8
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/Java8NewDateTimeAPI.png
keywords: java,java8,lambda
description: 使用Java 8，引入了一个新的Date-Time API来解决旧的日期时间API的以下缺点 -线程不安全-java.util.Date不是线程安全的，因此开发人员必须在使用日期时处理并发问题。新的日期时间API是不可变的，没有setter方法。设计差- 默认日期从1900开始，月份从1开始，日期从0开始，所以没有一致性。旧的API对日期操作的方法较少。新的API为此类操作提供了许多实用方法。困难的时区处理 - 开发人员不得不编写大量代码来处理时区问题。新的API已经开发出了保持领域特定的设计。
---

## Java 8 - New Date/Time API

使用Java 8，引入了一个新的Date-Time API来解决旧的日期时间API的以下缺点 -

![](http://osewdhh4j.bkt.clouddn.com/Java8NewDateTimeAPI.png)

 - **线程不安全** -  java.util.Date不是线程安全的，因此开发人员必须在使用日期时处理并发问题。新的日期时间API是不可变的，没有setter方法。
 - **设计差** - 默认日期从1900开始，月份从1开始，日期从0开始，所以没有一致性。旧的API对日期操作的方法较少。新的API为此类操作提供了许多实用方法。
 - **困难的时区处理** - 开发人员不得不编写大量代码来处理时区问题。新的API已经开发出了保持领域特定的设计。

Java 8在java.time包下引入了一个新的日期时间API 。以下是java.time包中介绍的一些重要类 -

 - **本地** - 简化的日期时间API，没有时区处理的复杂性。
 - **分区** - 处理各种时区的专用日期API。

### 本地日期时间API
LocalDate / LocalTime和LocalDateTime类简化了不需要时区的开发。让我们看看他们在行动 -

**Java8Tester.java**
```java
import java.time.LocalDate;
import java.time.LocalTime;
import java.time.LocalDateTime;
import java.time.Month;

public class Java8Tester {
   public static void main(String args[]){
      Java8Tester java8tester = new Java8Tester();
      java8tester.testLocalDateTime();
   }
	
   public void testLocalDateTime(){
	
      // Get the current date and time
      LocalDateTime currentTime = LocalDateTime.now();
      System.out.println("Current DateTime: " + currentTime);
		
      LocalDate date1 = currentTime.toLocalDate();
      System.out.println("date1: " + date1);
		
      Month month = currentTime.getMonth();
      int day = currentTime.getDayOfMonth();
      int seconds = currentTime.getSecond();
		
      System.out.println("Month: " + month +"day: " + day +"seconds: " + seconds);
		
      LocalDateTime date2 = currentTime.withDayOfMonth(10).withYear(2012);
      System.out.println("date2: " + date2);
		
      //12 december 2014
      LocalDate date3 = LocalDate.of(2014, Month.DECEMBER, 12);
      System.out.println("date3: " + date3);
		
      //22 hour 15 minutes
      LocalTime date4 = LocalTime.of(22, 15);
      System.out.println("date4: " + date4);
		
      //parse a string
      LocalTime date5 = LocalTime.parse("20:15:30");
      System.out.println("date5: " + date5);
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -
```
Current DateTime: 2014-12-09T11:00:45.457
date1: 2014-12-09
Month: DECEMBERday: 9seconds: 45
date2: 2012-12-10T11:00:45.457
date3: 2014-12-12
date4: 22:15
date5: 20:15:30
```
**分区日期时间API**
在考虑时区时使用分区日期时间API。让我们看看他们在行动 -

Java8Tester.java
```
import java.time.ZonedDateTime;
import java.time.ZoneId;

public class Java8Tester {
   public static void main(String args[]){
      Java8Tester java8tester = new Java8Tester();
      java8tester.testZonedDateTime();
   }
	
   public void testZonedDateTime(){
	
      // Get the current date and time
      ZonedDateTime date1 = ZonedDateTime.parse("2007-12-03T10:15:30+05:30[Asia/Karachi]");
      System.out.println("date1: " + date1);
		
      ZoneId id = ZoneId.of("Europe/Paris");
      System.out.println("ZoneId: " + id);
		
      ZoneId currentZone = ZoneId.systemDefault();
      System.out.println("CurrentZone: " + currentZone);
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -

    date1: 2007-12-03T10:15:30+05:00[Asia/Karachi]
    ZoneId: Europe/Paris
    CurrentZone: Etc/UTC

### 计时单位枚举
`java.time.temporal.ChronoUnit`枚举在Java 8中添加，以替换旧API中使用的整数值来表示日，月等。让我们看看它们在行动中 -

**Java8Tester.java**
```java
import java.time.LocalDate;
import java.time.temporal.ChronoUnit;

public class Java8Tester {
   public static void main(String args[]){
      Java8Tester java8tester = new Java8Tester();
      java8tester.testChromoUnits();
   }
	
   public void testChromoUnits(){
	
      //Get the current date
      LocalDate today = LocalDate.now();
      System.out.println("Current date: " + today);
		
      //add 1 week to the current date
      LocalDate nextWeek = today.plus(1, ChronoUnit.WEEKS);
      System.out.println("Next week: " + nextWeek);
		
      //add 1 month to the current date
      LocalDate nextMonth = today.plus(1, ChronoUnit.MONTHS);
      System.out.println("Next month: " + nextMonth);
		
      //add 1 year to the current date
      LocalDate nextYear = today.plus(1, ChronoUnit.YEARS);
      System.out.println("Next year: " + nextYear);
		
      //add 10 years to the current date
      LocalDate nextDecade = today.plus(1, ChronoUnit.DECADES);
      System.out.println("Date after ten year: " + nextDecade);
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

应该产生以下结果 -

    Current date: 2014-12-10
    Next week: 2014-12-17
    Next month: 2015-01-10
    Next year: 2015-12-10
    Date after ten year: 2024-12-10

### 期间与期限
使用Java 8，引入了两个专门的课程来处理时间差异 -

 - **期间** - 它处理基于日期的时间量。
 - **持续时间** - 它处理时间的时间量

。

让我们以一个例子来理解他们 -

**Java8Tester.java**
```java
import java.time.temporal.ChronoUnit;

import java.time.LocalDate;
import java.time.LocalTime;
import java.time.Duration;
import java.time.Period;

public class Java8Tester {
   public static void main(String args[]){
      Java8Tester java8tester = new Java8Tester();
      java8tester.testPeriod();
      java8tester.testDuration();
   }
	
   public void testPeriod(){
	
      //Get the current date
      LocalDate date1 = LocalDate.now();
      System.out.println("Current date: " + date1);
		
      //add 1 month to the current date
      LocalDate date2 = date1.plus(1, ChronoUnit.MONTHS);
      System.out.println("Next month: " + date2);
      
      Period period = Period.between(date2, date1);
      System.out.println("Period: " + period);
   }
	
   public void testDuration(){
      LocalTime time1 = LocalTime.now();
      Duration twoHours = Duration.ofHours(2);
		
      LocalTime time2 = time1.plus(twoHours);
      Duration duration = Duration.between(time1, time2);
		
      System.out.println("Duration: " + duration);
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -

    Current date: 2014-12-10
    Next month: 2015-01-10
    Period: P-1M
    Duration: PT2H

时间调整器
TemporalAdjuster用于执行日期数学。例如，获得“本月第二个星期六”或“下周二”。让我们看一个例子 -

**Java8Tester.java**
```java
import java.time.LocalDate;
import java.time.temporal.TemporalAdjusters;
import java.time.DayOfWeek;

public class Java8Tester {
   public static void main(String args[]){
      Java8Tester java8tester = new Java8Tester();
      java8tester.testAdjusters();
   }
	
   public void testAdjusters(){
	
      //Get the current date
      LocalDate date1 = LocalDate.now();
      System.out.println("Current date: " + date1);
		
      //get the next tuesday
      LocalDate nextTuesday = date1.with(TemporalAdjusters.next(DayOfWeek.TUESDAY));
      System.out.println("Next Tuesday on : " + nextTuesday);
		
      //get the second saturday of next month
      LocalDate firstInYear = LocalDate.of(date1.getYear(),date1.getMonth(), 1);
      LocalDate secondSaturday = firstInYear.with(TemporalAdjusters.nextOrSame(DayOfWeek.SATURDAY)).with(TemporalAdjusters.next(DayOfWeek.SATURDAY));
      System.out.println("Second Saturday on : " + secondSaturday);
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

应该产生以下结果 -

    Current date: 2014-12-10
    Next Tuesday on : 2014-12-16
    Second Saturday on : 2014-12-13

向后兼容性
将一个toInstant（）方法添加到原始的Date和Calendar对象中，可以将它们转换为新的Date-Time API。使用一个Instant（Insant，ZoneId）方法来获取一个LocalDateTime或ZonedDateTime对象。让我们以一个例子来理解它 -

**Java8Tester.java**
```java
import java.time.LocalDateTime;
import java.time.ZonedDateTime;

import java.util.Date;

import java.time.Instant;
import java.time.ZoneId;

public class Java8Tester {
   public static void main(String args[]){
      Java8Tester java8tester = new Java8Tester();
      java8tester.testBackwardCompatability();
   }
	
   public void testBackwardCompatability(){
	
      //Get the current date
      Date currentDate = new Date();
      System.out.println("Current date: " + currentDate);
		
      //Get the instant of current date in terms of milliseconds
      Instant now = currentDate.toInstant();
      ZoneId currentZone = ZoneId.systemDefault();
		
      LocalDateTime localDateTime = LocalDateTime.ofInstant(now, currentZone);
      System.out.println("Local date: " + localDateTime);
		
      ZonedDateTime zonedDateTime = ZonedDateTime.ofInstant(now, currentZone);
      System.out.println("Zoned date: " + zonedDateTime);
   }
}
```
**验证结果**
使用javac编译器编译类，如下所示：

    $javac Java8Tester.java

现在运行Java8Tester如下 -

    $java Java8Tester

它应该产生以下输出 -

    Current date: Wed Dec 10 05:44:06 UTC 2014
    Local date: 2014-12-10T05:44:06.635
    Zoned date: 2014-12-10T05:44:06.635Z[Etc/UTC]

