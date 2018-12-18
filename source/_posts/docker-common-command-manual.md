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
### 通过 Dockerfile 打包镜像

```bash
# 切换到Dockerfile 文件目录
cd <docker file path>
## 指定镜像名称<iamge name> 镜像路径<image page>
docker build -t <image name> <image_path>
```

### 重新命名镜像

```bash
docker tag <imageId> <image name>
```
例子

```bash
docker tag ca1b6b825289 huotaihe/selenium:latest
```
