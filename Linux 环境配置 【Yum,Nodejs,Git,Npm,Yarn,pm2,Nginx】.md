## Linux 环境配置 【Yum,Nodejs,Git,Npm,Yarn,pm2,Nginx】

### Yum配置

```
# 查看yum配置
yum info yum
# 修改yum源为阿里源
# 打开centos的yum文件夹
cd /etc/yum.repos.d/
# 用wget下载repo文件
wget http://mirrors.aliyun.com/repo/Centos-7.repo
# 如果wget命令不生效，说明还没有安装wget工具，输入yum -y install wget 回车进行安装。
# 当前目录是/etc/yum.repos.d/，刚刚下载的Centos-7.repo也在这个目录上
# 备份系统原来的repo文件
mv CentOS-Base.repo CentOS-Base.repo.back  
# 将下载的repo文件更名为配置文件
mv Centos-7.repo CentOS-Base.repo
# 依次执行一下文件
yum clean all
yum makecache
yum update
# 查看配置的源地址是否已更改
yum info yum
```

### Nodejs配置

```
# node官网下载nodejs包 选择linux 64位
# tar文件上传到服务器并解压，然后通过建立软连接变为全局
tar -xvf node-v10.15.2-linux-x64.tar.xz
# 更改解压后的文件夹名称
mv node-v10.15.2-linux-x64 nodejs
# 确认下nodejs下bin目录是否有node和npm文件,如果有则继续执行，没有请重新下载并重复以上步骤
# 建立软连接
ln -s /root/nodejs/bin/npm /usr/local/bin/npm
ln -s /root/nodejs/bin/node /usr/local/bin/node
# 最后检查nodejs是否配置成功
node -v
```

### Git配置

```
# 方式一：直接通过yum命令安装
yum install git
# 问题：直接通过yum命令安装的git版本过低

# 方式二：手动编译安装
# github上全局搜索git，下载git版本，tar.gz
# 压缩包解压
tar -zxvf git-2.21.0.tar.gz
# 安装编译所需依赖，耐心等待安装，出现提示输入y即可
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker
# 安装依赖时，yum自动安装了Git，需要卸载旧版本Git
yum remove git
# 进入解压后的文件夹，然后执行编译
make prefix=/usr/local/git all
# 安装Git至/usr/local/git路径
make prefix=/usr/local/git install
# 打开环境变量配置文件
vim /etc/profile
# 在底部加上Git相关配置信息
export PATH=/usr/local/git/bin:$PATH
# 查看安装的git版本，校验通过，安装成功
git version
```

### Npm配置

```
# 设置淘宝镜像
npm config set registry https://registry.npm.taobao.org/
```

### Yarn配置

```
# 下载源码包 官网安装选择环境‘备选’
https://yarnpkg.com/latest.tar.gz
# 解压
tar zvxf latest.tar.gz
# 修改文件夹名称
mv yarn-v1.16.0 yarn 
# 配置环境变量，最后添加
vim ~/.bashrc
export PATH=/root/yarn/bin:$PATH
# 使配置生效
source ~/.bashrc

# 设置淘宝镜像
yarn config set registry https://registry.npm.taobao.org/
```

### pm2配置

```
# 解压文件
tar -xvf pm2.tar.gz
# 全局配置pm2
ln -s /root/node/lib/node_modules/pm2/bin/pm2 /usr/local/bin/pm2
# OR
# 或者直接全局安装
yarn add global pm2
npm install pm2 -g
```

## Nginx配置

```
# 下载nginx压缩包 注：MainLine version（最新版本）、Stable version（稳定版本）、Legacy version（历史版本）
# OR
# 也可以直接使用wget命令下载，指令如下：
wget -c https://nginx.org/download/nginx-1.10.1.tar.gz

# 配置nginx安装所需的环境
# 安装以下依赖
yum install gcc
yum install pcre-devel
yum install zlib zlib-devel
yum install openssl openssl-devel
# OR 一次性安装以上四个依赖
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel

# 注：在安装前可通过命令查看是否已用yum安装以上依赖
yum list installed

# 安装nginx
# 解压
tar -zxvf nginx-1.xx.x.tar.gz
# 进入解压后的文件目录
cd nginx-1.xx.x
# 使用默认配置
./configure
# 编译安装（如果编译出错，请检查前面的4个依赖安装是否有问题）
make install
# 检查nginx是否存在
cd /usr/local
ls

# 启动nginx
# 以下操作都是在/usr/local/nginx/sbin 中进行的
# nginx基本操作命令如下：
# 启动nginx
./nginx
# 关闭nginx
./nginx -s quit  OR  ./nginx -s stop
# 重启nginx
./nginx -s reload
# 查看nginx进程
ps aux|grep nginx
# 设置nginx开机启动，只需在rc.local增加启动代码即可
vim /etc/rc.local
# 在底部增加
/usr/local/nginx/sbin/nginx

# nginx配置文件修改
# 配置文件所在位置
cd /usr/local/nginx/conf 修改其中的nginx.conf
vim nginx.conf 打开文本编辑器
i进入编辑状态
头部 #user nobody 改为 user root
esc退出编辑模式
shift+：输入wq保存并退出

# nginx配置站点示例
# vue-router 为hash模式配置如下
http{
	server{
		listen 6660;
		server_name web_demo;
		location / {
			root /home/web/demo;
			index index.html index.htm;
		}
	}
}

# vue-router为history模式配置如下
http{
	server{
		listen 6660;
		server_name web_demo
		location / {
			root /home/web/demo;
			index index.html index.htm;
			try_files $uri $uri/ /index.html;
		}
	}
}

# 之后重新启动服务，通过浏览器访问 地址+端口 即可
# 具体内容参考：vue-router路由模式

# 如果启动nginx后，通过浏览器访问没有看到相应页面
# 查看nginx进程
ps aux|grep nginx
# 查看80端口占用情况
netstat -ntlp
# 第一步，对80端口进行防火墙配置：
firewall-cmd --zone=public --add-port=80/tcp --permanent
# 第二步，重启防火墙服务
systemctl restart firewalld.service
# 然后重新在浏览器中访问你的ip，应该就可以访问了
```

