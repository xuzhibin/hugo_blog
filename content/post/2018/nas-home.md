---
title: 家庭数据中心
date: 2018-04-22
categories:
- tools
tags:
- 群晖
---

私人数据越来越多（如照片），网盘越来越小，并且不是关闭就是被墙。决定搭建自己的家庭数据中心。
![WechatIMG156](http://blog-img.xuzhibin.com/2018-11-28-080111.jpg)


# 计划&预算
- 硬件：群晖218+（加一条8g内存），希捷酷狼4T*2，玩客云，希捷移动硬盘2T（玩客云用），4T（群晖用）各一个，小米盒子国际版，斐讯k2p路由器（刷官改rom）
- 预算：8k
- 目的：数据备份，家庭影视
其实黑群也可以，价格更便宜。最好4盘位，2盘位还是少了点。
<!--more-->

# 家庭网络改造
通过 搬瓦工 安装ss，并在k2p设置ss服务和frp服务
- 可以无缝访问google
- 小米盒子（原生android tv）可以访问youtube
- 家庭网络设备可以通过frp对外访问
 
# 群晖外网访问
全球访问，frp速度更快，并且黑群也能用
- quickconnect
- frp

# 数据备份（多种备份方式，稳如磐石）
## Drive
多平台同步数据

## DS Photo、Moments
多平台同步照片

## Cloud Sync
![WX20180422-170201@2x](http://blog-img.xuzhibin.com/2018-11-28-080112.png)

- 同步下载百度网盘资源
- 同步下载Dropbox资源
- 将照片同步上传一份至onedrive和腾讯云cos（免费额度50G），即使本地硬盘挂了，也有网络备份

## Hyper Backup
- 定期将Drive数据和photo数据备份至移动硬盘。

## 快照
- 重要文件定期快照，随时恢复

# 家庭影视
- 玩客云负责下载电影（24小时下载电影）
- 手动通过smb将玩客云下载的电影移动到群晖的移动硬盘（群晖空间不大，只有4T，影视资源存至移动硬盘）
- 手机安装ds vedio、vlc；电视安装kodi、vlc；
- kodi通过局域网共享方式，可以将群晖的影视目录，照片目录，音乐目录，玩客云的影视目录加载进来，以后开机就可以直接看；
- 电视可以用VLC播放局域网视频资源
- 手机可以通过ds vedio直接看（外网也能访问），局域网可以vlc

# 家庭照片
- 手机可以用ds photo，Monents同步上传照片，也可以全球查看。
- 电视可以通过kodi查看

# web station
部署web服务，默认安装的php缺少很多模块，基本上当作静态站点使用（静态blog可以使用hexo，也可以使用MWeb生成）

# Docker 服务扩展
![WX20180422-170122@2x](http://blog-img.xuzhibin.com/2018-11-28-080113.png)

## 迅雷远程下载
可以通过迅雷远程下载文件。

## lnmp
可以部署运行php程序。

## gogs
部署git服务，sqlite数据库，部署方便，占资源少。

# Virtual Machine Manager
安装虚拟机，装了一个windows 2016，群晖需要8G内存以上才能安装。

# Antivirus Essential
之前中过毒，安装一个毒软定期检查

# Active Backup for Server
自动备份服务器文件，Linux需要安装rsync服务，windows只能备份局域网的smb

# 硬盘扩展
利用iSCSI扩展局域网windows硬盘空间，对于win系统非常好用。

# Time Machine
备份mac系统必备，比买apple服务便宜很多。

# 断电保护
准备买个bk650，暂时资金不够，而且很少断电，还没买。

