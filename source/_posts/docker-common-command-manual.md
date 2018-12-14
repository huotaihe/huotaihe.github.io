---
title: Docker 常用命令手册
date: 2018-12-13 13:50:03
tags:
toc: true
---
### 删除所有Docker容器
```bash
docker rm `docker ps -a -q`
```
### 删除所有Docker镜像
```bash
docker rmi `docker images -q`
```
<!-- more -->

### 登陆到Docker容器
```bash
docker exec  -it <containerId> /bin/bash
```



