---
title: XSS漏洞解决方案
date: 2017-07-01 20:58:51
tags: springboot
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/xxs.png
blogexcerpt: 跨站脚本攻击(Cross Site Scripting)，为不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS。恶意攻击者往Web页面里插入恶意Script代码，当用户浏览该页之时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的特殊目的。
---

**XSS漏洞解决方案**

**一、XSS**
`跨站脚本攻击`(Cross Site Scripting)，为不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS。
恶意攻击者往Web页面里插入恶意`Script`代码，当用户浏览该页之时，嵌入其中Web里面的`Script`代码会被执行，从而达到恶意攻击用户的特殊目的。

**1)、**恶意用户，在一些公共区域（例如，建议提交表单或消息公共板的输入表单）输入一些文本，这些文本被其它用户看到，但这些文本不仅仅是他们要输入的文本，同时还包括一些可以在客户端执行的脚本。

**2)、**恶意提交这个表单。

**3)、**其他用户看到这个包括恶意脚本的页面并执行，获取用户的cookie等敏感信息。

- - -

**二、解决方案**

**1.建立HttpServletRequestWapper的包装类
包装类是对用户发送的请求进行包装，把request中包含XSS代码过滤。**

**2.Filter过滤器实现对Request的过滤**

**三、代码实现**

作者以springboot为例：

**在eclipse中构建一个springboot demo** 【下图：】

![](http://osewdhh4j.bkt.clouddn.com/20170701203427.png)

**1. 创建springboot 程序入口**


```java
package com.xinxiucan.web;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

@SpringBootApplication
@ServletComponentScan
public class AppApplication {

    public static void main(String[] args) throws Exception {
		SpringApplication.run(AppApplication.class, args);
	}

}
```

```java
package com.xinxiucan.web.filter;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;

/**
 * @author xinxiucan
 * @date 2017年7月1日
 */
@WebFilter(urlPatterns = "/api/*", filterName = "indexFilter")
public class IndexFilter implements Filter {

    @Override
	public void init(FilterConfig filterConfig) throws ServletException {
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain) throws IOException,
			ServletException {
		XssHttpServletRequestWrapper xssRequest = new XssHttpServletRequestWrapper((HttpServletRequest) request);
		filterChain.doFilter(xssRequest, response);
	}

	@Override
	public void destroy() {
	}
}
```


```java
package com.xinxiucan.web.filter;

import java.util.Map;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;

/**
 * @author xinxiucan
 * @date 2017年7月1日
 */
public class XssHttpServletRequestWrapper extends HttpServletRequestWrapper {
    HttpServletRequest orgRequest = null;

	public XssHttpServletRequestWrapper(HttpServletRequest request) {
		super(request);
	}

	/**
	 * 覆盖getParameter方法，将参数名和参数值都做xss过滤。
	 * 如果需要获得原始的值，则通过super.getParameterValues(name)来获取
	 * getParameterNames,getParameterValues和getParameterMap也可能需要覆盖
	 */
	@Override
	public String getParameter(String name) {
		String value = super.getParameter(xssEncode(name));
		if (value != null) {
			value = xssEncode(value);
		}
		return value;
	}

	@Override
	public String[] getParameterValues(String name) {
		String[] value = super.getParameterValues(name);
		if (value != null ) {
			for (int i = 0; i < value.length; i++) {
				value[i] = xssEncode(value[i]);
			}
		}
		return value;
	}

	@Override
	public Map getParameterMap() {
		return super.getParameterMap();
	}

	/**
	 * 覆盖getHeader方法，将参数名和参数值都做xss过滤。 如果需要获得原始的值，则通过super.getHeaders(name)来获取
	 * getHeaderNames 也可能需要覆盖
	 */
	@Override
	public String getHeader(String name) {
		String value = super.getHeader(xssEncode(name));
		if (value != null && !name.equals("Accept")) {
			value = xssEncode(value);
		}
		return value;
	}

	/**
	 * 将容易引起xss漏洞的半角字符直接替换成全角字符 在保证不删除数据的情况下保存
	 * 
	 * @param s
	 * @return 过滤后的值
	 */
	private static String xssEncode(String s) {
		System.out.println(s);
		if (s == null || s.isEmpty()) {
			return s;
		}
		StringBuilder sb = new StringBuilder(s.length() + 16);
		for (int i = 0; i < s.length(); i++) {
			char ch = s.charAt(i);
			switch (ch) {
			case '&':
				sb.append("＆");
				break;
			case '<':
				sb.append("《");
				break;
			case '>':
				sb.append("》");
				break;
			case '"':
				sb.append("“");
				break;
			case '\'':
				sb.append("＼");
				break;
			case '/':
				sb.append("／");
				break;
			default:
				sb.append(ch);
			}
		}
		return sb.toString();
	}
}

```
**2. 测试**

```java
package com.xinxiucan.web.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.xinxiucan.web.beans.ReturnBean;

/**
 * @author xinxiucan
 * @date 2017年7月1日
 */
@RestController
@RequestMapping("/api")
public class IndexController {

    @RequestMapping("/test")
	public ReturnBean TestApi(String param1, String param2) {
		try {
			ReturnBean bean = new ReturnBean();
			bean.put("param1", param1);
			bean.put("param2", param2);
			return bean;
		} catch (Exception e) {
			return new ReturnBean(500, e.getMessage());
		}

	}

}


```

![](http://osewdhh4j.bkt.clouddn.com/Q20170701205510.png)



