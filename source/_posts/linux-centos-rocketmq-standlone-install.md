---
title: 【服务器环境搭建】之 CentOS 安装 RocketMQ
toc: true
categories:
  - 服务器环境搭建
tags:
  - 环境搭建
  - RocketMq
  - Linux
date: 2020-01-20 18:45:27
updated: 2020-01-20 18:45:27
---

# RocketMQ
> Apache RocketMQ™是一个统一消息引擎、轻量级数据处理平台。
>> Apache RocketMQ™ is a unified messaging engine, lightweight data processing platform.

# 环境信息
-  Apache RocketMQ 4.6.0
- CentOS Linux release 7.7.1908 (Core)
- jdk-8u241-linux-x64
- Apache Maven 3.5.2
- git version 2.21.0 (Apple Git-122)
- 4g+ free disk for Broker server
<!-- more -->

# 安装
安装包下载地址：
https://www.apache.org/dyn/closer.cgi?path=rocketmq/4.6.0/rocketmq-all-4.6.0-bin-release.zip


## 下载安装包
```bash
[root@baimaduhe ~]# cd /usr/local/src/
[root@baimaduhe src]# wget http://mirror.bit.edu.cn/apache/rocketmq/4.6.0/rocketmq-all-4.6.0-bin-release.zip
```

## 解压安装包
```bash
## 解压
[root@baimaduhe src]# unzip rocketmq-all-4.6.0-bin-release.zipp
## 将安装文件拷贝到指定目录
[root@baimaduhe src]# mv rocketmq-all-4.6.0-bin-release /usr/local/rocketmq
```

## 启动RocketMq
```bash
## 启动Name Server
[root@baimaduhe src]# nohup sh /usr/local/rocketmq/bin/mqnamesrv &
## 查看Namer Server 日志
[root@baimaduhe src]# tail -f ~/logs/rocketmqlogs/namesrv.log
## 启动Broker
[root@baimaduhe src]# nohup sh /usr/local/rocketmq/bin/mqbroker -n localhost:9876 >/dev/null 2>&1 &
## 查看Broker启动日志
[root@baimaduhe bin]# tail -f ~/logs/rocketmqlogs/broker.log
```

## 发送 & 接收消息
```bash
## 设置Name Server 环境变量
[root@baimaduhe bin]# export NAMESRV_ADDR=localhost:9876
## 发送消息
[root@baimaduhe bin]# sh /usr/local/rocketmq/bin/tools.sh org.apache.rocketmq.example.quickstart.Producer

## 接收消息
[root@baimaduhe bin]# sh /usr/local/rocketmq/bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
```

## 停止服务
```bash
## 关闭broker
[root@baimaduhe bin]# sh /usr/local/rocketmq/bin/mqshutdown broker

## 关闭Name Server
[root@baimaduhe bin]# sh /usr/local/rocketmq/bin/mqshutdown namesrv
```

## FAQ
Broker 启动失败，报错信息如下
```bash
Java HotSpot(TM) 64-Bit Server VM warning: INFO: os::commit_memory(0x00000005c0000000, 8589934592, 0) failed; error='Cannot allocate memory' (errno=12)
#
# There is insufficient memory for the Java Runtime Environment to continue.
# Native memory allocation (mmap) failed to map 8589934592 bytes for committing reserved memory.
# An error report file with more information is saved as:
# /usr/local/rocketmq/bin/hs_err_pid15314.log
```

### 原因: 
内存不足，我的Linux是阿里云ECS2C4G的
```bash
[root@baimaduhe bin]# free -m
              total        used        free      shared  buff/cache   available
Mem:           3602        1615         218           0        1768        1736
Swap:             0           0           0
[root@baimaduhe bin]#
```

### 解决方案
修改启动vm内存配置
修改/usr/local/rocketmq/bin/ 下的服务启动脚本 runserver.sh 、runbroker.sh 中对于内存的限制，​改成如下示例：

```bash
JAVA_OPT="${JAVA_OPT} -server -Xms1g -Xmx1g -Xmn500m"
```
# 参考资料
- http://rocketmq.apache.org/docs/quick-start/
- https://blog.csdn.net/u013680938/article/details/53392644