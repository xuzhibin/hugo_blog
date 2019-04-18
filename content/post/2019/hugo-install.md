---
title: 博客转为hugo
date: 2019-04-16
categories:
- tools
tags:
- hugo
toc: true
gitment: true
slug: hugo-install
---
hexo生成速度越来越慢了，决定转为hugo，hugo安装使用简单，不过模板数量和质量的确和hexo有差距。

hugo生成速度非常快，以前hexo生成站点要10几秒，hugo在1秒以内。
# 安装
[Hugo中文文档](https://www.gohugo.org)

[Next模板](https://github.com/xtfly/hugo-theme-next)

[Hugo 从入门到会用](https://blog.olowolo.com/post/hugo-quick-start/#url管理)
<!--more-->
# 配置
将模板下的config.toml复制到根目录修改即可
# 运行
``` bash
hugo server
```
浏览器里打开： http://localhost:1313

# 生成
``` bash
hugo
```
会在根目录下生成public目录

生成静态网站时，hugo 会忽略所有通过 draft: true 标记为草稿的文件。必须改为 draft: false 才会编译进 HTML 文件。
# 发布
将根目录下生成的public目录上传至服务器即可
# FAQ
## 代码高亮
highlight.js这个插件使用起来特别方便，还可以定制化选择需要高亮的语言，只需要在[highlight.js官网](https://highlightjs.org/download/)选择所需要的语言，下载即可。然后解压文件中，highlight.pack.js决定那些语言的代码会根据语法进行高亮，在style文件夹中选择喜欢的css样式文件，css文件决定代码高亮的颜色，最后我选择了github-gist.css

然后需要在<head>中增加：

```html
<link rel="stylesheet" href="/path/to/styles/default.css">
```

在<body>结束前增加：

``` html
<script src="/path/to/highlight.pack.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
```
## 内容截断
只需要在需要进行分隔的地方添加一个`<!--more-->`标签即可，这样标签后面的内容则需要点Read more才可完全显示。
## 评论
使用Gitment实现

[Hugo集成Gitment评论插件](http://www.qingpingshan.com/m/view.php?aid=377906)

[解决gitment无法登陆评论问题（Object ProgressEvent）](https://blog.csdn.net/wardseptember/article/details/82828391)

[Hexo 添加 Gitment 评论](https://sogrey.github.io/article/Hexo-添加-Gitment-评论/)

[完美替代多说-gitment](https://www.colabug.com/216603.html)
