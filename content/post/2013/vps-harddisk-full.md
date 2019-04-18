---
title: VPS硬盘被撑满
date: 2013-04-12
categories: 
- server
tags: 
- vps
- w3tc
---
早上打开博客，发现显示“无法连上数据库”，遂登录ssh，重启服务。重启后，Wordpress可以访问了，但是标签及分类目录全部丢失。一阵Google，百度后，大多说是缓存爆满导致。


``` bash
df -lh
```

果然，硬盘空间全部满了。于是寻找是哪个目录的问题。

1. 先du -sh /*, 在/home目录的地方卡住了，然后Ctrl+C, 然后


``` bash
ll|grep -v "total"|grep -v "home"|awk '{print $9}'|xargs du -sh
```

2. 发现其他目录不大，知道是/home目录的问题了
3. 递归对/home目录使用上面的方法处理

最后，发现是wordpress目录下的 wp-content/cache/* 超过10G了，此目录是 W3 Total Cache插件 的缓存目录，清空以下目录：

wp-content/cache/object/*

wp-content/cache/db/*

wp-content/cache/page/*

wp-content/cache/tmp/*

重启服务，OK。

此文给使用W3TC插件的同学一个提醒。