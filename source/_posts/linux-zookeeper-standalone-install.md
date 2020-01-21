---
title: Zookeeper 单点环境搭建
toc: true
categories:
  - 服务器环境搭建
tags:
  - Zookeeper
  - 环境搭建
  - Linux
date: 2019-08-15 10:46:59
updated: 2019-08-15 10:46:59
---

## 环境信息
- java: "1.8.0_191"
- Centos: 7.3.1611 (Core)
- Zookeeper: 3.5.5
<!-- more -->

## 下载
下载地址： http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.5.5/apache-zookeeper-3.5.5-bin.tar.gz

## 安装
```bash
## step1 解压安装包
tar -xzvf apache-zookeeper-3.5.5-bin.tar.gz

## step2 将解压后的安装包移动到/usr/local目录下
mv apache-zookeeper-3.5.5-bin /usr/local/zookeeper

## step3 创建数据目录
mkdir -p /var/lib/zookeeper

## step4 编辑配置文件
cat > /usr/local/zookeeper/conf/zoo.cfg << EOF
> tickTime=2000
> dataDir=/var/lib/zookeeper
> clientPort=2181
EOF

## step5 设置java环境变量，如果已经设置可以忽略
export JAVA_HOME=/opt/jdk1.8.0_191

##  step6 启动zookeeper
/usr/local/zookeeper/bin/zkServer.sh start
```
控制台输出如下内容说明启动成功
```bash
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```

## 验证
### 方法一 通过telnet 输入srvr
```bash
telnet 127.0.0.1 2181
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
srvr
Zookeeper version: 3.5.5-390fe37ea45dee01bf87dc1c042b5e3dcce88653, built on 05/03/2019 12:07 GMT
Latency min/avg/max: 0/0/0
Received: 1
Sent: 0
Connections: 1
Outstanding: 0
Zxid: 0x0
Mode: standalone
Node count: 5
Connection closed by foreign host.
```

### 方法二 通过zkServer 工具
```bash
/usr/local/zookeeper/bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost.
Mode: standalone
```
## 总结
### 问题1：
安装过程中启动的时候遇到了如下异常
```log
Could not find or load main class  org.apache.zookeeper.server.quorum.QuorumPeerMain
```
----
### 解决方法
通过查资料发现是下载的安装包不对(参考文档二)

apache 提供了两个Zookeeper的下载包

apache-zookeeper-3.5.5-bin.tar.gz 20-May-2019 18:40   10M  
apache-zookeeper-3.5.5.tar.gz     20-May-2019 18:40  3.1M  

下载apache-zookeeper-3.5.5.tar.gz 包安装后悔有问题。
重新下载apache-zookeeper-3.5.5-bin.tar.gz 并安装后问题解决。

具体原因待跟进

## 参考文档
- https://zookeeper.apache.org/doc/current/zookeeperStarted.html
- https://stackoverflow.com/questions/28484398/starting-zookeeper-cluster-error-could-not-find-or-load-main-class-org-apache