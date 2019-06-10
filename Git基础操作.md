## Git基础操作

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
git reset --soft commit_id
# 撤销且不保留改动（不推荐）
git reset --hard commit_id
```

拉取最新版本

```
git pull 
```

