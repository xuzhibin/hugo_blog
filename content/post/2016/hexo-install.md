---
title: Hexo安装记
date: 2016-05-30
categories: 
- tools
tags: 
- hexo
---
# Hexo是啥
[Hexo](https://hexo.io) 是由 Node.js 驱动的一款快速、简单且功能强大的博客框架。可以快速将 Markdown 格式的文档生成静态页面。是越来越笨重的 Wordpress 和麻烦的空间管理的替代方案。


# 安装 Hexo
首先翻看一遍 [Hexo 官方文档](https://hexo.io/zh-cn/)

## 环境准备
安装 Node.js & Git

该命令执行后，自动装好node和npm。

``` bash
brew install node
```

## 配置 Git
按照Github提示，创建SSH秘钥：


``` bash
$ mkdir ~/.ssh #创建ssh命令
$ cd ~/.ssh
$ ssh-keygen -t rsa -C "your_email@example.com" #注意更改Email地址
$ pbcopy < ~/.ssh/id_rsa.pub
```


此时，pdcopy命令已经将id_rsa.pub内容复制到剪切板。

登录 github：https://github.com/settings/ssh 点击Add an SSH Key，Title 部分填写一个所用电脑的名称，然后在Key部分填写之前通过pdcopy命令复制的内容。生成 key https://help.github.com/articles/generating-ssh-keys/


``` bash
$ git config --global user.name "yourusername"
$ git config --global user.email "youremail"
```

## 测试
可以输入下面的命令，看看设置是否成功，git@github.com的部分不要修改：

``` bash
$ ssh -T git@github.com
```

如果是下面的反馈：


``` bash
The authenticity of host 'github.com (207.97.227.239)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)?
```

不要紧张，输入yes就好，然后会看到：


``` bash
Hi cnfeat! You've successfully authenticated, but GitHub does not provide shell access.
```


## 安装 Hexo

``` bash
$ npm install -g hexo-cli
```


好了，这个时候如果报错，记得加 sudo


``` bash
$ sudo npm install -g hexo-cli
```
<!-- more -->

# 配置 Hexo
进入想要存放博客的文件夹，初始化设置：


``` bash
$ hexo init
$ npm install
```

初始化成功后，文件夹里会有这些东西：


``` bash
.
├── _config.yml        #配置文件
├── package.json    #应用程序数据
├── scaffolds
├── source            #网站内容
|   ├── _drafts        #草稿
|   └── _posts        #文章
└── themes            #主题
```

配置文件的详细说明请查看官方文档。


# 本地访问博客
生成静态文件：

``` bash
$ hexo generate
```


启动本地服务：

``` bash
$ hexo server
```


这时提示

``` bash
Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
```


然后在浏览器打开 http://0.0.0.0:4000/，就可以看到迁移好的博客啦！

# 挑选主题
可参考 有那些好看的 Hexo 主题？

下载主题：
``` bash
git clone https://github.com/xxxx themes/xxx
```


``` bash
$ cd your-hexo-site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

启用主题：克隆/下载 完成后，打开 站点配置文件 “_config.yml”，找到 theme 字段，并将其值更改为下载的主题名字。

剩下的就根据主题使用文档，按照自己的喜好修改了。

我选的 [NEXT主题](http://theme-next.iissnan.com) 胜在 [说明文档](http://theme-next.iissnan.com/getting-started.html) 超详细的~

# 安装插件
**站内搜索**
hexo-generator-search插件为 Hexo3 提供搜索功能。

安装：


``` bash
$ npm install hexo-generator-search --save
```


**Feed**
hexo-generator-feed 可以生成Atom 1.0 或 RSS 2.0 feed。

安装：


``` bash
$ npm install hexo-generator-feed --save
```

**Sitemap**
说明

安装：


``` bash
$ npm install hexo-generator-seo-friendly-sitemap --save
```


# 配置 Github 并上传
新建跟用户名同名的 repository，以“.github.io” 为后缀

创建成功后，点击界面右侧的Settings，打开页面后找到GitHub Pages，点击Launch automatic page generator，生成 page。

然后在网站配置文件 _config.yml文件中，找到Deployment，修改配置：


``` bash
deploy:
  type: git
  repo: git@github.com:yourname/yourname.github.io.git
  branch: master
```

如果使用 git 方式部署，执行 
``` bash
npm install hexo-deployer-git --save
```
命令来安装插件。


安装成功后，在当前目录进行 deploy 操作


``` bash
hexo d
```


上传成功后，访问 yourname.github.io ，测试是否可以正常访问。

# 绑定域名
在 Github 里生成 CNAME 文件
为了绑定域名，首先在 Hexo 的 "source" 文件夹中新建一个CNAME文件（没有后缀），文件内容为你要设置的域名。目的是将你的域名（如 uegeek.com ）指向 Github 服务器。

Deploy 到 Github上去后大概需要几分钟生效。


到 DNSPod 新建域名，并添加 CNAME 记录指向 kidult00.github.io 。

当打开 uegeek.com 看到改头换面的博客后，内心默默地原谅自己拖延了两年 T.T

# Hexo设置
## 站点设置

``` ini
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/tommy351/hexo/

# Site #整站的基本信息
title: 1000 words a Day #网站标题
subtitle: Writing 1000 Words a Day Changes My Life #网站副标题
description: 学习总结 思考感悟 知识管理 #网站描述
author:  cnFeat #网站作者，在下方显示
email: cnFeat@gmail.com #联系邮箱
language: zh-CN

# URL
## If your site is put in a subdirectory
url: http://www.cnfeat.com #你的域名
root: /
permalink: :year/:month/:day/:title/
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code

# Directory
source_dir: source
public_dir: public

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
auto_spacing: false # Add spaces between asian characters and western characters
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
max_open_file: 100
multi_thread: true
filename_case: 0
render_drafts: false
post_asset_folder: false
highlight:
  enable: true
  line_number: true
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Archives
## 2: Enable pagination
## 1: Disable pagination
## 0: Fully Disable
archive: 2
category: 2
tag: 2

# Server
## Hexo uses Connect as a server
## You can customize the logger format as defined in
## http://www.senchalabs.org/connect/logger.html
port: 4000
server_ip: 0.0.0.0
logger: false
logger_format:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: H:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 15 #每页15篇文章
pagination_dir: page

# Disqus #社会化评论disqus，我使用多说，在主题中配置
disqus_shortname:

# Extensions
## Plugins: https://github.com/tommy351/hexo/wiki/Plugins
## Themes: https://github.com/tommy351/hexo/wiki/Themes
theme: jacman
exclude_generator:
Plugins:
- hexo-generator-feed
- hexo-generator-sitemap

#sitemap
sitemap:
  path: sitemap.xml

#Feed Atom
feed:
  type: atom
  path: atom.xml
  limit: 20

# Markdown
## https://github.com/chjj/marked
markdown:
  gfm: true
  pedantic: false
  sanitize: false
  tables: true
  breaks: true
  smartLists: true
  smartypants: true

# Stylus
stylus:
  compress: false

# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: github
  repository: https://github.com/xuzhibin/xuzhibin.github.io.git
  branch: master
```

## 模版设置

``` bash
.
├── languages  #多语言
|   ├── default.yml#默认语言
|   └── zh-CN.yml  #中文语言
├── layout #布局，根目录下的*.ejs文件是对主页，分页，存档等的控制
|   ├── _partial   #局部的布局，此目录下的*.ejs是对头尾等局部的控制
|   └── _widget#小挂件的布局，页面下方小挂件的控制
├── source #源码
|   ├── css#css源码 
|   |   ├── _base  #*.styl基础css
|   |   ├── _partial   #*.styl局部css
|   |   ├── fonts  #字体
|   |   ├── images #图片
|   |   └── style.styl #*.styl引入需要的css源码
|   ├── fancybox   #fancybox效果源码
|   └── js #javascript源代码
├── _config.yml#主题配置文件
└── README.md  #用GitHub的都知道
```

# 常用命令
## 发表新文章

``` bash
$ hexo n #写文章
```

其中my new post为文章标题，执行命令后，会在项目\source_posts中生成my new post.md文件，用编辑器打开编写即可。

当然，也可以直接在\source_posts中新建一个md文件，我就是这么做的。

写完后，推送到服务器上，执行


``` bash
$ hexo g #生成
$ hexo d #部署 # 可与hexo g合并为 hexo d -g
```
## 用Hexo发表文章的Markdown语法

``` bash
title: postName #文章页面上的显示名称，可以任意修改，不会出现在URL中
date: 2013-12-02 15:30:16 #文章生成时间，一般不改，当然也可以任意修改
categories: example #分类
tags: [tag1,tag2,tag3] #文章标签，可空，多标签请用格式，注意:后面有个空格
description: 附加一段文章摘要，字数最好在140字以内。
---

以下正文
```

## 常用命令

``` bash
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub
```

## 常用复合命令


``` bash
hexo d -g #生成加部署
hexo s -g #预览加部署
```

## 简写


``` bash
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```

# Hexo备份

已有人写了插件，[hexo-git-backup](https://github.com/coneycode/hexo-git-backup)，按步骤操作即可；


``` bash
$ npm install hexo-git-backup --save
```


修改站点配置文件 _config.yml.


``` bash
backup:
    type: git
    repository:
       github: git@github.com:xxx/xxx.git,branchName
       gitcafe: git@github.com:xxx/xxx.git,branchName
```

使用


``` bash
hexo backup
```


or


``` bash
hexo b
```
# Hexo部署在VPS


``` bash
$ npm install hexo-deployer-rsync --save
```

根据 [Hexo rsync 部署文档](https://hexo.io/zh-cn/docs/deployment.html#Rsync) 在 Hexo 中的 _config.yml 中添加 Deploy 的配置，以下是我在自己项目中的配置代码


```
deploy:
  type: rsync
  host: 你 VPS 的 IP 地址或者域名
  user: root
  root: 你想将 Hexo 生成的静态文件存放在 VPS 中的目录 例如: /www/hexo/blog/
  port: 你 VPS 的 ssh 端口号
  delete: true
  verbose: true
  ignore_errors: false
```

*必须先在 VPS 上创建好了想要存放 Hexo 静态文件的目录， rsync 不能创建不存在的目录

我看网上有的朋友说光在 Hexo 中使用 rsync 不够，在 VPS 也需要下载 rsync 服务，但是我在阿里云上就没有下载 rsync 服务。如果你在 Hexo 中用 rsync 部署的时候一直报错，那就在 VPS 上下载 rsync 服务，看能否解决一直出错的原因，命令如下

``` bash
$ yum -y install rsync
```

如果rsync 报下面的错误


``` bash
rsync error: some files could not be transferred (code 23) at main.c(702)
```


解决方法: rsync的source有些文件没有读权限，所以报错。

``` bash
chmod +r  ./* -R
```

再次同步，即可OK.

# 相关链接
[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://www.jianshu.com/p/05289a4bc8b2)