---
title: spring中通过实现ApplicationContextAware接口获取Bean
date: 2017-08-11 09:53:30
tags: spring
categories: spring
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170805205407.png
keywords: spring,spring Aware
description: spring中通过实现ApplicationContextAware接口获取Bean,ApplicationContextAware接口的作用,当一个类实现了这个接口（ApplicationContextAware）之后，Aware接口的Bean在被初始之后，可以取得一些相对应的资源，这个类可以直接获取spring 配置文件中 所有引用（注入）到的bean对象。
---

## spring中通过实现ApplicationContextAware接口获取Bean
### ApplicationContextAware接口的作用

当一个类实现了这个接口（ApplicationContextAware）之后，Aware接口的Bean在被初始之后，可以取得一些相对应的资源，这个类可以直接获取spring 配置文件中 所有引用（注入）到的bean对象。

### 项目使用


```java
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

@Component
public class SpringUtil implements ApplicationContextAware {

	private static ApplicationContext applicationContext;

	// 获取applicationContext
	public static ApplicationContext getApplicationContext() {
		return applicationContext;
	}

	// 通过name获取 Bean.
	public static Object getBean(String name) {
		return getApplicationContext().getBean(name);
	}

	// 通过class获取Bean.
	public static <T> T getBean(Class<T> clazz) {
		return getApplicationContext().getBean(clazz);
	}

	// 通过name,以及Clazz返回指定的Bean
	public static <T> T getBean(String name, Class<T> clazz) {
		return getApplicationContext().getBean(name, clazz);
	}

	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		if (SpringUtil.applicationContext == null) {
			SpringUtil.applicationContext = applicationContext;
		}
	}

}
```

**直接通过静态方法获取Bean**

```
TestBeanService testBeanService = SpringUtil.getBean(TestBeanService.class);
```





