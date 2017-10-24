---
title: zabbix java api
date: 2017-08-21 10:08:47
tags: 监控
categories: java
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170821101322.png
keywords: zabbix,zabbix java api
description: Zabbix是一个高度集成的网络监控解决方案，在单个软件包中提供了多种功能。
---

## zabbix java api

zabbix官方的api文档地址：https://www.zabbix.com/documentation/3.0/manual/api
### Zabbix功能

#### 概观

 - Zabbix是一个高度集成的网络监控解决方案，在单个软件包中提供了多种功能。

#### 数据采集

 - 可用性和性能检查
 - 支持SNMP（捕获和轮询），IPMI，JMX，VMware监控
 - 定制检查
 - 以定制的间隔收集所需的数据
 - 由服务器/代理和代理执行

#### 灵活的阈值定义

 - 您可以定义非常灵活的问题阈值，称为触发器，从后端数据库引用值

#### 高度可配置的警报

 - 可以为升级计划，收件人，媒体类型定制发送通知
 - 使用宏变量可以使通知变得有意义和有用
 - 自动操作包括远程命令

#### 实时绘图

 - 使用内置的图形功能立即绘制被监视的项目

#### Web监控功能

 - Zabbix可以按照网站上模拟鼠标点击的路径，并检查功能和响应时间
#### 广泛的可视化选项

 - 能够创建可以将多个项目组合成单个视图的自定义图形
 - 网络地图
 - 自定义屏幕和幻灯片，以显示仪表板风格的概述
 - 报告
 - 监控资源的高级（业务）视图
 

#### 历史数据存储

 - 存储在数据库中的数据
 - 可配置历史
 - 内置内务程序

#### 轻松配置

 - 将监控的设备添加为主机
 - 主机被拾取用于监视，一次在数据库中
 - 将模板应用于受监控设备

#### 使用模板

 - 在模板中分组检查
 - 模板可以继承其他模板

#### 网络发现

 - 自动发现网络设备
 - 代理商自动注册
 - 发现文件系统，网络接口和SNMP OID

#### 快速的Web界面

 - PHP中的基于Web的前端
 - 可从任何地方访问
 - 你可以点击你的方式
 - 审核日志

#### Zabbix API

 - Zabbix API为Zabbix 提供了可编程接口，用于大规模操作，第三方软件集成和其他目的。

#### 权限系统

 - 安全的用户认证
 - 某些用户可以限于某些视图

#### 全功能和易于扩展的代理

 - 部署在监测目标上
 - 可以部署在Linux和Windows上

#### 二进制程序

 - 写在C中，用于性能和小内存占用
 - 容易携带

#### 准备复杂的环境

 - 通过使用Zabbix代理，远程监控变得容易

### zabbix最近问题列表
![](http://osewdhh4j.bkt.clouddn.com/20170815155701.png)

**pom.xml**
```
        <dependency>
			<groupId>io.github.hengyunabc</groupId>
			<artifactId>zabbix-api</artifactId>
			<version>0.0.1</version>
		</dependency>
```

**zabbix获取最近问题列表**

```
JSONObject jo = new JSONObject();
		jo.put("value", 1);
		jo.put("priority", new String[]{"2", "3", "4", "5"});
		Request request = RequestBuilder.newBuilder().method("trigger.get")
				.paramEntry("output", new String[]{"description", "priority", "lastchange"})
				.paramEntry("selectHosts", new String[]{"host", "name", "hostid"})
				.paramEntry("selectDependencies", "extend")
				.paramEntry("expandData", "host")
				.paramEntry("skipDependent", "1")
				.paramEntry("monitored", "1")
				.paramEntry("active", "1")
				.paramEntry("expandDescription", "1")
				.paramEntry("sortfield", "priority")
				.paramEntry("sortorder", "DESC")
				.paramEntry("filter", jo)
				.build();
```


**zabbix Api**

```
import io.github.hengyunabc.zabbix.api.DefaultZabbixApi;
import io.github.hengyunabc.zabbix.api.Request;
import io.github.hengyunabc.zabbix.api.RequestBuilder;
import io.github.hengyunabc.zabbix.api.ZabbixApi;

import org.apache.commons.lang.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONArray;
import com.alibaba.fastjson.JSONObject;

/**
 * zabbix Api
 * @author can
 */
public class ZabbixUtil {

	private static final Logger LOGGER = LoggerFactory.getLogger(ZabbixUtil.class);

	private ZabbixApi zabbixApi;

	public ZabbixUtil(String username, String password, String url) throws Exception {
		if (StringUtils.isBlank(username) || StringUtils.isBlank(password) || StringUtils.isBlank(url)){
			throw new Exception("ZabbixApi初始化失败！参数不全！");
		}
		login(username, password, url);
	}

	private ZabbixApi login(String username, String password, String url) throws Exception {
		zabbixApi = new DefaultZabbixApi(url);
		zabbixApi.init();
		boolean login = zabbixApi.login(username, password);
		if(!login){
			LOGGER.info(username + " login in Zabbix " + (login ? "SUCCESS" : "FALURE") + " !");
		}
		return zabbixApi;
	}

	/**
	 * 获取zabbix中所以的主机群组列表
	 * @return 主机群组列表json
	 */
	public String getHostGroupList() throws Exception {
		Request request = RequestBuilder.newBuilder().method("hostgroup.get")
				.paramEntry("output", "extend")
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}
	
	public String getHostList() throws Exception {
		Request request = RequestBuilder.newBuilder().method("host.get")
				.paramEntry("output", new String[]{"host", "name", "description", "hostid"})
				.paramEntry("selectGroups", "extend")
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}

	/**
	 * 获取主机ID
	 * @param hostIp
	 * @return 主机ID
	 */
	public String getHostByGroupid(Integer groupid) throws Exception {
		Request request = RequestBuilder.newBuilder().method("host.get")
				.paramEntry("groupids", groupid)
				.paramEntry("output", new String[]{"host", "name", "description", "hostid"})
				.paramEntry("selectGroups", "extend")
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}

	/**
	 * 获取zabbix报警列表
	 * @param timeFrom 仅返回在给定时间之后生成的警报。
	 * @return
	 */
	public String getAlertList(Long timeFrom) throws Exception {
		Request request = RequestBuilder.newBuilder().method("alert.get")
				.paramEntry("output", new String[]{"sendto", "subject", "clock", "message"})
				.paramEntry("selectHosts", new String[]{"host"})
				.paramEntry("time_from", timeFrom)
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}

	/**
	 * 获取zabbix报警列表
	 * @param timeFrom 仅返回在给定时间之后生成的警报。
	 * @return
	 */
	public String getAlertListByGroupids(Integer groupid, Long timeFrom) throws Exception {
		Request request = RequestBuilder.newBuilder().method("alert.get")
				.paramEntry("time_from", timeFrom)
				.paramEntry("groupids", groupid)
				.paramEntry("output", new String[]{"sendto", "subject", "clock", "message","triggerid"})
				.paramEntry("selectHosts", new String[]{"host"})
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}

	/**
	 * 获取zabbix最近问题列表
	 * @return
	 * @throws Exception
	 */
	public String getTriggerInfoList() throws Exception {
		JSONObject jo = new JSONObject();
		jo.put("value", 1);
		jo.put("priority", new String[]{"2", "3", "4", "5"});
		Request request = RequestBuilder.newBuilder().method("trigger.get")
				.paramEntry("output", new String[]{"description", "priority", "lastchange"})
				.paramEntry("selectHosts", new String[]{"host", "name", "hostid"})
				.paramEntry("selectDependencies", "extend")
				.paramEntry("expandData", "host")
				.paramEntry("skipDependent", "1")
				.paramEntry("monitored", "1")
				.paramEntry("active", "1")
				.paramEntry("expandDescription", "1")
				.paramEntry("sortfield", "priority")
				.paramEntry("sortorder", "DESC")
				.paramEntry("filter", jo)
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}
	
	/**
	 * 根据群组id和机器host获取触发器信息列表
	 * @param groupid
	 * @param host
	 * @return
	 * @throws Exception
	 */
	public String getTrigger(Integer groupid, String host) throws Exception {
		Request request = RequestBuilder.newBuilder().method("trigger.get")
				.paramEntry("groupids", groupid)
				.paramEntry("host", host)
				.paramEntry("monitored", 1)
				.paramEntry("output", new String[]{"expression","description", "priority", "lastchange","status"})
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}
	
	/**
	 * 根据触发器id获取触发器信息
	 * @param triggerId
	 * @return
	 * @throws Exception
	 */
	public String getTriggerByTriggerId(Integer triggerId) throws Exception {
		Request request = RequestBuilder.newBuilder().method("trigger.get")
				.paramEntry("triggerids", triggerId)
				.paramEntry("output", "extend")
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}

	public String getItemList() throws Exception {
		Request request = RequestBuilder.newBuilder().method("item.get").paramEntry("output", "extend").paramEntry("monitored", "true").build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}

	public String getTriggerPrototypeByGroupid(Integer groupid) throws Exception {
		Request request = RequestBuilder.newBuilder().method("triggerprototype.get")
				.paramEntry("groupids", groupid)
				.paramEntry("selectHosts", new String[]{"host", "hostid"})
				.paramEntry("selectGroups", "extend")
				.paramEntry("output", new String[]{"expression", "triggerid", "description", "priority", "status"})
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}
	
	public String getTriggerPrototypeByTriggerids(Integer triggerid) throws Exception {
		Request request = RequestBuilder.newBuilder().method("triggerprototype.get")
				.paramEntry("triggerids", triggerid)
				.paramEntry("output", "extend")
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}
	
	public String getTriggerInfo(Integer groupid,Long lastChangeSince) throws Exception {
		Request request = RequestBuilder.newBuilder().method("trigger.get")
				.paramEntry("groupids", groupid)
				.paramEntry("lastChangeSince", lastChangeSince)
				.paramEntry("output", new String[]{"description", "priority", "lastchange"})
				.paramEntry("selectHosts", new String[]{"host", "name", "hostid"})
				.paramEntry("skipDependent", "1")
				.paramEntry("monitored", "1")
				.paramEntry("active", "1")
				.paramEntry("expandDescription", "1")
				.paramEntry("sortfield", "priority")
				.build();
		JSONObject response = zabbixRequest(request);
		zabbixError(response);
		JSONArray result = response.getJSONArray("result");
		return result.toJSONString();
	}

	private JSONObject zabbixRequest(Request request) throws Exception {
		JSONObject response = zabbixApi.call(request);
		return response;
	}

	private void zabbixError(JSONObject response) throws Exception {
		if (!StringUtils.isBlank(response.getString("error")))
			throw new Exception("向Zabbix请求出错了！" + JSON.parseObject(response.getString("error")).getString("data"));
	}
```

### 运行结果

![](http://osewdhh4j.bkt.clouddn.com/20170815160901.png)
![](http://osewdhh4j.bkt.clouddn.com/20170815160902.png)
![](http://osewdhh4j.bkt.clouddn.com/20170815160903.png)



