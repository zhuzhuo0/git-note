## Mac 安装配置 redis

官网下载redis stable版本（稳定版本）

操作一下步骤

```
tar zxvf redis-xxx.tar.gz 解压
mv redis-xxxx /usr/local/ 移动解压出的文件夹到/usr/local/目录下 注：如果出现权限不足，命令前加上sudo
cd /usr/local/redis-xxxx  切换目录
sudo make test 编译测试（可跳过）
sudo make install 编译安装
```

安装成功后测试

```
# 启动redis
redis-server 
# 测试redis，新建一个终端，输入命令行redis-cli
```

