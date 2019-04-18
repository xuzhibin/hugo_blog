---
title: iptables封ip段的一些常见命令
date: 2011-05-18
categories:
- linux
tags:
- iptables
---
linux下使用iptables封ip段的一些常见命令：

**封单个IP的命令**

``` bash
iptables -I INPUT -s 211.1.0.0 -j DROP
```


**封IP段的命令**

``` bash
iptables -I INPUT -s 211.1.0.0/16 -j DROP
iptables -I INPUT -s 211.2.0.0/16 -j DROP
iptables -I INPUT -s 211.3.0.0/16 -j DROP
```


**封整个段的命令**

``` bash
iptables -I INPUT -s 211.0.0.0/8 -j DROP
```


**封几个段的命令**

``` bash
iptables -I INPUT -s 61.37.80.0/24 -j DROP
iptables -I INPUT -s 61.37.81.0/24 -j DROP
```


**封80端口**

``` bash
iptables -I INPUT -p tcp –dport 80 -s 124.115.0.0/24 -j DROP
```


**解封的话**

``` bash
iptables -D INPUT -s IP地址 -j REJECT
iptables -F 全清掉了
```


如果想开机就自动封锁某个IP，那就编辑/etc/sysconfig/iptables文件，添加一行

``` bash
-I INPUT -s IP地址 -j DROP
```

然后执行/etc/init.d/iptables restart重启iptables。