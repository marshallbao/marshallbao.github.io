# git 基础

### git 结构

工作区（写代码）

暂存区（临时存储）

本地库（历史版本）

工作区-> git add -> 暂存区 -> git commit -> 本地库（有新文件）

工作区-> git commit -> 本地库（无新文件）

远程仓库 <-> 本地仓库

远程库 —> clone -> 本地库 -> push -> 远程库 -> pull -> 本地库

git clone

clone是将一个库复制到你的本地，是一个本地从无到有的过程

git pull:pull先从远程下载git项目里的文件，然后将文件与本地的分支进行merge。

git pull = git fetch + git merge

A 远程仓库 <->B 远程仓库

A 远程仓库 -> fork -> B 远程仓库 -> clone -> 本地仓库 -> push -> B 远程仓库 -> pull request -> 审核 -> merge -> A 远程仓库

### 本地库初始化

git init

设置签名

用户名：

Email：

项目级别>系统用户级别

项目级别

git config user.name tom_pro

git config user.email kubernetes_pro@163.com

系统用户级别

git config -global user.name tom_glb

git config --global user.email kubernetes_glb@163.com

### 操作

```

# 常规操作
git status # 查看工作区和暂存区的状态
git add hello.txt   #将工作区文区提交到暂存区
git rm --cached hello.txt  #从暂存区删除（不影响工作区文件）
git commit -m "My second commit for hello.exe" hello.txt  #将暂存区提交到本地仓库

git log #查看提交日志
git log --pretty=oneline
git log --oneline
git reflog

# 撤销
# 只是修改了工作区
git restore  <file>
# 已经 add 到了缓存区
git restore --staged <file>
# 已经 commit 到了本地仓库
git reset hash
# 执行命令后会将本次提交的内容全部放置工作区供编辑；
前进后退
基于索引值【推荐】
--hard！！！！慎用,使用hard模式，会使此次hash之后所有的内容都会没有，包括本地、远程！！！！！
$ git reset --hard dbdc027
使用^：只能往后推退
$ git reset --hard HEAD^^  #几个^往后退几步
使用~：只能往后推退
$ git reset --hard HEAD~3  #往后回退3步

git reset soft:仅仅修改本地库
git reset mixed:修改本地库，重置暂存区
git reset hard:3个区域都修改

找回删除的文件(已经上传至本地版本库了)
$ cat testrm  #编辑文件
$ git add testrm #上传次至缓存区
$ git commit -m "The first commit for testrm" testrm  #提交至本地版本库
$ rm testrm  #删除工作区文件
$ git add testrm  ##上传次至缓存区
$ git commit -m "delete testrm" testrm   #提交至本地版本库，这时版本库里有删除记录了
$ git reset --hard 61b133d #回到添加这个文件的版本，工作区，缓存区，本地版本库就都有testrm了
找回删除的文件（已经上传至缓存区了）
$ git reset --hard HEAD #因为删除文件的操作没有上传至本地库，所以直接更新为有这个文件最新的head即可；
前提：删除前，文件存在的状态已经提交到本地库了
操作：git reset --hard #指向文件操作的版本；
diff
$ git diff testrm     #将工作区与缓存区文件进行比较
$ git diff HEAD^ testrm  #将工作与本地库某个版本进行比较
$ git diff   #所有文件进行比较

```

### 分支

在版本控制过程中，使用多条线同时推进多个任务；

并行推进多个供能开发，提高开发效率

如果分支失败了，不会影响其他分支及 master，删除重来就好

```
# 分支操作
$ git branch -v    #查看分支
$ git branch hot_fix    #新增分支
$ git checkout hot_fix   #切换分支

# 分支合并至master
# 切换至 master
$ git checkout master

# 合并
$git merge hot_fix
```

### git  账号密码存储

```
1. 放在 git config 里，global 和 仓库下的都行

[url "https://hairui:glpat-3-xxx@gitlab.blockbeat.hk"]
        insteadOf = https://gitlab.blockbeat.hk

2. 放在 .git-credentials 静态存储，分为 global 和 仓库
   git config --global credential.helper store
   git config credential.helper store


3. 根据电脑系统放在自带的私钥管理里面
   Mac
   git config --global credential.helper osxkeychain


windows
git config --global credential.helper manager

linux（需要桌面版）
git config --global credential.helper libsecret


4. GCM（还未测试）
git config --global credential.helper manager-core




其他操作
查看
git config --global credential.helper

配置
git config --global credential.helper store
git config --global credential.helper manager

修改
git config --file=.git/config credential.helper "store"
git config --file=~/.git_credentails --global credential.helper "store"

取消
git config --global --unset credential.helper
```

参考

https://www.cnblogs.com/wutou/p/17585668.html