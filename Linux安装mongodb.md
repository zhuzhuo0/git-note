## Linux安装mongodb

1. 下载mongodb，将下载的文件放到自定的目录中

   版本说明：

   development release 【开发版】  current release【当前稳定版本】 previous release【旧版本】

   系统说明：

   Ubuntu ----> 优般图 

   Linux legacy ----> 通用版

   window ----> 不解释

   RHEL Linux ----> 红帽

   Amazon Linux ----> 亚马逊

   Debian Linux ----> Debian

   SUSE Linux ----> SUSE

2. 解压

   ```
   tar -zxvf mongodb-linux-x86_64-4.0.10.tgz 
   # 为了方便以后操作，将文件夹名称简化
   mv mongodb-linux-x86_64-4.0.10 mongodb
   ```

3. 配置启动

   在mongoDB根目录下创建data目录及其子目录db，以及日志目录logs以及其日志文件mongoLogs.log，和配置文件mongodb.conf，命令如下

   ```
   [root@izbp1b498epn4trb75oykez mongoDB]# mkdir data
   [root@izbp1b498epn4trb75oykez mongoDB]# mkdir data/db
   [root@izbp1b498epn4trb75oykez mongoDB]# mkdir logs
   [root@izbp1b498epn4trb75oykez mongoDB]# touch logs/mongoLogs.log
   [root@izbp1b498epn4trb75oykez mongoDB]# touch mongodb.conf
   复制代码
   ```

   编辑配置文件mongodb.conf，命令如下

   ```
   [root@izbp1b498epn4trb75oykez mongoDB]# vi mongodb.conf 
   复制代码
   ```

   打开文件后，英文状态下按键盘i键进入文件编辑状态，文件内容如下

   ```
   #端口号 默认27017
   port=27017
   
   #数据目录
   dbpath = /root/mongodb/data/db
   
   #日志文件
   logpath = /root/mongodb/logs/mongoLogs.log
   
   #设置后台运行
   fork = true
   
   #日志输出方式
   logappend = true
   
   #开启校验用户
   auth = true
   
   ```

   编辑好之后按键盘ESC退出编辑状态，接着按shift+：键，然后输入wq，按回车保存退出

   启动mongodb服务

   ```
   # 进入mongodb文件夹的bin目录内
   cd /root/mongodb/bin
   # 以配置文件启动mongodb服务
   ./mongod --config /root/mongodb/mongodb.conf
   # 结果
   [root@localhost bin]# ./mongod --config /root/mongodb/mongodb.conf
   about to fork child process, waiting until server is ready for connections.
   forked process: 105682
   child process started successfully, parent exiting
   # 验证
   ./mongo
   show dbs
   ```

4. 关闭mongodb服务

   ```
   # 进入控制台
   ./mongo
   # 使用admin数据库
   use admin
   # 执行关闭服务命令
   db.shutdownServer()
   ```

5. 将命令添加至环境变量中

   ```
   # 查看环境配置文件
   cat ~/.bashrc
   # 添加mongodb配置
   vim ~/.bashrc
   exprt PATH=/root/mongodb/bin:$PATH
   # 执行生效
   source ~/.bashrc
   ```

   