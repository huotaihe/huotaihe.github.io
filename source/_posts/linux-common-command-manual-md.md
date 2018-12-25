---
title: Linux 常用命令手册
categories:
  - Linux 常用命令手册
tags:
  - Linux
  - Shell
  - Manual
toc: false
---
收集工作中常用到的Linux 命令；
只收集不易记忆的，常用的不在罗列都在脑子里。

## 目录管理
### 查看当前目录总共占的容量，而不单独列出各子项占用的容量 
```bash
du -sh
```
<!-- more -->

### 查看当前目录下一级子文件和子目录占用的磁盘容量
```bash
du -lh --max-depth=1
```