---
title: Kafka broker 安装
toc: true
categories:
  - 服务器环境搭建
tags:
  - Kafka
  - 环境搭建
  - Linux
date: 2019-08-15 13:20:05
updated: 2019-08-15 13:20:05
---

## 环境信息
- java: "1.8.0_191"
- Centos: 7.3.1611 (Core)
- Zookeeper: 3.5.5
- Kafka: 2.3.0
<!-- more -->

## 下载
下载地址：http://mirror.bit.edu.cn/apache/kafka/2.3.0/kafka_2.12-2.3.0.tgz

## 安装
Kafaka 使用Zookeeper  保存集群的元数据信息和消费者信息。
Kafka也自带了Zookeeper，可以直接从脚本启动。
Zookeeper 的安装教程见文章：http://blog.huotaihe.com/2019/08/15/linux-zookeeper-standalone-install/

```bash
## 解压kafka_2.12-2.3.0.tgz
tar -xzvf kafka_2.12-2.3.0.tgz

## 将解压目录移动到/usr/local
mv kafka_2.12-2.3.0 /usr/local/kafka

## 进入kafka安装目录
cd /usr/local/kafka/

## 启动Kafaka
bin/kafka-server-start.sh config/server.properties
```
出现如下日志说明启动成功
```log
[2019-08-15 13:39:04,087] INFO Kafka version: 2.3.0 (org.apache.kafka.common.utils.AppInfoParser)
[2019-08-15 13:39:04,087] INFO Kafka commitId: fc1aaa116b661c8a (org.apache.kafka.common.utils.AppInfoParser)
[2019-08-15 13:39:04,087] INFO Kafka startTimeMs: 1565847544082 (org.apache.kafka.common.utils.AppInfoParser)
[2019-08-15 13:39:04,092] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)
```

## 验证

### 创建主题
```bash
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```
### 验证主题
```bash
bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic test
```
输出如下内容
```log
Topic:test	PartitionCount:1	ReplicationFactor:1	Configs:
	Topic: test	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
```
### 向主题上发布消息

```bash
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
>This is a message
>This is another message
```
输入完成后通过"^D" 结束输入

### 从主题上读取消息
```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

输出如下内容
```log
This is a message
This is another message
```

## 结语
以上就是kafka 单broker的搭建教程。
会有新的文章介绍如何搭建kafka集群。

---
## 参考资料
- http://kafka.apache.org/quickstart
- 《Kafka 权威指南》