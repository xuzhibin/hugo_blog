---
title: CentOS安装crontab及使用方法
date: 2010-06-05
categories:
- server
tags:
- crontab
---
安装crontab:


``` bash
[root@CentOS ~] yum install vixie-cron
[root@CentOS ~] yum install crontabs
```

说明：
vixie-cron软件包是cron的主程序；

crontabs软件包是用来安装、卸装、或列举用来驱动 cron 守护进程的表格的程序。

cron 是linux的内置服务，但它不自动起来，可以用以下的方法启动、关闭这个服务：


``` bash
/sbin/service crond start #启动服务
/sbin/service crond stop #关闭服务
/sbin/service crond restart #重启服务
/sbin/service crond reload #重新载入配置
```

查看crontab服务状态：


``` bash
service crond status
```

手动启动crontab服务：


``` bash
service crond start
```

查看crontab服务是否已设置为开机启动，执行命令：


``` bash
ntsysv
```

加入开机自动启动:


``` bash
chkconfig --level 35 crond on
```

