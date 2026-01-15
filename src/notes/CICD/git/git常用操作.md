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
# git reset


# git revert
```



### 参考

https://www.cnblogs.com/ahzxy2018/p/14482626.html

https://blog.csdn.net/weixin_42310154/article/details/119004977

https://zhuanlan.zhihu.com/p/425853213?utm_id=0

https://blog.csdn.net/qq_36125138/article/details/118606548

https://blog.csdn.net/u014361280/article/details/109047336#:~:text=%E4%BA%8C%E3%80%81%E8%8E%B7%E5%8F%96%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AF%EF%BC%8C%E5%B9%B6%E4%B8%8E%E6%9E%84%E5%BB%BA%E5%AF%B9%E5%BA%94%E6%9C%AC%E5%9C%B0%E5%88%86%E6%94%AF%E7%9A%84%E6%93%8D%E4%BD%9C%201%201%E3%80%81%E6%96%B9%E6%B3%95%E4%B8%80%EF%BC%9Agit%20checkout%20targetbranch%202%202%E3%80%81%E6%96%B9%E6%B3%95%E4%BA%8C%EF%BC%9Agit%20checkout,%E6%9F%A5%E7%9C%8B%E8%BF%9C%E7%A8%8B%E4%BB%93%EF%BC%8C%E6%89%BE%E5%88%B0%E8%BF%9C%E7%A8%8B%E8%A6%81%E5%90%88%E5%B9%B6%E7%9A%84%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AF%208%203%E3%80%81%E4%BB%8E%E8%BF%9C%E7%A8%8B%E4%dBB%93orgin%E4%BB%93%E7%9A%84%20%5BremoteBranchName%5D%20%E5%88%86%E6%94%AF%E4%B8%8B%E8%BD%BD%E5%88%B0%E6%9C%AC%E5%9C%B0%EF%BC%8C%E5%B9%B6%E5%9C%A8%E6%9C%AC%E5%9C%B0%E6%96%B0%E5%BB%BA%E4%B8%80%E4%B8%AA%E5%AF%B9%E5%BA%94%20%5BlocalBranchName%5D%20%E5%88%86%E6%94%AF%20%E6%9B%B4%E5%A4%9A%E9%A1%B9%E7%9B%AE