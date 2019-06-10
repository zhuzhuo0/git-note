## Linux中某个端口拒绝远程主机连接原因及解决方法

问题描述： 比如在本机telent到192.168.8.170 主机的9000 端口，被拒绝。

原因：

1. 被防火墙拦截
2. 该端口的监听地址为本机(127.0.0.1)，如果这样的话，只有从本机发起的对端口的访问被允许，而外部主机访问被拒绝。

解决方法：

1. 如果防火墙过滤了访问该端口的请求则设置过滤规则，放行该端口或者关闭防火墙
2. 如果监听地址为本机地址则修改其监听地址为真是ip地址（如192.168.8.119）



判断是否是防火墙问题

```
telnet ip 端口号     例如：telnet 172.16.143 7300
```

如果运行telnet命令显示

```
-bash: telnet:command not found

# 解决办法
yum list telnet* 列出telnet相关的安装包
yum install telnet-server 安装telnet服务
yum install telnet.* 安装telnet客户端
```

Centos 7 默认使用firewall作为防火墙

##### 查看防火墙状态

首先查看防火墙是否开启，如未开启，需要先开启防火墙并作开机自启

```
systemctl status firewalld
```

开启防火墙并设置开机自启

```
systemctl start firewalld
systemctl enable firewalld
```

##### 开放端口

（1）如我们需要开启XShell连接时需要使用的22端口

```
firewall-cmd --zone=public --add-port=22/tcp --permanent
```


其中--permanent的作用是使设置永久生效，不加的话机器重启之后失效

（2）重新载入一下防火墙设置，使设置生效

```
firewall-cmd --reload
```

（3）可通过如下命令查看是否生效

```
firewall-cmd --zone=public --query-port=22/tcp
```

（4）如下命令可查看当前系统打开的所有端口

```
firewall-cmd --zone=public --list-ports
```

##### 限制端口

（1）比如我们现在需要关掉刚刚打开的22端口

```
firewall-cmd --zone=public --remove-port=22/tcp --permanent
```

（2）重新载入一下防火墙设置，使设置生效

```
firewall-cmd --reload
```

（3）再去查看系统所有开放的端口，已经看到没有22端口了

```
firewall-cmd --zone=public --list-ports
```

##### 批量开放或限制端口

（1）批量开放端口，如从100到500这之间的端口我们全部要打开

```
firewall-cmd --zone=public --add-port=100-500/tcp --permanent
```

（2）重新载入一下防火墙设置，使设置生效

```
firewall-cmd --reload
```

（3）查看系统所有开放的端口，可以看到从100到500的端口已被全部开放

```
firewall-cmd --zone=public --list-ports
```

（4）同理，批量限制端口为

```
firewall-cmd --zone=public --remove-port=100-500/tcp --permanent
firewall-cmd --reload
```

##### 限制IP地址访问

（1）比如限制IP为192.168.0.200的地址禁止访问80端口即禁止访问机器

```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0.200" port protocol="tcp" port="80" reject"
```

（2）重新载入一下防火墙设置，使设置生效

```
firewall-cmd --reload
```

（3）查看已经设置的规则

```
firewall-cmd --zone=public --list-rich-rules
```

##### 解除IP地址访问

（1）解除刚才被限制的192.168.0.200

```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0.200" port protocol="tcp" port="80" accept"
```

（2）重新载入一下防火墙设置，使设置生效

```
firewall-cmd --reload
```

（3）再查看规则设置发现已经没有192.168.0.200的限制了

```
firewall-cmd --zone=public --list-rich-rules
```

如设置未生效，可尝试直接编辑规则文件，删掉原来的设置规则，重新载入一下防火墙即可

```
vi /etc/firewalld/zones/public.xml
```

##### 限制IP地址段

（1）如我们需要限制10.0.0.0-10.0.0.255这一整个段的IP，禁止他们访问

```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.0.0.0/24" port protocol="tcp" port="80" reject"
```


其中10.0.0.0/24表示为从10.0.0.0这个IP开始，24代表子网掩码为255.255.255.0，共包含256个地址，即从0-255共256个IP，即正好限制了这一整段的IP地址，具体的设置规则可参考下表

![](/Users/zhuzhu/Desktop/20180522112329733.png)

（2）重新载入一下防火墙设置，使设置生效

```
firewall-cmd --reload
```

（3）查看规则，确认是否生效

```
firewall-cmd --zone=public --list-rich-rules
```

（4）同理，打开限制为

```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.0.0.0/24" port protocol="tcp" port="80" accept"

firewall-cmd --reload
```

