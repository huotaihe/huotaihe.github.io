---
title: Linux下在一行执行多条命令
date: 2018-12-13 13:53:24
categories:
  - Linux 常用命令手册
tags:
  - Linux
  - Shell
  - Manual
toc: true
---
要实现在一行执行多条Linux命令，分三种情况：

### 1. &&

```bash
hexo clean && hexo g
```

> 第2条命令只有在第1条命令成功执行之后才执行。当&&前的命令“hexo clean”成功执行后"hexo g"才执行，根据命令产生的退出码判断是否执行成功（0成功，非0失败）。
<!-- more -->
### 2. ||
```bash
hexo clean || hexo g
```

> 只有||前的命令“hexo clean”执行不成功（产生了一个非0的退出码）时，才执行后面的命令。

### 2. ;
```bash
hexo clean ; hexo g
```
> 顺序执行多条命令，当;号前的命令执行完（不管是否执行成功），才执行;后的命令。

------ 
参考：https://www.cnblogs.com/koreaseal/archive/2012/05/28/2522178.html