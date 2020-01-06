## Linux搭建ftp服务器

#### 安装

```
# 查看是否已安装 方法一
[root@localhost ~]# rpm -q vsftpd
vsftpd-3.0.2-21.el7.x86_64

# 查看是否已安装 方法二
[root@localhost ~]# vsftpd -v
vsftpd: version 3.0.2

# 安装 vsftpd
[root@localhost ~]# yum -y install vsftpd

# 卸载 vsftpd
yum -y remove vsftpd
```

#### 查看位置

```
[root@localhost ~]# whereis vsftpd
vsftpd: /usr/sbin/vsftpd /etc/vsftpd /usr/share/man/man8/vsftpd.8.gz
```

#### 启动vsftpd服务

```
systemctl start vsftpd.service
```

#### 关闭firewall和SELinux

```
setenforce 0   # 设置SELinux 成为permissive模式  （关闭SELinux）
setenforce 1   # 设置SELinux 成为enforcing模式   （开启SELinux）

# 或者修改配置
vi /etc/selinux/config
# SELINUX=enforcing
# 注释掉
# SELINUXTYPE=targeted
# 注释掉
SELINUX=disabled
# 增加
:wq! #保存退出
setenforce 0
```

或者设置SELinux

```
getsebool -a | grep ftp
setsebool -P ftpd_full_access on

systemctl stop firewalld.service
#停止firewall
systemctl disable firewalld.service
#禁止firewall开机启动
```

如果你不愿意关闭防火墙，需要防火墙添加FTP服务

```
firewall-cmd --permanent --zone=public --add-service=ftp
firewall-cmd --reload

# firewall扩展命令
# centos 7 firewall-cmd 查看端口是否开放以及开放端口
# 永久开放20/21端口 
firewall-cmd --permanent --zone=public --add-port=20/tcp
firewall-cmd --permanent --zone=public --add-port=21/tcp
# 开放多端口
firewall-cmd --zone=public --add-port=80-90/tcp --permanent
# 移除80端口
移除80端口号：firewall-cmd --permanent --zone=public --remove-port=80/tcp

--zone #作用域
--add-port=80/tcp #添加端口，格式为：端口/通讯协议
--permanent #永久生效，没有此参数重启后失效

# 查看防火墙所有信息
firewall-cmd --list-all

# 查询端口开启信息
firewall-cmd --list-ports

# 更新防火墙规则
firewall-cmd --reload

# 启动|关闭|重新启动 防火墙
systemctl [start|stop|restart] firewalld.service

# 查看开启服务 --其实一个服务对应一个端口，每个服务对应/usr/lib/firewalld/services下面一个xml文件。
firewall-cmd --list-services

# 查看还有哪些服务可以打开
firewall-cmd --get-services
```

#### 修改配置文件

```
# 配置文件 /etc/vsftpd/vsftpd.conf
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
chroot_local_user=NO
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
listen=YES
ftpd_banner=”welcome test Ftp Server.”
pam_service_name=vsftpd  #设置PAM使用的名称,该名称就是/etc/pam.d/目录下vsfptd文件的文件名
userlist_enable=YES
tcp_wrappers=YES
local_root=/home/www/test/upload/
virtual_use_local_privs=YES
guest_enable=YES   #表示是否开启vsftpd虚拟用户的功能，yes表示开启，no表示不开启。
guest_username=www
user_config_dir=/etc/vsftpd/vuser_conf  #指定每个虚拟用户账号配置目录
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=40080
pasv_promiscuous=YES
```

配置文件项说明

```
# 常用配置
anonymous_enable=NO        # 不允许匿名访问，禁用匿名登录
chroot_local_user=YES      # 启用限定用户在其主目录下
use_localtime=YES          # 使用本地时(自行添加)
chroot_list_enable=YES
local_enable=YES           # 允许使用本地帐户进行FTP用户登录验证
allow_writeable_chroot=YES # 如果启用了限定用户在其主目录下需要添加这个配置，解决报错 500 OOPS: vsftpd: refusing to run with writable root inside chroot()
xferlog_enable=YES         # 启用上传和下载的日志功能，默认开启。
local_umask=022            # 设置本地用户默认文件掩码022
# FTP上本地的文件权限，默认是077，不过vsftpd安装后的配置文件里默认是022

# 配置详情
# 匿名用户配置   
anonymous_enable=YES         # 是否允许匿名ftp,如否则选择NO   
anon_upload_enable=YES       # 匿名用户是否能上传   
anon_mkdir_write_enable=YES  # 匿名用户是否能创建目录   
anon_other_write_enable=YES  # 修改文件名和删除文件   
  
# 本地用户配置   
local_enable=YES # 是否允许本地用户登录   
local_umask=022  # umask 默认755   
write_enable=YES   
chroot_local_user=YES  # 本地用户禁锢在宿主目录中   
local_root=/home/test/ftp  #配置ftp根目录
chroot_list_enable=YES # 是否将系统用户限止在自己的home目录下   
chroot_list_file=/etc/vsftpd.chroot_list # 列出的是不chroot的用户的列表   
  
chown_upload=YES  # 是否改变上传文件的属主   
chown_username=username # 如果是需要输入一个系统用户名   
  
userlist_enable=YES   
userlist_deny=NO   
  
deny_email_enable=YES # 是否允许禁止匿名用户使用某些邮件地址   
banned_email_file=/etc/vsftpd.banned_emails # 禁止邮件地址的文件路径   
  
ftpd_banner=Welcome to chenlf FTP service. # 定制欢迎信息   
dirmessage_enable=YES # 是否显示目录说明文件, 需要收工创建.message文件   
message_file= # 设置访问一个目录时获得的目录信息文件的文件名,默认是.message   
  
xferlog_enable=YES # 是否记录ftp传输过程   
xferlog_file=/var/log/vsftpd.log # ftp传输日志的路径和名字   
xferlog_std_format=YES # 是否使用标准的ftp xferlog模式   
  
ascii_upload_enable=YES   # 是否使用ascii码方式上传文件   
ascii_download_enable=YES # 是否使用ascii码方式下载文件   
  
connect_from_port_20=YES # 是否确信端口传输来自20(ftp-data)   
  
nopriv_user=ftpsecure # 运行vsftpd需要的非特权系统用户默认是nobody   
  
async_abor_enable=YES # 是否允许运行特殊的ftp命令async ABOR.   
  
# FTP服务器的资源限制   
  
idle_session_timeout=600 # 设置session超时时间   
data_connection_timeout=120 # 设置数据传输超时时间   
  
max_clients=50 # 用户最大连接数 默认是0不限止   
max_per_ip=5   # 每个IP地址最大连接数   
  
anon_max_rate=102400  # 匿名的下载速度 KB   
local_max_rate=102400 # 普通用户的下载速度 KB   
--------------------- 
原文链接：https://blog.csdn.net/alger_magic/article/details/84451753
```

#### 本地用户登录

```
# 创建本地用户
# 新建系统用户www，用户目录为/home/www, 用户登录终端设为/bin/false(即使之不能登录系统)

# 方法一
# 创建用户 www 指定 `/home/www` 目录
useradd -g root -M -d /home/www -s /sbin/nologin www

# 设置用户 www 的密码
passwd www
# 把 /home/www 的所有权给www.root
chown -R www.root /home/www

# 方法二
useradd www -d /home/www -s /bin/false
chown www:www /home/www -R 

# 将 /home/www/test/upload 文件夹权限设为777
chmod -R 777 /home/www/test/upload

# 扩展
# 删除用户
userdel www
# 查看用户
cat /etc/passwd
```

#### 允许root账户登录ftp

```
产生这一现象的原因是vsftpd默认设置为禁止root账户登录，开启的方式如下：

1.编辑/etc/vsftpd/user_list和/etc/vsftpd/ftpusers两个设置文件脚本，将root账户前加上#号变为注释。（即让root账户从禁止登录的用户列表中排除）

2.重新开启vsftpd   service vsftpd reload

允许root账户访问ftp，可以远程访问centos系统中的任一文件，对于远程维护centos系统或下载文件十分方便。但从安全角度考虑，最好为临时性开启，平常还是把它关掉吧。
--------------------- 
原文链接：https://blog.csdn.net/zhanyongjia_cnu/article/details/50549127
```

#### 虚拟用户配置

```
# 创建虚拟用户配置目录
mkdir /etc/vsftpd/vuser_conf

# 创建虚拟用户文件，把这些用户名和密码存放在一个文件中。该文件内容格式是：用户名占用一行，密码占一行。
touch /etc/vsftpd/vuser_passwd
cat /etc/vsftpd/vuser_passwd
```

![屏幕快照 2019-08-09 00.06.38](/Users/zhuzhu/Desktop/屏幕快照 2019-08-09 00.06.38.png)

```
# 这个文件的虚拟用户和密码的文本文件无法被系统帐号直接调用，所以我们需要使用db_load命令生成db口令数据库文件，命令如下：
db_load -T -t hash -f /etc/vsftpd/vuser_passwd /etc/vsftpd/vuser_passwd.db

# 为了使服务器能够使用上述生成的数据库文件，对客户端进行身份验证，需要调用系统的PAM模块。PAM(Plugable Authentication Module)为可插拔认证模块，不必重新安装应用系统，通过修改指定的配置文件，调整对该程序的认证方式。PAM模块配置文件路径为/etc/pam.d/目录，此目录下保存着大量与认证有关的配置文件，并以服务名称命名
# 我们现在切换到/etc/pam.d/ 目录下，编辑vsfptd文件
```

![屏幕快照 2019-08-09 00.09.09](/Users/zhuzhu/Desktop/屏幕快照 2019-08-09 00.09.09.png)

```
auth required /lib64/security/pam_userdb.so db=/etc/vsftpd/vuser_passwd
account required /lib64/security/pam_userdb.so db=/etc/vsftpd/vuser_passwd

# auth是指对用户的用户名口令进行验证。
# accout是指对用户的帐户有哪些权限哪些限制进行验证。
# 再后面的/lib64/security/pam_userdb.so表示该条审核将调用pam_userdb.so这个库函数进行。
# 注意该函数会根据系统的位数而所在位置不同。
# 如果是32bit系统，该文件所在位置是/lib/security/pam_userdb.so
# 如果是64bit系统，该文件所在位置是/lib64/security/pam_userdb.so
# 最后db=/etc/vsftpd/login则指定了验证库函数将到这个指定的数据库中调用数据进行验证。其实该文件指的是/etc/vsftpd/login.db文件。
# 注意：db=/etc/vsftpd/login格式是这样的，去掉.db后缀。

# 配置虚拟用户与系统用户对应的文件，切换到 /etc/vsftpd/vuser_conf 目录下，创建test文件，注意文件名称要与vuser_passwd中的虚拟用户要对应
touch test
# test文件内容如下
local_root=/home/www/test/upload/  #表示使用本地用户登录到ftp时的默认目录
write_enable=YES  #虚拟用户和本地用户有相同的权限
virtual_use_local_privs=YES  #表示的是设置FTP对应的系统用户为www
guest_username=www
write_enable=YES
anon_umask=022

# 以上配置完毕后，启动vsftpd服务完成。
systemctl restart vsftpd.service

# 扩展
# 关于chroot：
    1. chroot_local_user
    2. chroot_list_enable
    3. chroot_list_file
# 可以通过如下两种方法来设置chroot，从而杜绝上述不安全的情况发生：

（1）设置所有的本地用户执行chroot，只要将/etc/vsftpd/vsftpd.con文件中的chroot_local_ user值置为YES，即             chroot_local_user=YES。

（2）设置指定的用户执行chroot，按照如下方法进行设置：
    chroot_local_user=NO
    chroot_list_enable=YES
    chroot_list_file=/etc/vsftpd.chroot_list
# 设置后，只有/etc/vsftpd.chroot_list文件中指定的用户才能够执行chroot命令。

--------------------- 
原文链接：http://www.myjishu.com/?p=369
```



#### 问题汇总

##### 500 OOPS: could not read chroot() list file:/etc/vsftpd/chroot_list

```
# 500 OOPS: could not read chroot() list file:/etc/vsftpd/chroot_list
# 出错原因：用户没有变更根目录的权限。
# 解决办法：
# 第一步：
# 打开 /etc/vsftpd/vsftpd.conf，作如下配置
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/chroot_list
# 第二步：
在/etc/vsftpd下touch chroot_list新建chroot_list,加入登陆用户的名字
直接添加即可
# 第三步
重启服务 systemctl restart vsftpd.service

# vsftpd 虚拟用户 530 Login incorrect.
--------------------- 
原文链接：http://blog.sina.com.cn/s/blog_4cd978f90102y0ng.html
```

##### vsftpd虚拟用户登录时 530 Login incorrect排错

```
很多朋友反映可能是selinux或者防火墙iptables，但是本机已经确认关闭了。也有朋友是说可能32位和64位的PAM 设置有差异但是自己是32位，设置没有错误，

32位设置如下：

auth    sufficient      /lib/security/pam_userdb.so    db=/etc/vsftpd/virtusers
account sufficient      /lib/security/pam_userdb.so    db=/etc/vsftpd/virtusers    

64位设置如下：

auth    sufficient      /lib64/security/pam_userdb.so    db=/etc/vsftpd/virtusers
account sufficient      /lib64/security/pam_userdb.so    db=/etc/vsftpd/virtusers

最后自己查看相关日志文件cat  /var/log/secure发现具体错误信息如下：

error retrieving information about user lXXX

很明显是 /lib/security/pam_userdb.so这个载入失败，后来突然想起来pam原配置文件被我修改成如下：

cat /etc/pam.d/vsftpd
#%PAM-1.0

session    optional    pam_keyinit.so    force revoke
auth      required pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
auth      required pam_shells.so
auth      include system-auth
account    include system-auth
session    include system-auth
session    required    pam_loginuid.so


auth    sufficient      /lib/security/pam_userdb.so    db=/etc/vsftpd/virtusers
account sufficient      /lib/security/pam_userdb.so    db=/etc/vsftpd/virtusers

于是重新调换位置，放在配置文件首，重启生效，正常登录

cat /etc/pam.d/vsftpd
#%PAM-1.0
auth    sufficient      /lib/security/pam_userdb.so    db=/etc/vsftpd/virtusers
account sufficient      /lib/security/pam_userdb.so    db=/etc/vsftpd/virtusers

session    optional    pam_keyinit.so    force revoke
auth      required pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
auth      required pam_shells.so
auth      include system-auth
account    include system-auth
session    include system-auth
session    required    pam_loginuid.so

# 附录
其他常见错误
（1）500错误
500 OOPS: vsftpd: refusing to run with writable root inside chroot ()
这种一般是因为用户的根目录可写，同时又使用了chroot限制，但这在这个版本的vsftp中默认是不被允许的。要修复这个错误，可以用命令chmod a-w /home/ftp 去除用户根目录的写权限，注意把目录替换成你自己的。
同时，在根目录下创建可以有写权限的文件夹，这样就可以进入该文件夹中，上传东西了。
或者显式指定根目录可以写，在vsftpd的配置文件中增加下列两项中的一项：对于标准的vsftpd build (vsftpd):
allow_writeable_chroot=YES
对于扩展的vsftpd build (vsftpd-ext):
allow_writable_chroot=YES
（2）550错误
550 Create directory operation failed.
这种一般是权限问题，不能创建目录或文件，重新修改下目录权限即可，同时注意是不是文件拥有者是不是ftp用户/宿主。

--------------------- 
原文链接：https://blog.csdn.net/weixin_33748818/article/details/92872808
```

##### vsftp提示500 OOPS: bad bool value in config file for: write_enable

```
# 仔细检查 /etc/vsftpd/vsftpd.conf 中的每一个配置项，是否有多余的空格
```

##### 550 Create directory operation failed.

```
# 这种一般是权限问题，不能创建目录或文件，重新修改下目录权限即可，同时注意是不是文件拥有者是不是ftp用户/宿主。
```

##### 创建用户目录

```
每个用户目录文件夹是有root用户创建的，也就是上面local_root配置目录，其权限应设置为755。因为权限的问题在该文件夹内无法直接上传文件。而如果设置为777则无法访问，这是由于vsftpd的安全性设置。解决上传问题的方法是在local_root文件夹内新建一个upload的文件夹，权限设置为777，可将文件上传到该文件夹。

mkdir -p /home/vsftpd/hss     # 每个用户对于一个目录，创建两个目录“hss”、“wcj”

# 下面是目录结构
/home/vsftpd
      ├── hss
      │   ├── filename.md
      │   └── upload
      └── wcj
          └── filename.md


# 赋予其权限
chmod -R 777 /var/vsftpd/hss/upload/

# 在/var/ftp下新建一个目录来实现匿名用户上传
mkdir /var/ftp/upload
```



#### 扩展

##### ftp各种用户之间的区别

```
# 本地用户：用户在FTP服务器拥有账号，且该账号为本地用户的账号，可以通过自己的账号和口令进行授权登录，登录目录为自己的home目录$HOME 

# 虚拟用户：用户在FTP服务器上拥有账号，但该账号只能用于文件传输服务。登录目录为某一特定的目录，通常可以上传和下载 

# 匿名用户：用户在FTP服务器上没有账号，登录目录为/var/ftp
```

##### 服务运维

```
systemctl restart vsftpd.service  # 重启服务
systemctl start vsftpd.service    # 启动服务
systemctl status vsftpd.service   # 服务状态查看
```

##### FTP命令

```
# 安装ftp
yum -y install ftp
# 连接主机
ftp 172.16.143.163
# 扩展命令
ftp> ascii  # 设定以ASCII方式传送文件(缺省值) 
ftp> bell   # 每完成一次文件传送,报警提示. 
ftp> binary # 设定以二进制方式传送文件. 
ftp> bye    # 终止主机FTP进程,并退出FTP管理方式. 
ftp> case # 当为ON时,用MGET命令拷贝的文件名到本地机器中,全部转换为小写字母. 
ftp> cd     # 同UNIX的CD命令. 
ftp> cdup   # 返回上一级目录. 
ftp> chmod  # 改变远端主机的文件权限. 
ftp> close  # 终止远端的FTP进程,返回到FTP命令状态, 所有的宏定义都被删除. 
ftp> delete # 删除远端主机中的文件. 
ftp> dir [remote-directory] [local-file] # 列出当前远端主机目录中的文件.如果有本地文件,就将结果写至本地文件. 
ftp> get [remote-file] [local-file] # 从远端主机中传送至本地主机中. 
ftp> help [command] # 输出命令的解释. 
ftp> lcd # 改变当前本地主机的工作目录,如果缺省,就转到当前用户的HOME目录. 
ftp> ls [remote-directory] [local-file] # 同DIR. 
ftp> macdef                 # 定义宏命令. 
ftp> mdelete [remote-files] # 删除一批文件. 
ftp> mget [remote-files]    # 从远端主机接收一批文件至本地主机. 
ftp> mkdir directory-name   # 在远端主机中建立目录. 
ftp> mput local-files # 将本地主机中一批文件传送至远端主机. 
ftp> open host [port] # 重新建立一个新的连接. 
ftp> prompt           # 交互提示模式. 
ftp> put local-file [remote-file] # 将本地一个文件传送至远端主机中. 
ftp> pwd  # 列出当前远端主机目录. 
ftp> quit # 同BYE. 
ftp> recv remote-file [local-file] # 同GET. 
ftp> rename [from] [to]     # 改变远端主机中的文件名. 
ftp> rmdir directory-name   # 删除远端主机中的目录. 
ftp> send local-file [remote-file] # 同PUT. 
ftp> status   # 显示当前FTP的状态. 
ftp> system   # 显示远端主机系统类型. 
ftp> user user-name [password] [account] # 重新以别的用户名登录远端主机. 
ftp> ? [command] # 同HELP. [command]指定需要帮助的命令名称。如果没有指定 command，ftp 将显示全部命令的列表。
ftp> ! # 从 ftp 子系统退出到外壳。 

# 常用命令
# 关闭ftp
bye
exit
quit

# 下载文件
ftp> get readme.txt # 下载 readme.txt 文件
ftp> mget *.txt     # 下载 

# 上传文件
ftp> put /path/readme.txt # 上传 readme.txt 文件
ftp> mput *.txt           # 可以上传多个文件
```

##### 状态码

```
230 - 登录成功

200 - 命令执行成功

150 - 文件状态正常，开启数据连接端口

250 - 目录切换操作完成

226 - 关闭数据连接端口，请求的文件操作成功
```

##### vim操作

```
全选（高亮显示）：按esc后，然后ggvG或者ggVG
全部复制：按esc后，然后ggyG
全部删除：按esc后，然后dG

解析：
gg：是让光标移到首行，在vim才有效，vi中无效 
v ： 是进入Visual(可视）模式 
G ：光标移到最后一行 

选中内容以后就可以其他的操作了，比如： 
d  删除选中内容 
y  复制选中内容到0号寄存器 
"+y  复制选中内容到＋寄存器，也就是系统的剪贴板，供其他程序用 
--------------------- 
原文地址：https://www.cnblogs.com/webStyle/p/4354464.html
```



#### 参考资料：

```
CentOS7安装配置vsftp搭建FTP
https://segmentfault.com/a/1190000008161400#articleHeader15
--------------------- 
[经验分享] CentOS7搭建vsftpd服务（本地用户篇）
http://bbs.qcloud.com/thread-48481-1-3.html
--------------------- 
vsftpd高级用法之虚拟用户配置
http://www.myjishu.com/?p=369
--------------------- 
CentOS7 FTP安装和配置
https://www.cnblogs.com/leoxuan/p/8329998.html
```

