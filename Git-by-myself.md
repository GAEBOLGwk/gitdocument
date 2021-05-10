

# GIT笔记

```
应用于创建一个新的项目
git init
git add .
git commit -m "first commit"
git branch -M master 添加一个主分支
git remote add origin https://github.com/GAEBOLGwk/test.git 把本地仓库与GitHub仓库进行关联 这里的origin是自己起的名字，可以更改
git push -u origin master
```



```
git status ./           查看这个文件夹下的文件状态，会列出有哪些没有加入追踪，哪些没有commit
git add ./*             把这个文件下的所有应该加入追踪的，加入到暂存区
git commit -m "日志描述" ./           把这个文件夹下可以commit的，都commit到本地库
git push                push到远程库
```



```
git pull --rebase origin master ，# 此为把最新版本的远程仓库源码更新到本地（不可缺失的步骤），如果有冲突则解决冲突，再执行后续操作
git diff XX 查看XX文件修改了那些内容
```



**状态**

仓库中的文件通常处于 3 种状态下：

1. **已追踪 Tracked** ----已在版本库中的文件，或是已暂存到索引中的文件
2. **已忽略 Ignored** ----版本库中存在但不需要追踪的文件
3. **未追踪 Untracked** ----不属于以上两者的文件

而文件的修改状态有以下 3 种：

1. **已修改 modified** ----- 在工作目录中修改文件

2. **已暂存 staged** ----- 暂存文件，将已修改的文件的快照放入暂存区域

3. **已提交 committed** ----- 找到暂存区域的文件，将快照永久性存储到 Git 仓库目录

   

## 远程仓库

**查看**

```
$ git remote

# 查看并展示地址
$ git remote -v
origin  https://github.com/Jax/A.git (fetch)
origin  https://github.com/Jax/A.git (push)

# 查看更多信息
$ git remote show <remote-name>
```

**增删**

```
# 增加
$ git remote add <remote-name> <url>

# 删除
$ git remote rm <remote-name>

# 删除某个本地分支对应的远端
$ git branch -r -d <remote-name>/<branch>

# 删除本地版本库中陈旧远程追踪分支
$ git remote prune

# 重命名
$ git remote rename <old_name> <new_name>

# 直接修改某个远端的地址
$ git remote set-url origin git://repos.example.com/stuff.git
```

**推拉**

```
# 推送代码
$ git push <remote> <local-branch>:<remote-branch>
# 抓取后自动合并远程分支到当前分支
$ git pull
# 拉取某个远端代码
$ git fetch <remote-name>

它与 git pull 的关系可大概理解为 git pull = git fetch + git merge
```



## 分支

**查看**

```
$ git branch
# --merged  查看已合并
# --no-merged 查看未合并

# 查看每一个分支的最后一次提交
$ git branch -v

HEAD 指向当前所在的本地分支，ORIG_HEAD 指向原始HEAD
```

**增删-切换**

```
# 创建分支，加上提交码可指定提交
$ git branch <branch-name> <commit>

# 创建并切换
$ git checkout -b <name>

# 手动切换分支
$ git checkout <branch-name>

# 删除分支
$ git branch -d <branch-name>

# 删除某个远端分支（本地还保留）
$ git push -d <branch-name>
```

## 变基

-----------**直接挪动指针的神器之一**

常见用途是**保持你正在开发的分支相对于主分支是最新的**

如**我从主分支 master 上新建一个功能分支 feature-upload**，开发当前迭代的新功能，

某位同事修复了一个重大bug并提交到了 master 分支上，此时可用变基获取这些修改

一如既往，获取修改时可能遇到需手动处理的冲突 ~~~

```
# 将feature-upload分支开出来时所在的“基”挪到 master 指针所在的位置
$ git rebase master

# 也可通过第二参数指定被挪的分支
$ git rebase <to-branch> <base-branch>

# 没有特殊需要的情况下，下列指令也可以达到相同目的
$ git pull --rebase
```









## 常见错误

**3.上传时发生错误**

```
fatal: Unable to create lock': File exists.

Another git process seems to be running in this repository, e.g.
an editor opened by 'git commit'. Please make sure all processes
are terminated then try again. If it still fails, a git process
may have crashed in this repository earlier:
remove the file manually to continue.
```

分析原因：是由于你之前上传时发生错误，比如断网了，github出现time out等上传出现错误，但是那是还保持着之前的命令，所以需要删除index

在Windows中，从repo目录的命令提示符中执行以下操作：

```
删除工作树未完成的操作
cd .git
del index.lock
```



**2.add添加后悔了想撤销**

```
最好的方式就是看他怎么提示你，有哪个就来哪个

add添加后悔了用撤销一下
git rm --cached <file>

这个是当本地向另一个库添加add，导致.gitignore发生变化，（就是原本remote是show.git，但是现在换了，所以要用这个）
git restore --staged 将文件从暂存区撤出，但不会撤销文件的更改
git restore --staged <file>: 撤销 暂存区

git resore 将不在暂存区的文件撤销更改
git restore <file>: 撤销 本地修改
```



**1.更新被拒绝，因为远程包含您确实提示的工作：在本地没有。这通常是由另一个存储库推送提示引起的：到相同的引用。您可能需要先整合远程更改**

```
error: failed to push some refs to 'https://github.com/GAEBOLGwk/show.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

git push -f origin master
死亡操作，就是把你远程库上的删除，无视规则，总之就是听我的，这样远程库只有你刚推上去的内容
```

解决方案

```
先拉再推
因为你电脑里的内容是比较旧的，所以你应该先拉一个线上版本的回来更新，然后再推一次：
git pull https://github.com/GAEBOLGwk/xxx.git
git push origin master
```

