---
title: 记一次docker  容器把宿主机系统盘写满的事件
toc: true
date: 2019-05-23 18:51:39
categories: [生产问题解决]
tags:
  - Docker
  - Linux
---

 
## 环境信息
- Docker version: 1.13.1
- Linux version: CentOS Linux release 7.3.1611 (Core)
- 磁盘分配情况
    ```bash
    [root@HZD-U-ISEE-STUDIO-01 ~]# df -h
    Filesystem                                               Size  Used Avail Use% Mounted on
    /dev/vda1                                                 40G  6.9G   31G  19% /
    devtmpfs                                                 3.9G     0  3.9G   0% /dev
    tmpfs                                                    3.9G   12K  3.9G   1% /dev/shm
    tmpfs                                                    3.9G   35M  3.8G   1% /run
    tmpfs                                                    3.9G     0  3.9G   0% /sys/fs/cgroup
    /dev/vdb1                                                 99G  7.3G   87G   8% /alidata1
    tmpfs                                                    783M     0  783M   0% /run/user/0
    tmpfs                                                    783M     0  783M   0% /run/user/1000
    ```

## 问题描述
> 服务器在运行的过程中，发现不能创建新的docker容器
<!--- more --->
## 定位过程
1. 查看CPU和内存
    ```bash
    top
    ```
    ```bash
    free -m
    ```

> 发现CPU和内存使用率正常

2. 查看磁盘使用情况
> 1. 由于一些容器会向nas盘写视频文件，就考虑到有可能是磁盘满了
> 2. 检查后发现docker的虚拟盘把宿主机的系统盘写满了，导致宿主主机没有磁盘空间创建新的容器

![在这里插入图片描述](http://images.huotaihe.com/2019-05-23-18-19-31.png)

## 解决方案
- 把docker 容器文件的存储位置迁移到容量更大的磁盘("/alidata1") 下面

## 解决过程
1. 删除现有的docker容器
```bash
docker stop `docker -a -q`
docker rm `docker -a -q`
```

2. 停止docker 服务
```bash
systemctl stop docker
```
3. 创建docker 文件存储目录
```bash
mkdir -p /alidata1/docker/graph
```
4.  修改配置文件
```bash 
vim /etc/sysconfig/docker
```
将文件中的OPTIONS那一行修改如下
```bash
OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false --graph=/alidata1/docker/graph'
```
5. 重新加载配置
```bash
systemctl daemon-reload
```

6. 启动docker服务
```bash
systemctl start docker
```

7. 重新拉取镜像
```bash
docker pull <docker image id>
```
> ps : 把原来'/var/lib/docker/' 目录下的文件拷贝过来，页可以看到镜像，并创建容器；但是容器启动后就消亡了。具体原因待定位，建议重新拉取镜像。

8. 启动容器
``` bash
docker run <....>
```

9. 验证结果

```bash
[root@localhost ~]# df -h
Filesystem                                               Size  Used Avail Use% Mounted on
/dev/vda1                                                 40G  6.9G   31G  19% /
devtmpfs                                                 3.9G     0  3.9G   0% /dev
tmpfs                                                    3.9G   12K  3.9G   1% /dev/shm
tmpfs                                                    3.9G   35M  3.8G   1% /run
tmpfs                                                    3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/vdb1                                                 99G  7.3G   87G   8% /alidata1
tmpfs                                                    783M     0  783M   0% /run/user/0
tmpfs                                                    783M     0  783M   0% /run/user/1000
overlay                                                   99G  7.3G   87G   8% /alidata1/docker/graph/overlay2/e071ea3439ff52a12ee127875b8511f5d05fdea236386495f99f4ece33bdfeb2/merged
shm                                                       64M     0   64M   0% /alidata1/docker/graph/containers/841a2f9199f5da6159a15156762d5d488eee21c46237b82c192018d1264d6328/shm
overlay                                                   99G  7.3G   87G   8% /alidata1/docker/graph/overlay2/850f00f1665f2c920e1d00aa836517e08e70df48d2fee0c3e4172dd2f95dd7f7/merged
overlay                                                   99G  7.3G   87G   8% /alidata1/docker/graph/overlay2/12e6e98dd0611e21d70378f32810f97fe86d1c30a9c9c562faf3bfb826d01244/merged
[root@localhost ~]#
```

通过查看磁盘情况，发现新的容器文件已经写到数据盘下了

## 总结
> docker 在生产环境运行的时候，不要把docker文件写在默认的系统盘"/var/lib/docker" 路径下