---
title: docker-install-middlewares
toc: true
categories:
  - temp
tags:
  - temp
date: 2019-10-17 07:21:38
updated: 2019-10-17 07:21:38
---
# Zookeeper
docker pull zookeeper:3.5
docker run --name zookeeper -p 2181:2181 -d zookeeper:3.5
docker logs zookeeper 
docker exec -it zookeeper bash
./zkCli.sh -server 127.0.0.1:2181
ls /


# Console
Console 是一个分布式的、高可用的、能够感知数据中心变化的基础设施

docker pull consul
docker run --name consul -d -p 8500:8500 -p 8600:8600/udp consul
dig @127.0.0.1 -p 8600 consul-service.service.consul

# Nacos
Nacos 是一个易于构建云原生应用的动态服务发现，配置管理和服务管理平台

获取镜像
docker pull nacos/nacos-server
运行镜像

docker run --name nacos -d -p 8848:8848 -e MODE=standalone nacos/nacos-server