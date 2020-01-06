## Linux 防火墙 [firewall、iptable]

Linux中有两种防火墙软件，ConterOS7.0以上使用的是firewall，ConterOS7.0以下使用的是iptables，本文将分别介绍两种防火墙软件的使用。

#### iptables防火墙

```
# 安装

# 由于CenterOS7.0以上版本并没有预装Iptables,我们需要自行安装
# 安装前先关闭firewall防火墙

# 安装iptables
yum install iptables

# 安装iptables-services
yum install iptables-services

# 使用

# 查看防火墙状态
service iptables status  

# 停止防火墙
service iptables stop  

# 启动防火墙
service iptables start  

# 重启防火墙
service iptables restart  

# 永久关闭防火墙
chkconfig iptables off  

# 永久关闭后重启
chkconfig iptables on　　

# 开启80端口
vim /etc/sysconfig/iptables
# 加入如下代码
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
保存退出后重启防火墙
service iptables restart

# 设置开机启动
systemctl enable iptables.service

# 禁用开机启动
systemctl disable iptables.service

# 查看filter表的几条链规则(INPUT链可以看出开放了哪些端口)
iptables -L -n
[图片](https://mmbiz.qpic.cn/mmbiz_png/CKvMdchsUwn5CKEUiaHMOL5KoaZ23xCzX0vKh2lNYGVYmvxFmNRppGtJ21qFb5ZNGvbmibSaalChuf3HNgicCjZUg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

# 查看NAT表的链规则
iptables -t nat -L -n
[图片](https://mmbiz.qpic.cn/mmbiz_png/CKvMdchsUwn5CKEUiaHMOL5KoaZ23xCzXrYjDYLuzj8dmYXqxAEuplkeulReF1lpP7TzXyxia1iaBFqjQ2OMqotrA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

# 清除防火墙所有规则
iptables -F
iptables -X
iptables -Z

# 给INPUT链添加规则（开放8080端口）
iptables -I INPUT -p tcp --dport 8080 -j ACCEPT

# 查找规则所在行号
iptables -L INPUT --line-numbers -n
[图片](https://mmbiz.qpic.cn/mmbiz_png/CKvMdchsUwn5CKEUiaHMOL5KoaZ23xCzXWoicbDeNStdn3ibCmhupKbXXnaiagxfOZUTKzpccvTdD4Skh2icjuh7GoQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

# 根据行号删除过滤规则（关闭8080端口）
iptables -D INPUT 1
[图片](https://mmbiz.qpic.cn/mmbiz_png/CKvMdchsUwn5CKEUiaHMOL5KoaZ23xCzXuBZQ8ibgMNKPjCpaibk9urLsDzBVYk39BrxE8Ek9auJI9h4R1jrW2Iibw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

```

firewall防火墙

```
1、查看firewall服务状态

systemctl status firewalld

出现Active: active (running)切高亮显示则表示是启动状态。

出现 Active: inactive (dead)灰色表示停止，看单词也行。
2、查看firewall的状态

firewall-cmd --state
3、开启、重启、关闭、firewalld.service服务

# 开启
service firewalld start

# 重启
service firewalld restart

# 关闭
service firewalld stop

4、查看防火墙规则

firewall-cmd --list-all
5、查询、开放、关闭端口

# 查询端口是否开放
firewall-cmd --query-port=8080/tcp

# 开放80端口
firewall-cmd --permanent --add-port=80/tcp

# 移除端口
firewall-cmd --permanent --remove-port=8080/tcp

# 重启防火墙(修改配置后要重启防火墙)
firewall-cmd --reload

# 设置开机启动
systemctl enable firewalld

# 禁用开机启动
systemctl disable firewalld

# 查看开放的端口
firewall-cmd --list-ports

# 关闭端口
firewall-cmd --zone=public --remove-port=8080/tcp --permanent

```

