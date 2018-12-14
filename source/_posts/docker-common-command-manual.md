---
title: Docker 常用命令手册
date: 2018-12-13 13:50:03
tags:
---
### 1、删除所有Docker容器
```bash
docker rm `docker ps -a -q`
```
### 2、删除所有Docker镜像
```bash
docker rmi `docker images -q`
```
<!-- more -->

### 3、登陆到Docker容器
```bash
docker exec  -it <containerId> /bin/bash
```



