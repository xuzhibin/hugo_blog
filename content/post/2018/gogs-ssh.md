---
title: gogs开启ssh支持
date: 2018-08-15
categories:
- server
tags:
- gogs
---
已经通过docker安装了gogs，http能正常使用，ssh不能使用
# gogs配置app.ini
``` ini
[server]
DOMAIN           = xxx
HTTP_PORT        = 3000
ROOT_URL         = http://xxx:13000/
DISABLE_SSH      = false
SSH_DOMAIN       = xxx:10022
SSH_PORT         = 22
START_SSH_SERVER = false
SSH_LISTEN_PORT  = 10033
SSH_ROOT_PATH    = /home/git/.ssh
OFFLINE_MODE     = false
REWRITE_AUTHORIZED_KEYS_AT_START = false
```
<!--more-->
# docker
ssh的配置文件路径
/app/gogs/docker/sshd_config
通过下面命令可以获得
``` bash
ps aux
```
# 设置目录权限
``` bash
chmod 0777 /data/git/.ssh
chmod 0600 /data/git/.ssh/authorized_keys
```
# 在后台/管理面板执行
重新生成 '.ssh/authorized_keys' 文件（警告：不是 Gogs 的密钥也会被删除）

# 重启ssh服务
``` bash
$ service ssh restart
$ exit
```
# 重新登录
``` bash
ssh -p 10022 git@xxx
```
# git clone格式
```
ssh://git@xxx:10022/jabin/myproject.git
```
# copy ssh-key
```bash
pbcopy < ~/.ssh/id_rsa.pub
```
