## Linux 使用技巧

### Linux查看和修改PATH环境变量的方法

查看PATH：echo $PATH

以添加mongodb server为例

修改方法1:

```
export PATH=/usr/local/mongodb/bin:$PATH
// 配置完成后可以通过echo $PATH查看配置结果
# 生效方式：立即生效
# 有效期限：临时改变，只能在当前的终端窗口中有效，当前窗口关闭后就会恢复原有的path配置
# 用户局限：仅对当前用户
```

修改方式2:

```
# 通过修改.bashrc文件
vim ~/.bashrc
# 在最后一行添加
export PATH=/usr/local/mongodb/bin:$PATH
# 生效方式：（有以下两种）
1.关闭当前终端窗口，重新打开一个新终端窗口就能生效
2.输入'source ~/.bashrc'命令，立即生效
# 有效期限：永久有效
# 用户局限：仅对当前用户
```

修改方式3:

```
# 通过修改profile文件
vim /etc/profile
# 找到设置PATH的行，添加
export PATH=/usr/local/mongodb/bin:$PATH
# 生效方式：系统重启
# 有效期限：永久有效
# 用户局限：对所有用户
```

修改方式4:

```
# 通过修改environment文件
vim /etc/environment
# 在PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:等等
末尾加入:/usr/local/mongodb/bin
# 生效方式：系统重启
# 有效期限：永久有效
# 用户局限：对有所用户
```

### Linux权限计算

eg：drwxr-xr-x

群组划分：user，group，other

文件的权限分为：read：4，write：2，execute：1，'-'对应无权限

drwxr-xr-x =>755

### Linux复制文件

`cp 目标文件 保存位置` 例如：`cp /root/redis/redis.conf /usr/local/redis/path/`

### Linux移动文件/文件夹/重命名文件夹

`mv 目标文件 目标路径`  例如：`mv /root/demo.txt /usr/local/bin`

`mv 目标文件夹 新的文件夹名称` 例如：`mv /root/redis-1.0.1 redis`

### Linux 删除文件/文件夹

`rm  文件/文件夹` 文件夹如果有内容，  `rm -rf 文件夹`

### Linux Vi/Vim编辑器操作

##### 查找

命令模式下 `/`  输入搜索的字符串

`n` 下一个匹配结果   `N` 上一个匹配结果