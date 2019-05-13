---
title: 在Centos7 上搭建rabbitmq
date: 2019-04-17 13:18:53
tags: rabbitmq
---
### 安装erlang
```bash
## step1 通过yum安装 erlang
yum install erlang
## step2 检查erlang是否安装成功
erl -version
## teminal 输出如下内容说明安装成功
Erlang (SMP,ASYNC_THREADS,HIPE) (BEAM) emulator version 5.10.4
```
<!-- more -->
### 安装rabbit mq
RabbitMQ 最新版本地址：https://www.rabbitmq.com/releases/rabbitmq-server/current/

#### step1. 下载安装包

``` bash
## 切换到安装包存放目录
cd /tmp
## step1 下载安装包
wget https://www.rabbitmq.com/releases/rabbitmq-server/current/rabbitmq-server-3.6.15-1.el6.noarch.rpm

```

#### step2 通过rpm安装
```bash
yum install rabbitmq-server-3.6.15-1.el6.noarch.rpm 
```
#### step3 启动RabitMQ 服务
```bash
systemctl start rabbitmq-server.service
```
#### step4 查看RabbitMQ 服务状态
```bash
systemctl status  rabbitmq-server.service
```
##### 输出如下信息说明启动成功
```bash
● rabbitmq-server.service - LSB: Enable AMQP service provided by RabbitMQ broker
   Loaded: loaded (/etc/rc.d/init.d/rabbitmq-server; bad; vendor preset: disabled)
   Active: active (running) since Mon 2019-05-06 20:44:37 CST; 19s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 5455 ExecStart=/etc/rc.d/init.d/rabbitmq-server start (code=exited, status=0/SUCCESS)
   Memory: 67.7M
   CGroup: /system.slice/rabbitmq-server.service
           ├─5653 /bin/sh /etc/rc.d/init.d/rabbitmq-server start
           ├─5655 /bin/bash -c ulimit -S -c 0 >/dev/null 2>&1 ; /usr/sbin/rabbitmq-server
           ├─5658 /bin/sh /usr/sbin/rabbitmq-server
           ├─5677 su rabbitmq -s /bin/sh -c /usr/lib/rabbitmq/bin/rabbitmq-server
           ├─5679 /bin/sh /usr/lib/rabbitmq/bin/rabbitmq-server
           ├─5868 /usr/lib64/erlang/erts-5.10.4/bin/beam.smp -W w -A 64 -P 1048576 -t 5000000 -stbt db -zdbbl 128000 -K true -B i -- -root /usr/lib64/erlang -progname erl -- -home /var/lib/rabbitmq -- -pa /usr/lib/rabbitmq/lib/rabbitmq...
           ├─6011 inet_gethost 4
           └─6012 inet_gethost 4
```

#### step5 查看已经安装的插件

```bash
## 切换到命令所在的目录
cd /usr/sbin
## 查看插件
./rabbitmq-plugins lis
```

##### 得到如下输出信息
```bash
Configured: E = explicitly enabled; e = implicitly enabled
 | Status:   * = running on rabbit@GYB-T-ISEE-STUDIO-01
 |/
[  ] amqp_client                       3.6.15
[  ] cowboy                            1.0.4
[  ] cowlib                            1.0.2
[  ] rabbitmq_amqp1_0                  3.6.15
[  ] rabbitmq_auth_backend_ldap        3.6.15
[  ] rabbitmq_auth_mechanism_ssl       3.6.15
[  ] rabbitmq_consistent_hash_exchange 3.6.15
[  ] rabbitmq_event_exchange           3.6.15
[  ] rabbitmq_federation               3.6.15
[  ] rabbitmq_federation_management    3.6.15
[  ] rabbitmq_jms_topic_exchange       3.6.15
[  ] rabbitmq_management               3.6.15
[  ] rabbitmq_management_agent         3.6.15
[  ] rabbitmq_management_visualiser    3.6.15
[  ] rabbitmq_mqtt                     3.6.15
[  ] rabbitmq_random_exchange          3.6.15
[  ] rabbitmq_recent_history_exchange  3.6.15
[  ] rabbitmq_sharding                 3.6.15
[  ] rabbitmq_shovel                   3.6.15
[  ] rabbitmq_shovel_management        3.6.15
[  ] rabbitmq_stomp                    3.6.15
[  ] rabbitmq_top                      3.6.15
[  ] rabbitmq_tracing                  3.6.15
[  ] rabbitmq_trust_store              3.6.15
[  ] rabbitmq_web_dispatch             3.6.15
[  ] rabbitmq_web_mqtt                 3.6.15
[  ] rabbitmq_web_mqtt_examples        3.6.15
[  ] rabbitmq_web_stomp                3.6.15
[  ] rabbitmq_web_stomp_examples       3.6.15
[  ] sockjs                            0.3.4
```


#### step6 安装web管理插件
```bash
cd /usr/sbin
rabbitmq-plugins enable rabbitmq_management
```

- 输出如下信息说明启动成功
```bash
The following plugins have been enabled:
  amqp_client
  cowlib
  cowboy
  rabbitmq_web_dispatch
  rabbitmq_management_agent
  rabbitmq_management

Applying plugin configuration to rabbit@GYB-T-ISEE-STUDIO-01... started 6 plugins.
```

#### step7 登录WEB管理台
- 添加admin用户 并赋予管理员权限
```bash
[root@localhost ~]# rabbitmqctl add_user  admin  admin
Creating user "admin"
[root@localhost ~]# rabbitmqctl set_user_tags admin administrator
Setting tags for user "admin" to [administrator]
```

- 通过chrome问地址：http://localhost:15672/#/

![](http://images.huotaihe.com/2019-05-07-12-48-30.png)
![](http://images.huotaihe.com/2019-05-07-12-49-47.png)


---
### 参考资料
https://blog.csdn.net/cool_summer_moon/article/details/78803661
https://blog.battcn.com/2017/08/20/linux/linux-centos7-ribbitmq/