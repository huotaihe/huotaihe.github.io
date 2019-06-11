---
title: Linux 下批量重命名文件
toc: true
categories:
  - temp
tags:
  - temp
date: 2019-06-05 19:21:31
updated: 2019-06-05 19:21:31
---

## 需求
  > 需要将目录下文件明"?" 及以后的内容去掉
## 文件格式
  ```bash
  [root@localhost]# ls |grep ?
  base.css?t=24
  bg-limited-count.png?hash=558ca0e
  bg-limited-normal.png?hash=3d4abb8
  bg-limited-time.png?hash=8deb2ab
  bitmap.png?hash=c6835a4
  ```
<!-- more -->
## 实现
## Linux 实现
```bash
## 先声明关键字
[root@localhost]# b="\\?"
## 用循环批量修改
for var in `ls |grep ?`; do mv -f $var ` echo ${var%$b*}`; done
```

### mac 
```bash
## 先声明关键字
b="?"
## 用循环批量修改
for var in `ls |grep "?"`; do mv -f $var `echo ${var%$b*}`; done
```

### 结果
```bash
 [root@localhost]# ls
  base.css
  bg-limited-count.png
  bg-limited-normal.png
  bg-limited-time.png
  bitmap.png
```

---
参考资料
- https://blog.csdn.net/wuyinggui10000/article/details/52779364
- https://www.cnblogs.com/pangblog/p/3243931.html