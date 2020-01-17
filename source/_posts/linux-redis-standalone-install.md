---
title: Centos7 安装redis
toc: true
categories:
  - Redis 
tags:
  - Redis
  - 环境搭建
date: 2019-11-27 18:39:24
updated: 2019-11-27 18:39:24
---


## REDIS
> Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。 它支持多种类型的数据结构，如 字符串（strings）， 散列（hashes）， 列表（lists）， 集合（sets）， 有序集合（sorted sets） 与范围查询， bitmaps， hyperloglogs 和 地理空间（geospatial） 索引半径查询。 Redis 内置了 复制（replication），LUA脚本（Lua scripting）， LRU驱动事件（LRU eviction），事务（transactions） 和不同级别的 磁盘持久化（persistence）， 并通过 Redis哨兵（Sentinel）和自动 分区（Cluster）提供高可用性（high availability）

<!-- more -->

>Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker. It supports data structures such as strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs, geospatial indexes with radius queries and streams. Redis has built-in replication, Lua scripting, LRU eviction, transactions and different levels of on-disk persistence, and provides high availability via Redis Sentinel and automatic partitioning with Redis Cluster.

## 下载解压
```bash
## 切换目录
cd /usr/local/src
## 下载文件
wget http://download.redis.io/releases/redis-5.0.7.tar.gz

## 解压
tar -xzvf redis-5.0.7.tar.gz

## 将文件移动到安装目录
mv redis-5.0.7 /usr/local/redis
```

## 编译安装

```bash
## 切换目录
cd /usr/local/redis

## 编译安装
make && make install
```

## 查看安装结果
```bash
cd /usr/local/bin/
ll
```

ll结果输出如下说明安装成功
```bash
-rwxr-xr-x 1 root root 4366848 Nov 27 19:14 redis-benchmark
-rwxr-xr-x 1 root root 8125240 Nov 27 19:14 redis-check-aof
-rwxr-xr-x 1 root root 8125240 Nov 27 19:14 redis-check-rdb
-rwxr-xr-x 1 root root 4807920 Nov 27 19:14 redis-cli
lrwxrwxrwx 1 root root      12 Nov 27 19:14 redis-sentinel -> redis-server
-rwxr-xr-x 1 root root 8125240 Nov 27 19:14 redis-server
```

## 启动
```bash
## 通过默认配置启动
/usr/local/bin/redis-server
## 查看是否运行
[root@baimaduhe bin]# ps -ef |grep redis
root      6058  6007  0 22:05 pts/1    00:00:00 ./redis-server *:6379
root      6063  5899  0 22:05 pts/0    00:00:00 grep --color=auto redis
```

## 连接测试
```bash
[root@baimaduhe bin]# /usr/local/bin/redis-cli
127.0.0.1:6379> set key1 hello
OK
127.0.0.1:6379> get key1
"hello"
127.0.0.1:6379>
```