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
-----
## VIM
### vim 统计字符串出现次数（词频）
```bash
# <pattern> 是需要统计的字符串
:%s/<pattern>//gn
```
> % - 操作区间，全文本；可以使用1, $或行区间替代
    s - substitute
    <pattern> - 查询字符串
    // - 无替代文本。如果偏好，中间可以加任意字符！建议添加‘～’,重复上次查询
    g - Replace all occurrences in the line.  Without this argument,  replacement occurs only for the first occurrence in each line.因此，如果不用此标志，就变成统计出现pattern的行数。
    n - Report the number of matches, do not actually substitute.这是核心标志，也是达到目的的标志。同时也说明了为什么'//'之间可以添加任意字符！