---
title: Ehcache 3.2-Basic topics
date: 2017-07-08 22:09:07
tags: 缓存
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170708212240.png
blogexcerpt: EhCache 是一个纯Java的进程内缓存框架，具有快速、精干等特点，是Hibernate中默认的CacheProvider。如同先前的版本Ehcache，典型的处理方式Cache是通过CacheManager
keywords: xinxiucan,辛修灿,Ehcache,Ehcache 3.2-Basic topics
description: EhCache 是一个纯Java的进程内缓存框架，具有快速、精干等特点，是Hibernate中默认的CacheProvider。如同先前的版本Ehcache，典型的处理方式Cache是通过CacheManager
---

## Ehcache 3.2-Basic topics
**EhCache 是一个纯Java的进程内缓存框架，具有快速、精干等特点，是Hibernate中默认的CacheProvider。**

> **特性:**

> -  快速
> -  简单
> -  多种缓存策略
> -  缓存数据有两级：内存和磁盘，因此无需担心容量问题
> -  缓存数据会在虚拟机重启的过程中写入磁盘
> -  可以通过RMI、可插入API等方式进行分布式缓存
> -  具有缓存和缓存管理器的侦听接口
> -  支持多缓存管理器实例，以及一个实例的多个缓存区域
> -  提供Hibernate的缓存实现

如同先前的版本`Ehcache`，典型的处理方式`Cache`是通过`CacheManager`：

```java
package com.xinxiucan.cache;

import org.ehcache.Cache;
import org.ehcache.CacheManager;
import org.ehcache.config.builders.CacheConfigurationBuilder;
import org.ehcache.config.builders.CacheManagerBuilder;
import org.ehcache.config.builders.ResourcePoolsBuilder;

/**
 * @author xinxiucan
 * @date 2017年7月8日
 */
public class CacheManagerDemo {

	public static void main(String[] args) {
		CacheManager cacheManager = CacheManagerBuilder
				.newCacheManagerBuilder()
				.withCache("preConfigured", CacheConfigurationBuilder.newCacheConfigurationBuilder(Long.class, String.class, ResourcePoolsBuilder.heap(10)))
				.build();
		cacheManager.init();
		Cache<Long, String> preConfigured = cacheManager.getCache("preConfigured", Long.class, String.class);
		Cache<Long, String> myCache = cacheManager.createCache("myCache",
				CacheConfigurationBuilder.newCacheConfigurationBuilder(Long.class, String.class, ResourcePoolsBuilder.heap(10)).build());
		myCache.put(1L, "da one!");
		String value = myCache.get(1L);
		cacheManager.removeCache("preConfigured");
		cacheManager.close();
	}
}

```


> - 1.`CacheManagerBuilder.newCacheManagerBuilder`返回一个新`CacheManagerBuilder`实例。
> - 2.`.withCache`方法中第一个参数<String>：定义Cache别名 第二个参数<CacheConfiguration>:配置Cache
`.newCacheConfigurationBuilder()`方法对`CacheConfigurationBuilder`创建默认配置。
> - 3.调用build()返回一个完全实例化，但是没有初始化，
> - 4.`CacheManager`需要被初始化，这可以通过两种方式：`CacheManager.init()`或`CacheManagerBuilder.build(boolean init);` init必须为true;
> - 5.声明一个cache，通过调用cacheManager.getCache。注意alias=preConfigured, keyType=Long.class and valueType=String.class。要和之前定义的相同否则CacheManager会抛出ClassCastException
> - 6.CacheManager创建新的缓存实例
> - 7.新增一个Cache(key,value)
> - 8.缓存中获取值，通过调用cache.get(key)方法。如果key不存在，则返回Null。
> - 9.CacheManager.removeCache(String) 删除CacheManager
> - 10.关闭所有Cache实例。


### pom.xml配置

```java
<!-- https://mvnrepository.com/artifact/org.ehcache/ehcache -->
		<dependency>
			<groupId>org.ehcache</groupId>
			<artifactId>ehcache</artifactId>
			<version>3.2.2</version>
		</dependency>
```







