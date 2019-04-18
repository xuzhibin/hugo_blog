---
title: 在Coding部署Hexo
date: 2016-06-01
categories:
- tools
tags:
- hexo
---

网上的Hexo教程大多是部署在Github，在实际的使用中，Github速度比较满，有时更新还会抽风一下，于是想在国内找个替代方案。

目前来说，Coding从体验和速度来说，都是国内比较好的选择，阿里云虽然也有代码库，但是刚开始起步，很多都还不完善，但是如果做下去，未来看好。

下面以Coding为例，说一下部署的步骤。

# 注册

这个就不多说了，常规按步骤注册即可。[传送门](https://coding.net/register?key=99951510-5c9f-4659-b088-43f34e0d2b0e)

# 创建项目
1. 我们要创建的是 用户Pages，项目名需要和用户名一样，这样我们就可以用{user_name}.coding.me 形式的 URL 直接访问，适合用作个人或组织的演示网站。

2. 在新建项目里创建一个 coding-pages 分支，Coding默认是用 coding-pages 分支作为Pages的文件库。

3. 开启项目的Pages服务。

# Hexo配置
在Coding，我使用 coding-pages 分支发布Hexo，master 分支备份存储源文件。

备份Hexo我是用的插件是 [hexo-git-backup](https://github.com/coneycode/hexo-git-backup)

下面是我的配置文件，我是同时使用了Github和Coding服务，也可以只使用其中一个。


``` yaml
# 发布
deploy: 
- type: git
  repo: 
    github: git@github.com:xuzhibin/xuzhibin.github.io.git,master
    coding: git@git.coding.net:xuzhibin/xuzhibin.git,coding-pages 

# 备份
backup:
  type: git
  repository:
    coding: git@git.coding.net:xuzhibin/xuzhibin-backup.git,master
    github: git@github.com:xuzhibin/hexo-blog-backup.git,master
```

# 发布 & 备份

发布

```
hexo d
```

备份

```
hexo b
```

# 预览
Github: [xuzhibin.github.io](http://xuzhibin.github.io)

Coding: [xuzhibin.coding.me](http://xuzhibin.coding.me)

[Coding注册链接](https://coding.net/register?key=99951510-5c9f-4659-b088-43f34e0d2b0e)

# 其他
1. 域名绑定,在Coding的Pages标签页，是有域名绑定功能的，我目前的Hexo是用rsync方式发布在阿里云，所以没有具体测试。

2. Coding可以直接在仓库页打标签（Github没有这个功能），方便以后有需要时可以回滚。

3. Coding的Pages服务有几分钟的更新延迟。

