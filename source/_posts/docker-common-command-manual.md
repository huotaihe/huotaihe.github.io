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

### 启动容器时通过启动命令绑定HOST

```bash
docker run --rm -ti --add-host=<host1>:<ip1> --add-host=<host2>:<ip2> <imageId|imageName>
```
例子

```bash
docker run -ti --add-host=www.baidu.com:127.0.0.1 --add-host=www.google.com:127.0.0.1 centos
```

登录容器验证
```bash
root@13c025719aaa /]# ping www.baidu.com
PING www.baidu.com (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.048 ms
64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.044 ms
64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.038 ms
64 bytes from localhost (127.0.0.1): icmp_seq=4 ttl=64 time=0.046 ms
^C
--- www.baidu.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 2999ms
rtt min/avg/max/mdev = 0.038/0.044/0.048/0.003 ms
[root@13c025719aaa /]# ping www.google.com
PING www.google.com (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.045 ms
64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.042 ms
64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.051 ms
64 bytes from localhost (127.0.0.1): icmp_seq=4 ttl=64 time=0.043 ms
64 bytes from localhost (127.0.0.1): icmp_seq=5 ttl=64 time=0.046 ms
^C
--- www.google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 3999ms
rtt min/avg/max/mdev = 0.042/0.045/0.051/0.006 ms
[root@13c025719aaa /]#
```