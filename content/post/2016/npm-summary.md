---
title: npm小结
date: 2016-05-29
categories:
- tools
tags:
- npm

---

随着一系列基于nodes的应用/工具的出现，无论在node应用的开发，还是使用中，包管理都扮演着一个很重要的作用。NPM（node package manager），作为node的包管理工具，极大地便利了我们的开发工作，很有必要了解一下。

# NPM是什么
NPM（node package manager），通常称为node包管理器。顾名思义，它的主要功能就是管理node包，包括：安装、卸载、更新、查看、搜索、发布等。
npm的背后，是基于couchdb的一个数据库，详细记录了每个包的信息，包括作者、版本、依赖、授权信息等。它的一个很重要的作用就是：将开发者从繁琐的包管理工作（版本、依赖等）中解放出来，更加专注于功能的开发。

npm官网：https://npmjs.org/

npm官方文档：https://npmjs.org/doc/README.html

# 我们需要了解什么
1. npm的安装、卸载、升级、配置
2. npm的使用：package的安装、卸载、升级、查看、搜索、发布
3. npm包的安装模式：本地 vs 全局
4. package.json：包描述信息
5. package版本：常见版本声明形式

# npm的安装 Mac
该命令执行后，自动装好node和npm。

``` bash
brew install node
```

## npm包安装模式
在具体介绍npm包的管理之前，我们首先得来了解一下npm包的两种安装模式。

## 本地安装 vs 全局安装（重要）
node包的安装分两种：本地安装、全局安装。两者的区别如下，后面会通过简单例子说明
本地安装：package会被下载到当前所在目录，也只能在当前目录下使用。
全局安装：package会被下载到到特定的系统目录下，安装的package能够在所有目录下使用。

## npm install pkg – 本地安装
运行如下命令，就会在当前目录下安装grunt-cli（grunt命令行工具）


``` bash
npm install grunt-cli
```

安装结束后，当前目录下回多出一个node_modules目录，grunt-cli就安装在里面。同时注意控制台输出的信息：


``` bash
grunt-cli@0.1.9 node_modules/grunt-cli
├── resolve@0.3.1
├── nopt@1.0.10 (abbrev@1.0.4)
└── findup-sync@0.1.2 (lodash@1.0.1, glob@3.1.21)
```

简单说明一下：

1. grunt-cli@0.1.9：当前安装的package为grunt-cli，版本为0.19
1. node_modules/grunt-cli：安装目录
1. resolve@0.3.1：依赖的包有resolve、nopt、findup-sync，它们各自的版本、依赖在后面的括号里列出来

## npm install -g pkg- 全局安装
上面已经安装了grunt-cli，然后你跑到其他目录下面运行如下命令


``` bash
grunt
```

果断提示你grunt命令不存在，为什么呢？因为上面只是进行了本地安装，grunt命令只能在对应安装目录下使用。


``` bash
-bash: grunt: command not found
```

如果为了使用grunt命令，每到一个目录下都得重新安装一次，那不抓狂才怪。肿么办呢？
很简单，采用全局安装就行了，很简单，加上参数-g就可以了


``` bash
npm install -g grunt-cli
```

于是，在所有目录下都可以无压力使用grunt命令了。这个时候，你会注意到控制台输入的信息有点不同。主要的区别在于安装目录，现在变成了/usr/local/lib/node_modules/grunt-cli，/usr/local/lib/node_modules/也就是之前所说的全局安装目录啦。


``` bash
grunt-cli@0.1.9 /usr/local/lib/node_modules/grunt-cli
├── resolve@0.3.1
├── nopt@1.0.10 (abbrev@1.0.4)
└── findup-sync@0.1.2 (lodash@1.0.1, glob@3.1.21)
```
<!-- more -->

# npm包管理
npm的包管理命令是使用频率最高的，所以也是我们需要牢牢记住并熟练使用的。其实无非也就是几个动作：安装、卸载、更新、查看、搜索、发布等。

**安装最新版本的grunt-cli**


``` bash
npm install grunt-cli
```

**安装0.1.9版本的grunt-cli**


``` bash
npm install grunt-cli@"0.1.9"
```

**通过package.json进行安装**

如果我们的项目依赖了很多package，一个一个地安装那将是个体力活。我们可以将项目依赖的包都在package.json这个文件里声明，然后一行命令搞定


``` bash
nam install
```

**其他package安装命令**
运行如下命令，列出所有npm install可能的参数形式


``` bash
npm install --help
```

输出如下，有兴趣的童鞋可以了解下


``` bash
npm install <tarball file>
npm install <tarball url>
npm install <folder>
npm install <pkg>
npm install <pkg>@<tag>
npm install <pkg>@<version>
npm install <pkg>@<version range>
```

**卸载grunt-cli**
比如卸载grunt-cli


``` bash
npm uninstall grunt-cli
```

卸载0.1.9版本的grunt-cli


``` bash
npm uninstall grunt-cli@"0.1.9"
```

**npm ls：查看安装了哪些包**

运行如下命令，就可以查看当前目录安装了哪些package


``` bash
npm ls
```

输出如下


``` bash
/private/tmp/npm
└─┬ grunt-cli@0.1.9
  ├─┬ findup-sync@0.1.2
  │ ├─┬ glob@3.1.21
  │ │ ├── graceful-fs@1.2.3
  │ │ ├── inherits@1.0.0
  │ │ └─┬ minimatch@0.2.12
  │ │   ├── lru-cache@2.3.0
  │ │   └── sigmund@1.0.0
  │ └── lodash@1.0.1
  ├─┬ nopt@1.0.10
  │ └── abbrev@1.0.4
  └── resolve@0.3.1
```

输出如下，同样，如果是要查看package的全局安装信息，加上-g就可以

**npm ls pkg：查看特定package的信息**
运行如下命令，输出grunt-cli的信息


``` bash
npm ls grunt-cli
```

输出的信息比较有限，只有安装目录、版本，如下：


``` bash
/private/tmp/npm
└── grunt-cli@0.1.9
```

如果要查看更详细信息，可以通过npm info pkg，输出的信息非常详尽，包括作者、版本、依赖等。


``` bash
npm info grunt-cli
```

**npm update pkg：package更新**


``` bash
npm update grunt-cli
```

**npm search pgk：搜索**
输入如下命令


``` bash
npm search grunt-cli
```

返回结果如下


``` bash
npm http GET http://registry.npmjs.org/-/all/since?stale=update_after&startkey=1375519407838
npm http 200 http://registry.npmjs.org/-/all/since?stale=update_after&startkey=1375519407838
NAME                  DESCRIPTION                                        AUTHOR            DATE              KEYWORDS
grunt-cli             The grunt command line interface.                  =cowboy =tkellen  2013-07-27 02:24
grunt-cli-dev-exitprocess The grunt command line interface.              =dnevnik          2013-03-11 16:19
grunt-client-compiler Grunt wrapper for client-compiler.                 =rubenv           2013-03-26 09:15  gruntplugin
grunt-clientside      Generate clientside js code from CommonJS modules  =jga              2012-11-07 01:20  gruntplugin
```

# npm发布
这个命令我自己也还没实际用过，不误导大家，语法如下，也可参考官方对于package发布的说明 https://npmjs.org/doc/developers.html：


``` bash
npm publish <tarball>
npm publish <folder>
```

# NPM配置
npm的配置工作主要是通过npm config命令，主要包含增、删、改、查几个步骤，下面就以最为常用的proxy配置为例。

## 设置proxy
内网使用npm很头痛的一个问题就是代理，假设我们的代理是 http://proxy.example.com:8080，那么命令如下：


``` bash
npm config set proxy http://proxy.example.com:8080
```

由于npm config set命令比较常用，于是可以如下简写


``` bash
npm set proxy http://proxy.example.com:8080
```

## 查看proxy
设置完，我们查看下当前代理设置


``` bash
npm config get proxy
```

输出如下：


``` bash
http://proxy.example.com:8080/
```

同样可如下简写：


``` bash
npm get proxy
```

## 删除proxy
代理不需要用到了，那删了吧


``` bash
npm delete proxy
```

## 查看所有配置


``` bash
npm config list
```

## 直接修改配置文件
有时候觉得一条配置一条配置地修改有些麻烦，就直接进配置文件修改了


``` bash
npm config edit
```

# 关于package.json
这货在官网似乎没有详细的描述，其实就是包的描述信息啦。假设当我们下载了node应用，这个node应用依赖于A、B、C三个包，如果没有package.json，我们需要人肉安装这个三个包（如果对版本有特定要求就更悲剧了）：


``` bash
npm install A
npm install B
npm install C
```

有了package.json，一行命令安装所有依赖。


``` bash
npm install
```

## package.json的生成


``` bash
npm init
```

## package.json字段简介
字段相当多，但最重要的的是下面几个

> name: package的名字（由于他会成为url的一部分，所以 non-url-safe 的字母不会通过，也不允许出现”.”、”_”），最好先在http://registry.npmjs.org/上搜下你取的名字是否已经存在

> version: package的版本，当package发生变化时，version也应该跟着一起变化，同时，你声明的版本需要通过semver的校验（semver可自行谷歌）
> dependencies: package的应用依赖模块，即别人要使用这个package，至少需要安装哪些东东。应用依赖模块会安装到当前模块的node_modules目录下。

> devDependencies：package的开发依赖模块，即别人要在这个package上进行开发

> 其他：参见官网

## package版本
在package.json里，你经常会在包名后看到类似”~0.1.0″这样的字符串，这就是包的版本啦。下面会列举最常见的版本声明形式，以及版本书写的要求：
常见版本声明形式
a、”~1.2.3″ 是神马意思呢，看下面领悟


```
"~1.2.3" = ">=1.2.3 &lt;1.3.0"
"~1.2" = ">=1.2.0 &lt;1.3.0"
"~1" = ">=1.0.0 &lt;1.1.0"
```

b、”1.x.x”是什么意思呢，继续自行领悟


```
"1.2.x" = ">=1.2.0 &lt;1.3.0"
"1.x.x" = ">=1.0.0 &lt;2.0.0"
"1.2" = "1.2.x"
"1.x" = "1.x.x"
"1" = "1.x.x"
```

版本书写要求
1. 版本可以v开头，比如 v1.0.1（v只是可选）
2. 1.0.1-7，这里的7是所谓的“构建版本号”，不理是神马，反正版本大于1.0.1
3. 1.0.1beta，或者1.0.1-beta，如果1.0.1后面不是 “连字符加数字” 这种形式，那么它是pre release 版本，即版本小于 1.0.1
4. 根据b、c，有：0.1.2-7 > 0.1.2-7-beta > 0.1.2-6 > 0.1.2 > 0.1.2beta

# 写在后面
内容只是简单地把最常见的命令，以及一些需要了解的内容列了出来。如要进一步了解，可参考官网说明。此外，npm help是我们最好的朋友，如果忘了有哪些命令，命令下有哪些参数，可通过help进行查看。