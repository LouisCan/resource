---
title: JPA Advanced Mappings(映射)
date: 2017-07-30 21:51:12
tags: java
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170805205407.png
keywords: java,jpa,JPA,jpa映射,jpa Mapping
description: JPA是一个使用java规范发布的库。因此，它支持所有面向对象的实体持久性概念。Staff, TeachingStaff, NonTeachingStaff关系..
---

## JPA Advanced Mappings(映射)

JPA是一个使用java规范发布的库。因此，它支持所有面向对象的实体持久性概念。

![](http://osewdhh4j.bkt.clouddn.com/20170730214817.png)

### 继承策略
继承是面向对象语言的核心概念，因此我们可以在实体之间使用继承关系或策略。JPA支持三种类型的继承策略，如SINGLE_TABLE，JOINED_TABLE和TABLE_PER_CONCRETE_CLASS。

Staff, TeachingStaff, NonTeachingStaff关系：

![](http://osewdhh4j.bkt.clouddn.com/20170730213125.png)

在上图中，Staff是一个实体，TeachingStaff和NonTeachingStaff是员工的子实体。在这里我们将讨论上述三个继承的策略。
### 单表策略
单表策略采用所有类字段（超类和子类），并将其映射到称为SINGLE_TABLE策略的单个表中。鉴别器值在区分一个表中三个实体的值时起关键作用。

让我们考虑上面的例子，TeachingStaff和NonTeachingStaff是类员工的子类。提醒继承的概念（是通过子类继承超类的属性的机制），因此sid，sname是属于TeachingStaff和NonTeachingStaff的字段。创建一个JPA项目。本项目的所有模块如下：

**创建实体**
在`“src”`包下创建一个名为`“com.tutorialspoint.eclipselink.entity”` 的包。在给定的包下创建一个名为Staff.java的新Java类。Staff实体类显示如下：

```java
package com.tutorialspoint.eclipselink.entity;

import java.io.Serializable;

import javax.persistence.DiscriminatorColumn;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Inheritance;
import javax.persistence.InheritanceType;
import javax.persistence.Table;

@Entity
@Table
@Inheritance( strategy = InheritanceType.SINGLE_TABLE )
@DiscriminatorColumn( name = "type" )

public class Staff implements Serializable {
   @Id
   @GeneratedValue( strategy = GenerationType.AUTO )
   
   private int sid;
   private String sname;
   
   public Staff( int sid, String sname ) {
      super( );
      this.sid = sid;
      this.sname = sname;
   }
   
   public Staff( ) {
      super( );
   }
   
   public int getSid( ) {
      return sid;
   }
   
   public void setSid( int sid ) {
      this.sid = sid;
   }
   
   public String getSname( ) {
      return sname;
   }
   
   public void setSname( String sname ) {
      this.sname = sname;
   }
}
```

在上述代码中，@DescriminatorColumn指定字段名称（type），其值显示剩余的（Teaching and NonTeachingStaff）字段。

在com.tutorialspoint.eclipselink.entity包下创建一个名为TeachingStaff.java的 Staff类的子类（class）。TeachingStaff实体类显示如下：

```java
package com.tutorialspoint.eclipselink.entity;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@DiscriminatorValue( value="TS" )
public class TeachingStaff extends Staff {

   private String qualification;
   private String subjectexpertise;

   public TeachingStaff( int sid, String sname, 
   
   String qualification,String subjectexpertise ) {
      super( sid, sname );
      this.qualification = qualification;
      this.subjectexpertise = subjectexpertise;
   }

   public TeachingStaff( ) {
      super( );
   }

   public String getQualification( ){
      return qualification;
   }

   public void setQualification( String qualification ){
      this.qualification = qualification;
   }

   public String getSubjectexpertise( ) {
      return subjectexpertise;
   }

   public void setSubjectexpertise( String subjectexpertise ){
      this.subjectexpertise = subjectexpertise;
   }
}
```

在com.tutorialspoint.eclipselink.entity包下创建一个名为NonTeachingStaff.java的 Staff类的子类（class）。NonTeachingStaff实体类显示如下：

```java
package com.tutorialspoint.eclipselink.entity;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@DiscriminatorValue( value = "NS" )

public class NonTeachingStaff extends Staff {
   private String areaexpertise;

   public NonTeachingStaff( int sid, String sname, String areaexpertise ) {
      super( sid, sname );
      this.areaexpertise = areaexpertise;
   }

   public NonTeachingStaff( ) {
      super( );
   }

   public String getAreaexpertise( ) {
      return areaexpertise;
   }

   public void setAreaexpertise( String areaexpertise ){
      this.areaexpertise = areaexpertise;
   }
}
```

**persistence.xml中**

Persistence.xml文件包含实体类的数据库和注册信息的配置信息。xml文件显示如下：

```java
<?xml version="1.0" encoding="UTF-8"?>

<persistence version="2.0" xmlns="http://java.sun.com/xml/ns/persistence"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
   xsi:schemaLocation="http://java.sun.com/xml/ns/persistence 
   http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd">

   <persistence-unit name="Eclipselink_JPA" transaction-type="RESOURCE_LOCAL">
   
      <class>com.tutorialspoint.eclipselink.entity.Staff</class>
      <class>com.tutorialspoint.eclipselink.entity.NonTeachingStaff</class>
      <class>com.tutorialspoint.eclipselink.entity.TeachingStaff</class>
      
      <properties>
         <property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:3306/jpadb"/>
         <property name="javax.persistence.jdbc.user" value="root"/>
         <property name="javax.persistence.jdbc.password" value="root"/>
         <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver"/>
         <property name="eclipselink.logging.level" value="FINE"/>
         <property name="eclipselink.ddl-generation" value="create-tables"/>
      </properties>
      
   </persistence-unit>
</persistence>
```

**Service class**

服务类是业务组件的实现部分。在名为'com.tutorialspoint.eclipselink.service'的'src'包下创建一个包。

在给定的包下创建一个名为SaveClient.java的类来存储Staff，TeachingStaff和NonTeachingStaff类字段。SaveClient类显示如下：

```java
package com.tutorialspoint.eclipselink.service;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;
import com.tutorialspoint.eclipselink.entity.NonTeachingStaff;
import com.tutorialspoint.eclipselink.entity.TeachingStaff;

public class SaveClient {

   public static void main( String[ ] args ) {
   
      EntityManagerFactory emfactory = Persistence.createEntityManagerFactory( "Eclipselink_JPA" );
      EntityManager entitymanager = emfactory.createEntityManager( );
      entitymanager.getTransaction( ).begin( );

      //Teaching staff entity 
      TeachingStaff ts1=new TeachingStaff(1,"Gopal","MSc MEd","Maths");
      TeachingStaff ts2=new TeachingStaff(2, "Manisha", "BSc BEd", "English");
      
      //Non-Teaching Staff entity
      NonTeachingStaff nts1=new NonTeachingStaff(3, "Satish", "Accounts");
      NonTeachingStaff nts2=new NonTeachingStaff(4, "Krishna", "Office Admin");

      //storing all entities
      entitymanager.persist(ts1);
      entitymanager.persist(ts2);
      entitymanager.persist(nts1);
      entitymanager.persist(nts2);
      
      entitymanager.getTransaction().commit();
      
      entitymanager.close();
      emfactory.close();
   }
}
```

编译和执行上述程序后，您将在Eclipse IDE的控制面板中收到通知。检查MySQL工作台的输出。表格格式的输出如下所示：

Sid	|Type|	Sname|	Areaexpertise|	Qualification	|Subjectexpertise
----|----|----|----|----|----
1	|TS	|Gopal|	-|MSC MED	|Maths
2	|TS	|Manisha|-|		BSC BED|	English
3	|NS	|Satish	|Accounts		
4	|NS	|Krishna|	Office Admin		

最后，您将获得包含所有三类字段的单表，并与名为“Type”（字段）的discriminator列不同。

### 加盟表策略
加入表策略是共享引用的列，其中包含唯一值以加入表并进行简单的事务。让我们考虑与上述相同的例子。

创建JPA项目。所有项目模块如下所示：

**创建实体**
在“src”包下创建一个名为“com.tutorialspoint.eclipselink.entity” 的包。在给定的包下创建一个名为Staff.java的新Java类。Staff实体类显示如下：

```java
package com.tutorialspoint.eclipselink.entity;

import java.io.Serializable;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Inheritance;
import javax.persistence.InheritanceType;
import javax.persistence.Table;

@Entity
@Table
@Inheritance( strategy = InheritanceType.JOINED )

public class Staff implements Serializable {

   @Id
   @GeneratedValue( strategy = GenerationType.AUTO )
   
   private int sid;
   private String sname;
   
   public Staff( int sid, String sname ) {
      super( );
      this.sid = sid;
      this.sname = sname;
   }
   
   public Staff( ) {
      super( );
   }
   
   public int getSid( ) {
      return sid;
   }
   
   public void setSid( int sid ) {
      this.sid = sid;
   }
   
   public String getSname( ) {
      return sname;
   }
   
   public void setSname( String sname ) {
      this.sname = sname;
   }
}
```

在com.tutorialspoint.eclipselink.entity包下创建一个名为TeachingStaff.java的 Staff类的子类（class）。TeachingStaff实体类显示如下：

```java
package com.tutorialspoint.eclipselink.entity;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@PrimaryKeyJoinColumn(referencedColumnName="sid")

public class TeachingStaff extends Staff {
   private String qualification;
   private String subjectexpertise;

   public TeachingStaff( int sid, String sname, 
   
   String qualification,String subjectexpertise ) {
      super( sid, sname );
      this.qualification = qualification;
      this.subjectexpertise = subjectexpertise;
   }

   public TeachingStaff( ) {
      super( );
   }

   public String getQualification( ){
      return qualification;
   }

   public void setQualification( String qualification ){
      this.qualification = qualification;
   }

   public String getSubjectexpertise( ) {
      return subjectexpertise;
   }

   public void setSubjectexpertise( String subjectexpertise ){
      this.subjectexpertise = subjectexpertise;
   }
}
```

在com.tutorialspoint.eclipselink.entity包下创建一个名为NonTeachingStaff.java的 Staff类的子类（class）。NonTeachingStaff实体类显示如下：

```java
package com.tutorialspoint.eclipselink.entity;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@PrimaryKeyJoinColumn(referencedColumnName="sid")

public class NonTeachingStaff extends Staff {
   private String areaexpertise;

   public NonTeachingStaff( int sid, String sname, String areaexpertise ) {
      super( sid, sname );
      this.areaexpertise = areaexpertise;
   }

   public NonTeachingStaff( ) {
      super( );
   }

   public String getAreaexpertise( ) {
      return areaexpertise;
   }

   public void setAreaexpertise( String areaexpertise ) {
      this.areaexpertise = areaexpertise;
   }
}
```

### persistence.xml中
Persistence.xml文件包含实体类的数据库和注册信息的配置信息。xml文件显示如下：

```
<?xml version = "1.0" encoding = "UTF-8"?>

<persistence version = "2.0" xmlns = "http://java.sun.com/xml/ns/persistence" 
   xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance" 
   xsi:schemaLocation = "http://java.sun.com/xml/ns/persistence 
   http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd">
   
   <persistence-unit name = "Eclipselink_JPA" transaction-type = "RESOURCE_LOCAL">
      <class>com.tutorialspoint.eclipselink.entity.Staff</class>
      <class>com.tutorialspoint.eclipselink.entity.NonTeachingStaff</class>
      <class>com.tutorialspoint.eclipselink.entity.TeachingStaff</class>
      
      <properties>
         <property name = "javax.persistence.jdbc.url" value = "jdbc:mysql://localhost:3306/jpadb"/>
         <property name = "javax.persistence.jdbc.user" value = "root"/>
         <property name = "javax.persistence.jdbc.password" value = "root"/>
         <property name = "javax.persistence.jdbc.driver" value = "com.mysql.jdbc.Driver"/>
         <property name = "eclipselink.logging.level" value = "FINE"/>
         <property name = "eclipselink.ddl-generation" value = "create-tables"/>
      </properties>
      
   </persistence-unit>
</persistence>
```

### 服务类
服务类是业务组件的实现部分。在名为'com.tutorialspoint.eclipselink.service'的'src'包下创建一个包。

在给定的包下创建一个名为SaveClient.java的类来存储Staff，TeachingStaff和NonTeachingStaff类字段。然后SaveClient类如下：

```
package com.tutorialspoint.eclipselink.service;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;
import com.tutorialspoint.eclipselink.entity.NonTeachingStaff;
import com.tutorialspoint.eclipselink.entity.TeachingStaff;

public class SaveClient {
   public static void main( String[ ] args ) {
      EntityManagerFactory emfactory = Persistence.createEntityManagerFactory( "Eclipselink_JPA" );
      EntityManager entitymanager = emfactory.createEntityManager( );
      entitymanager.getTransaction( ).begin( );

      //Teaching staff entity 
      TeachingStaff ts1 = new TeachingStaff(1,"Gopal","MSc MEd","Maths");
      TeachingStaff ts2 = new TeachingStaff(2, "Manisha", "BSc BEd", "English");
      
      //Non-Teaching Staff entity
      NonTeachingStaff nts1 = new NonTeachingStaff(3, "Satish", "Accounts");
      NonTeachingStaff nts2 = new NonTeachingStaff(4, "Krishna", "Office Admin");

      //storing all entities
      entitymanager.persist(ts1);
      entitymanager.persist(ts2);
      entitymanager.persist(nts1);
      entitymanager.persist(nts2);

      entitymanager.getTransaction().commit();
      entitymanager.close();
      emfactory.close();
   }
}

```

编译和执行上述程序后，您将在Eclipse IDE的控制面板中收到通知。输出检查MySQL工作台如下：

这里创建了三个表格，表格格式的员工表格的结果如下所示：

Sid	|Dtype|	Sname
-----|-----|-----
1	|TeachingStaff	|Gopal
2	|TeachingStaff	|Manisha
3	|NonTeachingStaff|	Satish
4	|NonTeachingStaff|	Krishna

**TeachingStaff**表的结果如表格所示：

Sid	|Qualification	|Subjectexpertise
-----|-----|-----
1	|MSC MED	|Maths
2	|BSC BED	|English

在上表中sid是外键（参考字段表单staff表）NonTeachingStaff表的结果如表格所示：

Sid	|Areaexpertise
-----|----
3	|Accounts
4	|Office Admin

最后，这三个表分别使用它们的字段创建，SID字段由所有三个表共享。在员工表SID中是主键，在剩余的（TeachingStaff和NonTeachingStaff）表中SID是外键。

### 每个类策略表
每个类策略的表是为每个子实体创建一个表。工作人员表将被创建，但它将包含空记录。Staff表的字段值必须由TeachingStaff和NonTeachingStaff表共享。

让我们考虑与上述相同的例子。本项目的所有模块如下所示：

**创建实体**
在“src”包下创建一个名为“com.tutorialspoint.eclipselink.entity” 的包。在给定的包下创建一个名为Staff.java的新Java类。Staff实体类显示如下：

```
package com.tutorialspoint.eclipselink.entity;

import java.io.Serializable;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Inheritance;
import javax.persistence.InheritanceType;
import javax.persistence.Table;

@Entity
@Table
@Inheritance( strategy = InheritanceType.TABLE_PER_CLASS )

public class Staff implements Serializable {

   @Id
   @GeneratedValue( strategy = GenerationType.AUTO )

   private int sid;
   private String sname;

   public Staff( int sid, String sname ) {
      super( );
      this.sid = sid;
      this.sname = sname;
   }

   public Staff( ) {
      super( );
   }

   public int getSid( ) {
      return sid;
   }

   public void setSid( int sid ) {
      this.sid = sid;
   }

   public String getSname( ) {
      return sname;
   }

   public void setSname( String sname ) {
      this.sname = sname;
   }
}
```

在com.tutorialspoint.eclipselink.entity包下创建一个名为TeachingStaff.java的 Staff类的子类（class）。TeachingStaff实体类显示如下：

```
package com.tutorialspoint.eclipselink.entity;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
public class TeachingStaff extends Staff {
   private String qualification;
   private String subjectexpertise;

   public TeachingStaff( int sid, String sname, String qualification, String subjectexpertise ) {
      super( sid, sname );
      this.qualification = qualification;
      this.subjectexpertise = subjectexpertise;
   }

   public TeachingStaff( ) {
      super( );
   }

   public String getQualification( ){
      return qualification;
   }
   
   public void setQualification( String qualification ) {
      this.qualification = qualification;
   }

   public String getSubjectexpertise( ) {
      return subjectexpertise;
   }

   public void setSubjectexpertise( String subjectexpertise ){
      this.subjectexpertise = subjectexpertise;
   }
}
```

在com.tutorialspoint.eclipselink.entity包下创建一个名为NonTeachingStaff.java的 Staff类的子类（class）。NonTeachingStaff实体类显示如下：

```
package com.tutorialspoint.eclipselink.entity;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
public class NonTeachingStaff extends Staff {
   private String areaexpertise;

   public NonTeachingStaff( int sid, String sname, String areaexpertise ) {
      super( sid, sname );
      this.areaexpertise = areaexpertise;
   }

   public NonTeachingStaff( ) {
      super( );
   }

   public String getAreaexpertise( ) {
      return areaexpertise;
   }

   public void setAreaexpertise( String areaexpertise ) {
      this.areaexpertise = areaexpertise;
   }
}
```

**persistence.xml中**
Persistence.xml文件包含实体类的数据库和注册信息的配置信息。xml文件显示如下：

```
<?xml version="1.0" encoding = "UTF-8"?>
<persistence version = "2.0" xmlns = "http://java.sun.com/xml/ns/persistence"
   xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance" 
   xsi:schemaLocation = "http://java.sun.com/xml/ns/persistence 
   http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd">

   <persistence-unit name = "Eclipselink_JPA" transaction-type = "RESOURCE_LOCAL">
      <class>com.tutorialspoint.eclipselink.entity.Staff</class>
      <class>com.tutorialspoint.eclipselink.entity.NonTeachingStaff</class>
      <class>com.tutorialspoint.eclipselink.entity.TeachingStaff</class>
      
      <properties>
         <property name = "javax.persistence.jdbc.url" value = "jdbc:mysql://localhost:3306/jpadb"/>
         <property name = "javax.persistence.jdbc.user" value = "root"/>
         <property name = "javax.persistence.jdbc.password" value = "root"/>
         <property name = "javax.persistence.jdbc.driver" value = "com.mysql.jdbc.Driver"/>
         <property name = "eclipselink.logging.level" value = "FINE"/>
         <property name = "eclipselink.ddl-generation" value="create-tables"/>
      </properties>
      
   </persistence-unit>
</persistence>
```

### 服务类
服务类是业务组件的实现部分。在名为'com.tutorialspoint.eclipselink.service'的'src'包下创建一个包。

在给定的包下创建一个名为SaveClient.java的类来存储Staff，TeachingStaff和NonTeachingStaff类字段。SaveClient类显示如下：

```java
package com.tutorialspoint.eclipselink.service;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

import com.tutorialspoint.eclipselink.entity.NonTeachingStaff;
import com.tutorialspoint.eclipselink.entity.TeachingStaff;

public class SaveClient {
   public static void main( String[ ] args ) {
      EntityManagerFactory emfactory = Persistence.createEntityManagerFactory( "Eclipselink_JPA" );
      EntityManager entitymanager = emfactory.createEntityManager( );
      entitymanager.getTransaction( ).begin( );

      //Teaching staff entity 
      TeachingStaff ts1 = new TeachingStaff(1,"Gopal","MSc MEd","Maths");
      TeachingStaff ts2 = new TeachingStaff(2, "Manisha", "BSc BEd", "English");
      
      //Non-Teaching Staff entity
      NonTeachingStaff nts1 = new NonTeachingStaff(3, "Satish", "Accounts");
      NonTeachingStaff nts2 = new NonTeachingStaff(4, "Krishna", "Office Admin");

      //storing all entities
      entitymanager.persist(ts1);
      entitymanager.persist(ts2);
      entitymanager.persist(nts1);
      entitymanager.persist(nts2);

      entitymanager.getTransaction().commit();
      entitymanager.close();
      emfactory.close();
   }
}
```

编译和执行上述程序后，您将在Eclipse IDE的控制面板中收到通知。对于输出，检查MySQL工作台如下：

这里创建了三个表，Staff表包含空记录。

TeachingStaff的结果如表格所示：

Sid	|Qualification	|Sname	|Subjectexpertise
---|----|---|---
1	|MSC MED	|Gopal	|Maths
2	|BSC BED	|Manisha	|English

上表TeachingStaff包含员工和教学实体的领域。

NonTeachingStaff的结果如表格所示：

Sid	|Areaexpertise|	Sname
-----|----|----
3	|Accounts	|Satish
4	|Office Admin	|Krishna

以上表格NonTeachingStaff包含Staff和NonTeachingStaff实体的字段。




