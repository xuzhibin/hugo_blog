---
title: Git 常用命令速查表
date: 2013-11-07
categories: 
- tools
tags: 
- git
---
# 配置


``` bash
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

# 创建版本库


``` bash
$ git clone [url] #克隆远程版本库
$ git clone git://github.com/schacon/grit.git
$ git clone git://github.com/schacon/grit.git mygrit
$ git clone --bare my_project my_project.git #创建纯仓库
$ git init #初始化本地版本库
$ git init --bare #创建纯仓库
```

# 修改和提交


``` bash
$ git status #查看状态
$ git diff #查看变更内容
$ git diff --cached #查看已经暂存起来的文件和上次提交时的快照之间的差异
$ git diff master...contrib #特性分支contrib和它同master分支的共同祖先之间的差异（合并时实际将要引入的新代码）
$ git add . #跟踪所有改动过的文件
$ git add [file] #跟踪指定的文件
$ git mv [old] [new] #文件改名
$ git rm [file] #删除文件
$ git rm --cached [file] #停止跟踪文件但不删除
$ git commit -m "commit message" #提交所有更新过的文件
$ git commit --amend #修改最后一次提交
$ git commit -a -m 'added new benchmarks' #跳过暂存区域直接提交
```

# 查看提交历史


``` bash
$ git log #查看提交历史
$ git log --pretty=oneline
$ git log -p [file] #查看指定文件的提交历史
$ git log master..experiemnt #所有可从experiment分支中获得而不能从master分支中获得的提交
$ git blame [file] #以列表方式查看指定文件的提交历史
$ git log origin/featureA ^featureA #比较origin/featureA及featureA分支，查看origin/featureA更新了哪些内容
```

# 撤消

``` bash
$ git reset --hard HEAD #撤消工作目录中所有未提交文件的修改内容
$ git reset --hard [commit] #会退到某个[commit]
$ git reset HEAD [file] #取消已经暂存的文件
$ git checkout HEAD [file] #撤消指定的未提交文件的修改内容[已暂存]
$ git checkout -- benchmarks.rb #取消对文件的修改[未暂存]
$ git revert [commit] #撤消指定的提交
```

# 分支与标签


``` bash
$ git branch #显示所有本地分支
$ git checkout [branch/tag] #切换到指定分支或标签
$ git checkout -b featureB origin/master #从分支origin/master克隆并创建分支featureB，切换至featureB
$ git branch [new-branch] #创建新分支
$ git branch sc/ruby_client master #从master分支克隆一个sc/ruby_client分支
$ git branch -d [branch] #删除本地分支
$ git branch --merged #查看哪些分支已被并入当前分支
$ git branch --no-merged #查看哪些分支未被并入当前分支
$ git tag #列出所有本地标签
$ git tag [tagname] #基于最新提交创建标签
$ git tag -d [tagname] #删除标签
```

# 合并与衍合


``` bash
$ git merge [branch] #合并指定分支到当前分支
$ git rebase [branch] #衍合指定分支到当前分支
```

# 远程操作


``` bash
$ git remote -v #查看远程版本库信息
$ git remote show [remote] #查看指定远程版本库信息
$ git remote add [remote] [url] #添加远程版本库
$ git remote  rename [old-remote-name] [new-remote-name]  #远程仓库的重命名
$ git remote rm [remote]   #远程仓库的删除
$ git fetch [remote] #从远程库获取代码
$ git pull [remote] [branch] #下载代码及快速合并至当前分支
$ git push [remote] [branch] #上传代码及快速合并
$ git push origin featureB[本地分支]:featureBee[远程分支] #推送本地分支至指定的远程分支
$ git push [remote] :[branch/tag-name] #删除远程分支或标签
$ git push --tags #上传所有标签
```

# 其他


``` bash
$ git describe master #生成内部版本号
$ git archive master --prefix='project/' | gzip > 'git describe master'.tar.gz #打包成tar
$ git archive master --prefix='project/' --format=zip > 'git describe master'.zip #打包成zip
$ git stash #储藏
$ git stash list #查看储藏列表
$ git stash apply stash@2 #应用名为stash@2 的储藏。如果你不指明，Git 默认使用最近的储藏并尝试应用它
$ git stash drop stash@{0} #移除名为stash@{0}的储藏
$ git blame -L 12,22 simplegit.rb #文件标注
```

# Git文件

.gitattributes #属性文件


``` bash
.doc diff=word
database.xml merge=ours
```

.gitignore #忽略某些文件


``` bash
# 此为注释– 将被Git忽略
*.[oa] # 忽略所有.o或.a结尾的文件
!lib.a # 但lib.a 除外
/TODO # 仅仅忽略项目根目录下的TODO文件，不包括subdir/TODO
build/ # 忽略build/目录下的所有文件
doc/*.txt # 会忽略doc/notes.txt但不包括doc/server/arch.txt
```
