---
title: 记一次Linux磁盘分区写入失败的事件
toc: true
categories:
  - temp
tags:
  - temp
date: 2019-12-27 10:42:25
updated: 2019-12-27 10:42:25
---



## 环境信息
- Linux version: CentOS Linux release 7.3.1611 (Core)
- 磁盘分配情况
```bash
[root@GYB-T-SAS-06 ~]# df -h
Filesystem                                                                                         Size  Used Avail Use% Mounted on
/dev/vda1                                                                                           40G   15G   23G  40% /
devtmpfs                                                                                           3.9G     0  3.9G   0% /dev
tmpfs                                                                                              3.9G   12K  3.9G   1% /dev/shm
tmpfs                                                                                              3.9G  197M  3.7G   6% /run
tmpfs                                                                                              3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/vdb1                                                                                           99G   55G   39G  59% /alidata1
tmpfs                                                                                              783M     0  783M   0% /run/user/0
```
<!-- more -->

## 问题描述

> 程序在运行的过程中发现程序写入文件一直失败，报错日志如下
```bash
java.nio.file.FileSystemException: /alidata1/resource/bHZ2bWQ6I: No space left on device
        at sun.nio.fs.UnixException.translateToIOException(UnixException.java:91) ~[na:1.8.0_191]
        at sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:102) ~[na:1.8.0_191]
        at sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:107) ~[na:1.8.0_191]
        at sun.nio.fs.UnixFileSystemProvider.createDirectory(UnixFileSystemProvider.java:384) ~[na:1.8.0_191]
        at java.nio.file.Files.createDirectory(Files.java:674) ~[na:1.8.0_191]
        at java.nio.file.Files.createAndCheckIsDirectory(Files.java:781) ~[na:1.8.0_191]
        at java.nio.file.Files.createDirectories(Files.java:767) ~[na:1.8.0_191]
```

## 定位过程
1. 以为是磁盘空间满了
查看磁盘分区存储情况, 发现磁盘分区空间还很充足
```bash
[root@localhost tmp]# df -h
Filesystem                                                                                         Size  Used Avail Use% Mounted on
/dev/vda1                                                                                           40G   16G   22G  42% /
devtmpfs                                                                                           3.9G     0  3.9G   0% /dev
tmpfs                                                                                              3.9G   12K  3.9G   1% /dev/shm
tmpfs                                                                                              3.9G  197M  3.7G   6% /run
tmpfs                                                                                              3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/vdb1                                                                                           99G   55G   39G  59% /alidata1
```

2. 分析了程序特点，程序在向/alidata卷下写大量的小文件，分析可能是inode满了
查看inode，发现Inode已经被写满
```bash
[root@GYB-T-SAS-06 tmp]# df -ih
Filesystem                                                                                        Inodes IUsed IFree IUse% Mounted on
/dev/vda1                                                                                           2.5M   68K  2.5M    3% /
devtmpfs                                                                                            976K   344  975K    1% /dev
tmpfs                                                                                               978K     4  978K    1% /dev/shm
tmpfs                                                                                               978K   50K  929K    6% /run
tmpfs                                                                                               978K    16  978K    1% /sys/fs/cgroup
/dev/vdb1                                                                                           6.3M  6.3M  0K   100% /alidata1
tmpfs                                                                                               978K     1  978K    1% /run/user/0
```

## 解决方案
- 把大量的小文件mv到其它卷
- 换一个卷继续写入
- 调大inode 上限

## 总结
- 磁盘监控一定要做好包括“磁盘容量”和inode容量
- 存储大批量散碎文件不太适合用nas或者本地磁盘
- 建议使用阿里云oss存储可以屏蔽很多复杂度和问题

## 知识补充
### 什么是inode

> 一、inode是什么？
理解inode，要从文件储存说起。
文件储存在硬盘上，硬盘的最小存储单位叫做"扇区"（Sector）。每个扇区储存512字节（相当于0.5KB）。
操作系统读取硬盘的时候，不会一个个扇区地读取，这样效率太低，而是一次性连续读取多个扇区，即一次性读取一个"块"（block）。这种由多个扇区组成的"块"，是文件存取的最小单位。"块"的大小，最常见的是4KB，即连续八个 sector组成一个 block。
文件数据都储存在"块"中，那么很显然，我们还必须找到一个地方储存文件的元信息，比如文件的创建者、文件的创建日期、文件的大小等等。这种储存文件元信息的区域就叫做inode，中文译名为"索引节点"。每一个文件都有对应的inode，里面包含了与该文件有关的一些信息。

二、inode的内容
inode包含文件的元信息，具体来说有以下内容：
   * 文件的字节数
　　* 文件拥有者的User ID
　　* 文件的Group ID
　　* 文件的读、写、执行权限
　　* 文件的时间戳，共有三个：ctime指inode上一次变动的时间，mtime指文件内容上一次变动的时间，atime指文件上一次打开的时间。
　　* 链接数，即有多少文件名指向这个inode
　　* 文件数据block的位置
总之，除了文件名以外的所有文件信息，都存在inode之中。至于为什么没有文件名，下文会有详细解释。
可以用stat命令，查看某个文件的inode信息：
### NAS 和 OSS 优缺点对比
 待补充
# 参考资料
- https://blog.csdn.net/lidan3959/article/details/16981137
- https://www.cnblogs.com/jiangxiaoxian/p/9610903.html