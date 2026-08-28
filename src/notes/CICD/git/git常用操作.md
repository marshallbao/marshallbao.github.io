# git 常用操作

```
git fetch --all

# 清理孤立或无法访问的 Git 对象
git prune

# 创建一个附注标签
git tag -a v2.0.0-rc -m "Release version 1.0"

# 查看标签
git tag

# 推送特定标签到远程
git push origin v1.0

# 推送所有标签到远程
git push origin --tags
```

commit

```
# commit
git commit -m "feat feature"

# 修改最后一个 commit 内容
git commit --amend -m "fix login bug"

# 漏掉了文件,把文件合并进上次提交，并且不会产生新的 commit 
git add xx
git commit --amend
```

分支

```
# 查看本地分支
git branch

# 查看远程分支
git branch -r

# 查看所有分支
git branch -a

# 创建本地分支
git branch branchname

# 同步本地分支至远程
git push --set-upstream origin test1

# 创建本地分支
# 另创建本地分支分两种情况
1. 在本地创建远程没有的分支
git branch targetbranch

2. 在本地创建远程已有的分支
方法1：git checkout targetbranch
方法2：git checkout -b 本地分支名 origin/远程分支名
方法3：git checkout --track origin/远程分支名

# 删除本地分支
git branch -d branchname

# 删除本地的远程分支
git branch -d -r branchname

# 删除远程 git 服务器上的分支
git push origin --delete origin/patch-1

-d 和 -D 的区别 
-d：删除指定分支。这是一个安全的操作，因为当分支中含有未合并的变更时，Git会阻止这一次删除操作。
-D：强制删除指定分支，即便其中含有未合并的变更。该命令常见于当开发者希望永久删除某一开发过程中的所有commit

# switch，切换分支
git switch 分支名字
```



远程

```
# 查看远程仓库信息
git remote -v

# 查看某个远程仓库的详细信息
git remote show  origin

# 清理本地仍然有被删除的远程分支的信息
git remote prune 

```



合并

```
# 挑选 commit 进行合并
git cherry-pick

```



回滚

```
## git restore
# 放弃修改在工作区的文件
git restore .

# 将暂存区恢复到工作区 （保留修改）
git restore --staged .

# 如果你修改了某个文件并且 add 到了暂存区，想彻底恢复到没有修改的状态
git restore --staged file && git restore file

## git reset
# git reset ，本质把当前分支的 HEAD 指针直接指向过去的某次 commit；（用于已经执行 git commit 的情况）

# --soft，仅仅把版本库回退，修改保存在暂存区
git reset --soft <commit-id>

# --mixed （默认参数），版本库回退，代码保留在工作区，但暂存区会被清空（变成未 add 状态）。
git reset --mixed <commit-id>

# --hard ，彻底抹杀！ 版本库、暂存区、工作区全部回退到指定 commit 的状态。你回退之后的全部修改都会被彻底删掉。
git reset --hard <commit-id>
！！！ 只有当你确定那些写错的代码一丝一毫都不要了，才用 --hard（本地的修改都不要了，完全只要仓库里的）

# 如果你的代码已经 push 到远程仓库（比如 GitHub、GitLab），并且别人已经拉取了你的代码，千万不要用 git reset！因为 reset 会改变历史，导致别人的本地仓库和你对不上。

## git revert
# git revert 的逻辑是：既然你做错了一件事，那我就再做一件相反的事来抵消它，并生成一次新的提交。
效果：比如你在 commit A 里增加了 10 行代码。运行 git revert A 后，Git 会自动创建一个新的 commit B，这个 B 的内容就是自动删掉那 10 行代码

git revert <写错的那次commit-id>

# 把当前目录下的所有文件从 Git 的暂存区（Staging Area）中删除，让 Git 不再追踪（Track）这些文件，但同时保留你本地硬盘上的物理文件。通常用来修复“忘记配置 .gitignore ”的失误。

git rm -r --cached .
```



tag

```
# 附注标签（推荐）
git tag -a v1.0.0 -m "Release version 1.0.0"


# 轻量标签
git tag v1.0.0-lw

# 给过去的提交打标签
git tag -a v0.9.0 9fceb02 -m "补打旧版本的标签"

# 列出 tag
git tag

# 查看 tag
git show v1.0.0

# 推送 tag
git push origin v1.0.0

# 推送所有 tag
git push origin --tags

# 删除 tag 
git tag -d v1.0.0

# 删除远程 tag
git push origin --delete v1.0.0
```



暂存/stash

```
# 暂存/stash
git stash

# 查看暂存列表
git stash list

# 拿出来用，顺便删掉（仅限最近一条或指定一条）。
git stash pop

# 指定用
git stash pop stash@{X}
git stash apply stash@{1}

# 指定删除
git stash drop stash@{X}

# 删除最近的一条
git stash drop

# 清理掉
git stash clear

```



### 参考

https://www.cnblogs.com/ahzxy2018/p/14482626.html

https://blog.csdn.net/weixin_42310154/article/details/119004977

https://zhuanlan.zhihu.com/p/425853213?utm_id=0

https://blog.csdn.net/qq_36125138/article/details/118606548

https://blog.csdn.net/u014361280/article/details/109047336#:~:text=%E4%BA%8C%E3%80%81%E8%8E%B7%E5%8F%96%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AF%EF%BC%8C%E5%B9%B6%E4%B8%8E%E6%9E%84%E5%BB%BA%E5%AF%B9%E5%BA%94%E6%9C%AC%E5%9C%B0%E5%88%86%E6%94%AF%E7%9A%84%E6%93%8D%E4%BD%9C%201%201%E3%80%81%E6%96%B9%E6%B3%95%E4%B8%80%EF%BC%9Agit%20checkout%20targetbranch%202%202%E3%80%81%E6%96%B9%E6%B3%95%E4%BA%8C%EF%BC%9Agit%20checkout,%E6%9F%A5%E7%9C%8B%E8%BF%9C%E7%A8%8B%E4%BB%93%EF%BC%8C%E6%89%BE%E5%88%B0%E8%BF%9C%E7%A8%8B%E8%A6%81%E5%90%88%E5%B9%B6%E7%9A%84%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AF%208%203%E3%80%81%E4%BB%8E%E8%BF%9C%E7%A8%8B%E4%dBB%93orgin%E4%BB%93%E7%9A%84%20%5BremoteBranchName%5D%20%E5%88%86%E6%94%AF%E4%B8%8B%E8%BD%BD%E5%88%B0%E6%9C%AC%E5%9C%B0%EF%BC%8C%E5%B9%B6%E5%9C%A8%E6%9C%AC%E5%9C%B0%E6%96%B0%E5%BB%BA%E4%B8%80%E4%B8%AA%E5%AF%B9%E5%BA%94%20%5BlocalBranchName%5D%20%E5%88%86%E6%94%AF%20%E6%9B%B4%E5%A4%9A%E9%A1%B9%E7%9B%AE