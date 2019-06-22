## Git基础操作

修改username 和 email

```
# 仅对当前仓库有效
git config user.name 'xxxxxx'
git config user.email 'xxxxx'

# 全局修改
git config --global user.name 'xxxxxx'
git config --global user.email 'xxxxxx'
```

添加更改到暂存区

```
git add [path,filename]

git add -A 全部内容添加到暂存区

git add /dir/* 将dir文件夹中的更改添加到暂存区

git add file.txt /dir/fileb.txt  将file.txt,dir文件夹中的fileb.txt 添加到暂存区
```

将暂存区里的改动给提交到本地的版本库

```
git commit -m "xxxxxxxx"
# 注意：提交说明必须要双引号
```

推送到远程仓库

```
# 注：每次推送之前应先拉取远端仓库最新代码，合并冲突之后再次提交
# 完整写法
git push <远程主机名> <本地分支名>:<远程分支名>
# 查看已经配置的远程仓库服务器-远程主机名
git remote
# 示例 将本地master分支推送到远程仓库master分支
git push origin master:master
```

放弃改动

```
# 放弃所有改动
git checkout . 
# 放弃某个文件改动
git checkout -- README.md
```

添加（add）到缓存区后撤销操作

```
git reset --mixed
```

查看提交记录

```
git log
```

提交之后（commit）撤销操作

```
# 撤销并且保留改动（推荐）
git reset --soft commit_id 注：commit_id 是指要还原到的目标commit_id
# 撤销且不保留改动（不推荐）
git reset --hard commit_id 注：commit_id 是指要还原到的目标commit_id
```

拉取最新版本

```
git pull 
```

拉取其他分支代码

```
# 默认clone的是仓库的master分支
git clone git@gitlab.xxx.com:xxxxx.git
# 解决办法
git branch -r  # 查看远程分支
OR
git branch -a  # 查看所有分支
# 直接拉取
git checkout origin/xxxx

# git clone 默认会把远程仓库整个clone，但只会在本地默认创建一个master分支
# 拉取远端分支并且在本地建立同名分支
git checkout -t origin/xxxx
OR
git fetch origin xxxxx:xxxxx
# 不过通过fetch命令来建立的本地分支不是一个track branch，而且成功之后不会自动切换到该分支上
```

git reset 三种用法
```
# 回退一个版本,且会将暂存区的内容和本地已提交的内容全部恢复到未暂存的状态,不影响原来本地文件(未提交的也 
不受影响) 
git reset (–mixed) HEAD~1 

# 回退一个版本,不清空暂存区,将已提交的内容恢复到暂存区,不影响原来本地的文件(未提交的也不受影响) 
git reset –soft HEAD~1 

# 回退一个版本,清空暂存区,将已提交的内容的版本恢复到本地,本地的文件也将被恢复的版本替换
git reset –hard HEAD~1
```

git版本回滚（方法一）

```
# 本地回滚
git reset --hard commit_id  注：commit_id是目标id
git reset --hard HEAD~3：将最近3次的提交回滚
git reset --hard HEAD~100：将最近100次的提交回滚
# 扩展   --hard选项表示彻底将工作区、暂存区和版本库记录恢复到指定的版本库，HEAD^表示回滚到上一个版本，HEAD^^表示回滚到上上一个版本，以此类推，如果要回滚到上100个版本，可以使用HEAD~100
# 回滚远端分支
git push -f orgin branch_name  注：将本地的远端分支强制push到远端仓库，覆盖
```

git版本回滚（方法二 针对强推不起作用并且是非master分支）

```
# 对本地仓库进行回滚
git reset --hard commit_id  注：commit_id是目标id
# 备份，删除远程分支，将本地分支回滚后的分支推送到远端
git checkout the_branch  切换分支
git pull
git branch the_branch_backup   备份一下这个分支当前的情况
git reset --hard the_commit_id   把the_branch本地回滚到the_commit_id
git push origin :the_branch   删除远程 the_branch
git push origin the_branch //用回滚后的本地分支重新建立远程分支
git push origin :the_branch_backup //如果前面都成功了，删除这个备份分支
```

[Git 执行 「fork 出来的仓库」和「最新版本的原仓库」内容同步更新](https://www.cnblogs.com/huangjianwu/p/4899672.html)

当我们在 GitHub 上 fork 出一个仓库后，如果原仓库更新了，此时怎样才能保证我们 fork 出来的仓库和原仓库内容一致呢？我们一般关注的是仓库的 master（主干分支）的内容，通过以下步骤来保证他最新就可以了。

```
# 添加一个远程仓库，此时只是空壳，还没有同步内容，引用 fork 的原仓库地址
git remote add originUpstream 主仓库git地址
# 更新远程仓库，从引用 fork 的原仓库地址同步内容，此时原仓库的 master（主干分支）已经可以在本地访问了
git remote update originUpstream
# checkout（检出）用于操作的本地分支 ，比如 master；
git checkout master
# 直接从远程仓库的分支 pull（拉取）数据
git pull originUpstream master
# 把本地已检出分支的已提交数据 push（推送） 到自己 fork 的仓库中
git push origin master
```

五步git操作搞定Github中fork的项目与原作者同步

```
# git clone fork的项目到本地
git clone fork的仓库地址
# 进入刚才clone的文件目录下，然后增加源分支地址到你项目远程分支列表中
git remote add 主仓库对应的分支 主仓库地址
# fetch源分支到本地
git fetch 更新的分支
# 合并两个版本的代码
git merge 更新的分支/本地的分支
# 最后一步，把合并后的代码push到你的Github项目上去
git push origin master
```

[GitHub上 fork别人的项目 源作者更新后如何同步更新](https://blog.csdn.net/weixin_40928253/article/details/84665971)

说明：github远程实现fork仓库更新

查看本地仓库关联的远程仓库

```
# 查看当前的远程库
# 要查看当前配置有哪些远程仓库，可以用 git remote 命令，它会列出每个远程库的简短名字。在克隆完某个项目后，至少可以看到一个名为 origin 的远程库，Git 默认使用这个名字来标识你所克隆的原始仓库
$ git clone git://github.com/schacon/ticgit.git
Cloning into 'ticgit'...
remote: Reusing existing pack: 1857, done.
remote: Total 1857 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (1857/1857), 374.35 KiB | 193.00 KiB/s, done.
Resolving deltas: 100% (772/772), done.
Checking connectivity... done.
$ cd ticgit
$ git remote
origin

# 也可以加上 -v 选项（译注：此为 --verbose 的简写，取首字母），显示对应的克隆地址：
$ git remote -v
origin  git://github.com/schacon/ticgit.git (fetch)
origin  git://github.com/schacon/ticgit.git (push)

# 如果有多个远程仓库，此命令将全部列出。比如在我的 Grit 项目中，可以看到：
$ cd grit
$ git remote -v
bakkdoor  git://github.com/bakkdoor/grit.git
cho45     git://github.com/cho45/grit.git
defunkt   git://github.com/defunkt/grit.git
koke      git://github.com/koke/grit.git
origin    git@github.com:mojombo/grit.git

# 添加远程仓库
要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用，运行 git remote add [shortname] [url]：

# 从远程仓库抓取数据
git fetch [remote-name]
```

