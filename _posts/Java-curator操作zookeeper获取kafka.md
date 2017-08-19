---
title: Java curator操作zookeeper获取kafka
date: 2017-08-01 23:56:43
tags: kafka
categories: 大数据
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170802094224.png
keywords: Java curator操作zookeeper获取kafka,kafka,curator,zookeeper,curator操作zookeeper
description: Curator是Netflix公司开源的一个Zookeeper客户端，与Zookeeper提供的原生客户端相比，Curator的抽象层次更高，简化了Zookeeper客户端的开发量。
---

## Java curator操作zookeeper获取kafka
> Curator是Netflix公司开源的一个Zookeeper客户端，与Zookeeper提供的原生客户端相比，Curator的抽象层次更高，简化了Zookeeper客户端的开发量。

![](http://osewdhh4j.bkt.clouddn.com/20170802094224.png)

### Curator的Maven依赖

```
    <dependency>
		<groupId>org.apache.curator</groupId>
		<artifactId>curator-recipes</artifactId>
		<version>2.3.0</version>
	</dependency>
```

### 本地启动kafka

启动zookeeper

```
./bin/zookeeper-server-start.sh config/zookeeper.properties 
```

启动kafka

```
./bin/kafka-server-start.sh config/server.properties
```

启动kafkaManager

```
sudo ./bin/kafka-manager 
```

![](http://osewdhh4j.bkt.clouddn.com/20170802092222.png)

![](http://osewdhh4j.bkt.clouddn.com/20170802092243.png)

![](http://osewdhh4j.bkt.clouddn.com/20170802092347.png)

### 代码

项目结构
![](http://osewdhh4j.bkt.clouddn.com/20170802093255.png)

先看测试

```java
package com.xinxiucan.test;

import java.util.List;

import com.xinxiucan.api.BrokerApi;
import com.xinxiucan.api.TopicApi;
import com.xinxiucan.zookeeper.ZKBean;

public class AppTest {

	public static void main(String[] args) {
		ZKBean zk = new ZKBean();
		zk.setZkURL("127.0.0.1:2181");
		List<String> ids = BrokerApi.findBrokerIds(zk);
		for (String id : ids) {
			String idInfo = BrokerApi.findBrokerById(zk, id);
			System.out.println(idInfo);
		}

		List<String> topicNames = TopicApi.findAllTopicName(zk);
		for (String topicName : topicNames) {
			System.out.println("topicName:" + topicName);
		}
		
		List<String> topics = TopicApi.findAllTopics(zk);
		for (String topic : topics) {
			System.out.println("topic:" + topic);
		}

	}
}
```

**运行结果**

```
{"jmx_port":-1,"timestamp":"1501636761404","endpoints":["PLAINTEXT://can:9092"],"host":"can","version":3,"port":9092}
topicName:test_xin_1
topicName:xin
topic:{"version":1,"partitions":{"0":[0]}}
topic:{"version":1,"partitions":{"0":[0]}}
```

----------

### 剩余代码

**ZKBean**

```java
package com.xinxiucan.zookeeper;

import java.io.Serializable;

public class ZKBean implements Serializable {

	private static final long serialVersionUID = -6057956208558425192L;

	private int id = -1;

	private String name;

	private String zkURL;

	private String version = "0.8.2.2";

	private boolean jmxEnable;

	private String jmxAuthUsername;

	private String jmxAuthPassword;

	private boolean jmxWithSsl;

	private int zkConnectionTimeout = 30;

	private int maxRetryCount = 3;

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getZkURL() {
		return zkURL;
	}

	public void setZkURL(String zkURL) {
		this.zkURL = zkURL;
	}

	public boolean isJmxEnable() {
		return jmxEnable;
	}

	public void setJmxEnable(boolean jmxEnable) {
		this.jmxEnable = jmxEnable;
	}

	public String getJmxAuthUsername() {
		return jmxAuthUsername;
	}

	public void setJmxAuthUsername(String jmxAuthUsername) {
		this.jmxAuthUsername = jmxAuthUsername;
	}

	public String getJmxAuthPassword() {
		return jmxAuthPassword;
	}

	public void setJmxAuthPassword(String jmxAuthPassword) {
		this.jmxAuthPassword = jmxAuthPassword;
	}

	public boolean isJmxWithSsl() {
		return jmxWithSsl;
	}

	public void setJmxWithSsl(boolean jmxWithSsl) {
		this.jmxWithSsl = jmxWithSsl;
	}

	public int getZkConnectionTimeout() {
		return zkConnectionTimeout;
	}

	public void setZkConnectionTimeout(int zkConnectionTimeout) {
		this.zkConnectionTimeout = zkConnectionTimeout;
	}

	public int getMaxRetryCount() {
		return maxRetryCount;
	}

	public void setMaxRetryCount(int maxRetryCount) {
		this.maxRetryCount = maxRetryCount;
	}

	public String getVersion() {
		return version;
	}

	public void setVersion(String version) {
		this.version = version;
	}

	@Override
	public int hashCode() {
		return this.id;
	}

	@Override
	public boolean equals(Object obj) {
		if (obj == null) {
			return false;
		}
		if (this == obj) {
			return true;
		}
		if (obj instanceof ZKBean) {
			ZKBean anotherZKCluster = (ZKBean) obj;
			return this.id == anotherZKCluster.getId();
		} else {
			return false;
		}
	}

}
```

**ZKConnectionFactory**

```java
package com.xinxiucan.zookeeper;

import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.retry.ExponentialBackoffRetry;

public class ZKConnectionFactory {

	public static CuratorFramework buildCuratorFramework(ZKBean zk) {
		RetryPolicy retryPolicy = new ExponentialBackoffRetry(zk.getZkConnectionTimeout() * 1000, zk.getMaxRetryCount());
		CuratorFramework client = CuratorFrameworkFactory.newClient(zk.getZkURL(), retryPolicy);
		client.start();
		return client;
	}

}
```

**BrokerApi**

```java
package com.xinxiucan.api;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.apache.curator.framework.CuratorFramework;

import com.xinxiucan.KafkaZKPath;
import com.xinxiucan.zookeeper.ZKBean;
import com.xinxiucan.zookeeper.ZKConnectionFactory;

/**
 * 获取broker信息
 * @author xinxiucan
 */
public class BrokerApi {

	/**
	 * 根据zk获取Broker ID列表
	 * @param zkBean
	 * @return
	 */
	public static List<String> findBrokerIds(ZKBean zkBean) {
		CuratorFramework client = null;
		try {
			client = ZKConnectionFactory.buildCuratorFramework(zkBean);
			List<String> brokerIdStrs = client.getChildren().forPath(KafkaZKPath.BROKER_IDS_PATH);
			return brokerIdStrs;
		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (client != null) {
				client.close();
			}
		}
	}

	/**
	 * 根据zk&brokerId获取Broker信息
	 * @param zkBean
	 * @return
	 */
	public static String findBrokerById(ZKBean zkBean, String id) {
		CuratorFramework client = null;
		try {
			client = ZKConnectionFactory.buildCuratorFramework(zkBean);
			String brokerIdPath = KafkaZKPath.getBrokerSummeryPath(id);
			String brokerInfo = new String(client.getData().forPath(brokerIdPath), "UTF-8");
			return brokerInfo;
		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (client != null) {
				client.close();
			}
		}
	}

	public static Map<String, String> findAllBrokerSummarys(ZKBean zkBean) {
		CuratorFramework client = null;
		try {
			client = ZKConnectionFactory.buildCuratorFramework(zkBean);
			List<String> brokerIds = client.getChildren().forPath(KafkaZKPath.BROKER_IDS_PATH);
			if (brokerIds == null || brokerIds.size() == 0) {
				return null;
			}
			Map<String, String> brokerMap = new HashMap<String, String>();
			for (String brokerId : brokerIds) {
				String brokerIdPath = KafkaZKPath.getBrokerSummeryPath(brokerId);
				String brokerInfo = new String(client.getData().forPath(brokerIdPath), "UTF-8");
				brokerMap.put(brokerId, brokerInfo);
			}
			return brokerMap;
		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (client != null) {
				client.close();
			}
		}
	}

}
```

**TopicApi**

```java
package com.xinxiucan.api;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.apache.curator.framework.CuratorFramework;
import org.apache.zookeeper.data.Stat;

import com.xinxiucan.KafkaZKPath;
import com.xinxiucan.ReplicasAssignmentBuilder;
import com.xinxiucan.util.Object2Array;
import com.xinxiucan.zookeeper.ZKBean;
import com.xinxiucan.zookeeper.ZKConnectionFactory;


/**
 * 获取Topic信息
 * @author can
 */
public class TopicApi {

	/**
	 * 创建Topic
	 * @param zkBean
	 * @param topic
	 * @param partitionsCount
	 * @param replicationFactorCount
	 * @param topicConfig
	 */
	public static void createTopic(ZKBean zkBean, String topic, int partitionsCount, int replicationFactorCount,
			Map<String, String> topicConfig) {
		CuratorFramework client = null;
		try {
			client = ZKConnectionFactory.buildCuratorFramework(zkBean);
			List<String> brokerIds = client.getChildren().forPath(KafkaZKPath.BROKER_IDS_PATH);
			List<Integer> ids = new ArrayList<>();
			for (String str : brokerIds) {
				int i = Integer.parseInt(str);
				ids.add(i);
			}
			Map<String, List<Integer>> replicasAssignment = ReplicasAssignmentBuilder.assignReplicasToBrokers(ids, partitionsCount,
					replicationFactorCount);
			Map<String, Object> topicSummaryMap = new HashMap<String, Object>();
			topicSummaryMap.put("version", 1);
			topicSummaryMap.put("partitions", replicasAssignment);
			byte[] topicSummaryData = Object2Array.objectToByteArray(topicSummaryMap);
			String topicSummaryPath = KafkaZKPath.getTopicSummaryPath(topic);
			Stat stat = client.checkExists().forPath(topicSummaryPath);
			if (stat == null) {
				// create
				client.create().creatingParentsIfNeeded().forPath(topicSummaryPath, topicSummaryData);
			} else {
				// update
				client.setData().forPath(topicSummaryPath, topicSummaryData);
			}

			Map<String, Object> topicConfigMap = new HashMap<String, Object>();
			topicConfigMap.put("version", 1);
			topicConfigMap.put("config", topicConfig);
			byte[] topicConfigData = Object2Array.objectToByteArray(topicConfigMap);

			String topicConfigPath = KafkaZKPath.getTopicConfigPath(topic);
			Stat stat2 = client.checkExists().forPath(topicConfigPath);
			if (stat2 == null) {
				// create
				client.create().creatingParentsIfNeeded().forPath(topicConfigPath, topicConfigData);
			} else {
				// update
				client.setData().forPath(topicConfigPath, topicConfigData);
			}
		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (client != null) {
				client.close();
			}
		}
	}

	/**
	 * 删除Topic
	 * @param zkBean
	 * @param topicName
	 */
	public static void deleteTopic(ZKBean zkBean, String topicName) {
		CuratorFramework client = null;
		try {
			client = ZKConnectionFactory.buildCuratorFramework(zkBean);
			String adminDeleteTopicPath = KafkaZKPath.getDeleteTopicPath(topicName);
			client.create().creatingParentsIfNeeded().forPath(adminDeleteTopicPath, null);
		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (client != null) {
				client.close();
			}
		}
	}

	/**
	 * 查询topic
	 * @param zkBean
	 * @return
	 */
	public static List<String> findAllTopics(ZKBean zkBean) {
		CuratorFramework client = null;
		try {
			client = ZKConnectionFactory.buildCuratorFramework(zkBean);
			List<String> topicNames = client.getChildren().forPath(KafkaZKPath.BROKER_TOPICS_PATH);
			List<String> topicSummarys = new ArrayList<String>();
			for (String topicName : topicNames) {
				byte[] topicData = client.getData().forPath(KafkaZKPath.getTopicSummaryPath(topicName));
				topicSummarys.add(new String(topicData, "UTF-8"));
			}
			// add delete flag
			List<String> deleteTopics = client.getChildren().forPath(KafkaZKPath.ADMIN_DELETE_TOPICS_PATH);
			return topicSummarys;
		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (client != null) {
				client.close();
			}
		}
	}

	public static List<String> findAllTopicName(ZKBean zkBean) {
		CuratorFramework client = null;
		try {
			client = ZKConnectionFactory.buildCuratorFramework(zkBean);
			return client.getChildren().forPath(KafkaZKPath.BROKER_TOPICS_PATH);
		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (client != null) {
				client.close();
			}
		}
	}

	
}
```

**KafkaZKPath**

```java
package com.xinxiucan;

public class KafkaZKPath {

	public static final String COMSUMERS_PATH = "/consumers";
	public static final String BROKER_IDS_PATH = "/brokers/ids";
	public static final String BROKER_TOPICS_PATH = "/brokers/topics";
	public static final String CONFIG_TOPICS_PATH = "/config/topics";
	public static final String CONFIG_CHANGES_PATH = "/config/changes";
	public static final String CONTROLLER_PATH = "/controller";
	public static final String CONTROLLER_EPOCH_PATH = "/controller_epoch";
	public static final String ADMIN_PATH = "/admin";
	public static final String ADMIN_REASSGIN_PARTITIONS_PATH = "/admin/reassign_partitions";
	public static final String ADMIN_DELETE_TOPICS_PATH = "/admin/delete_topics";
	public static final String ADMIN_PREFERED_REPLICA_ELECTION_PATH = "/admin/preferred_replica_election";

	public static String getTopicSummaryPath(String topic) {
		return BROKER_TOPICS_PATH + "/" + topic;
	}

	public static String getTopicPartitionsStatePath(String topic,String partitionId) {
		return getTopicSummaryPath(topic) + "/partitions/" + partitionId+ "/state";
	}

	public static String getTopicConfigPath(String topic) {
		return CONFIG_TOPICS_PATH + "/" + topic;
	}

	public static String getDeleteTopicPath(String topic) {
		return ADMIN_DELETE_TOPICS_PATH + "/" + topic;
	}
	
	public static String getBrokerSummeryPath(String id) {
		return BROKER_IDS_PATH + "/" + id;
	}
	
	public static String getConsumerOffsetPath(String groupId) {
		return  COMSUMERS_PATH + "/" + groupId +"/offsets";
	}
	
	public static String getTopicConsumerOffsetPath(String groupId,String topic){
		return  COMSUMERS_PATH + "/" + groupId +"/offsets/"+topic;
	}
	
	public static String getTopicPartitionConsumerOffsetPath(String groupId, String topic,String partitionId){
		return  COMSUMERS_PATH + "/" + groupId +"/offsets/"+topic + "/" + partitionId;
	}
	
	public static String getTopicPartitionConsumerOwnerPath(String groupId, String topic, String partitionId) {
		return  COMSUMERS_PATH + "/" + groupId +"/owners/"+topic + "/" + partitionId;
	}
	
	
}
```

**ReplicasAssignmentBuilder**

```java
package com.xinxiucan;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Random;

public class ReplicasAssignmentBuilder {

	public static Map<String, List<Integer>> assignReplicasToBrokers(List<Integer> brokerListSet, int nPartitions, int nReplicationFactor) {
		return assignReplicasToBrokers(brokerListSet, nPartitions, nReplicationFactor, -1, -1);
	}

	/**
	 * There are 2 goals of replica assignment:
	 * 1. Spread the replicas evenly among brokers.
	 * 2. For partitions assigned to a particular broker, their other replicas are spread over the other brokers.
	 *
	 * To achieve this goal, we:
	 * 1. Assign the first replica of each partition by round-robin, starting from a random position in the broker list.
	 * 2. Assign the remaining replicas of each partition with an increasing shift.
	 *
	 * Here is an example of assigning
	 * broker-0  broker-1  broker-2  broker-3  broker-4
	 * p0        p1        p2        p3        p4       (1st replica)
	 * p5        p6        p7        p8        p9       (1st replica)
	 * p4        p0        p1        p2        p3       (2nd replica)
	 * p8        p9        p5        p6        p7       (2nd replica)
	 * p3        p4        p0        p1        p2       (3nd replica)
	 * p7        p8        p9        p5        p6       (3nd replica)
	 */
	public static Map<String, List<Integer>> assignReplicasToBrokers(List<Integer> brokerListSet, int nPartitions, int nReplicationFactor,
			int fixedStartIndex, int startPartitionId) {
		// sort
		brokerListSet.sort(new Comparator<Integer>() {

			@Override
			public int compare(Integer o1, Integer o2) {
				return o1 - o2;
			}

		});
		if (nPartitions <= 0) {
			throw new RuntimeException("num of partition cannot less than 1");
		}
		if (nReplicationFactor <= 0) {
			throw new RuntimeException("num of replication factor cannot less than 1");
		}
		if (nReplicationFactor > brokerListSet.size()) {
			throw new RuntimeException("broker num is less than replication factor num");
		}

		Random random = new Random();

		Map<String, List<Integer>> result = new HashMap<String, List<Integer>>();
		int startIndex = fixedStartIndex >= 0 ? fixedStartIndex : random.nextInt(brokerListSet.size());
		int currentPartitionId = startPartitionId >= 0 ? startPartitionId : 0;
		int nextReplicaShift = fixedStartIndex >= 0 ? fixedStartIndex : random.nextInt(brokerListSet.size());

		for (int i = 0; i < nPartitions; i++) {
			if (currentPartitionId > 0 && ((currentPartitionId % brokerListSet.size()) == 0)) {
				nextReplicaShift++;
			}
			int firstReplicaIndex = (currentPartitionId + startIndex) % brokerListSet.size();
			List<Integer> replicaList = new ArrayList<Integer>();
			replicaList.add(brokerListSet.get(firstReplicaIndex));
			for (int j = 0; j < (nReplicationFactor - 1); j++) {
				replicaList.add(brokerListSet.get(getReplicaIndex(firstReplicaIndex, nextReplicaShift, j, brokerListSet.size())));
			}
			result.put("" + currentPartitionId, getReverseList(replicaList));
			currentPartitionId++;
		}

		return result;
	}

	private static List<Integer> getReverseList(List<Integer> list) {
		List<Integer> result = new ArrayList<Integer>();
		for (int i = list.size() - 1; i >= 0; i--) {
			result.add(list.get(i));
		}
		return result;
	}

	private static int getReplicaIndex(int firstReplicaIndex, int nextReplicaShift, int replicaIndex, int nBrokers) {

		int shift = 1 + (nextReplicaShift + replicaIndex) % (nBrokers - 1);
		int result = (firstReplicaIndex + shift) % nBrokers;
		// System.out.println(firstReplicaIndex+"|"+nextReplicaShift+"|"+replicaIndex+"="+result);
		return result;
	}

	private static void printAssignReplicas(Map<String, List<Integer>> map, List<Integer> brokerListSet) {
		Map<Integer, List<String>> brokerMap = new HashMap<Integer, List<String>>();
		for (int i = 0; i < brokerListSet.size(); i++) {
			List<String> partitions = new ArrayList<String>();
			for (int j = 0; j < map.size(); j++) {
				List<Integer> brokerIds = map.get(j + "");
				if (brokerIds.contains(i)) {
					partitions.add("" + j);
				}
			}
			brokerMap.put(i, partitions);
		}
	}

}
```

