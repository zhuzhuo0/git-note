# Linux 下安装 Redis 

1. 下载redis源码包

2. 解压redis源码包

   ```
   tar -zxvf redis-5.0.5.tar.gz
   ```

3. 进入redis文件内，使用`make`命令编译源码

   可能出现的错误：`fatal error: jemalloc/jemalloc.h: No such file or directory`

   在redis的README.md文件中，可以找到解决方法，手动设置MALLOC环境

   使用 `make MALLOC=libc` 命令代替 `make` 命令。

4. 编译好 Redis 之后，可以使用 `make test` 命令测试一下

   可能出现提示 `You need tcl 8.5 or newer in order to run the Redis test` ，这是缺少 tcl 包，安装一下 tcl 就好了（如 `yum install tcl`）

5. 测试完成，就可以安装 Redis 了，

   先 cd 到 Redis 解压文件的 src 目录 

   `cd redis/src`

   使用 `make PREFIX=/usr/local/redis install` 安装，可以设置 Redis 的安装位置

6. 配置redis

   安装完 Redis，可以看到 Redis 安装目录下只有一个 bin 目录，目录内容如下：

   - redis-server —— Redis 的服务器
   - redis-cli —— Redis 的命令行客户端
   - redis-benchmark —— Redis 的性能测试工具
   - redis-check-rdb —— Redis 的 RDB 文件检索工具
   - redis-check-aof —— Redis 的 AOF 文件修复工具
   - redis-sentinel —— Redis 的集群监控工具

   #### 前台启动

   进入`cd /usr/loca/redis/bin` 文件夹内

   执行`./redis-server` 即可启动

   使用 `redis-server` 命令就可以启动 Redis 服务器了，但是默认是前台启动方式，也就是 Redis 会占用当前的终端窗口。

   可以通过`./redis-cli`来连接redis，并使用

   #### 后台启动

   后台启动方式，需要指定配置文件

   在`cd /usr/local/redis` 内新建文件夹 `mkdir path` 拷贝 redis 解压文件中的 redis.conf 文件到 Redis 的安装目录（方便查找）

   `cp 目标文件 保存位置` 例如：`cp /root/redis/redis.conf /usr/local/redis/path/`

   修改拷贝后的 redis.conf 文件中的内容，将 `daemonize no` 改为 `daemonize yes`，也即是将 Redis 服务器作以守护进程方式运行。

   在启动时，使用 `redis-server /usr/loca/redis/path/redis.conf` 命令启动，path 为你指定的配置文件目录，这样 Redis 就不会占用终端窗口了。

   使用查看进程命令，看 Redis 是否真正启动成功，`ps -aux | grep redis`。

   使用 `redis-cli` 命令启动 Redis 的命令行窗口，`ping` 命令来测试是否可以连接 Redis 服务器。

   可将redis/bin目录写入配置文件

   ```
   vi ~/.bashrc
   # 最后添加一句
   export PATH=/usr/local/redis/bin:$PATH
   # 执行生效
   source ~/.bashrc
   ```

   #### 关闭redis服务

   `./redis-cli shutdown`

   

   **核心配置**

   - `bind 127.0.0.1`

   绑定的IP地址，默认只允许本机访问 Redis，也即是 127.0.0.1（localhost），如果其他IP也想访问，可以将 `bind 127.0.0.1` 改为 `bind 指定的IP地址`，IP 地址设置成 0.0.0.0 表示允许任何IP访问，但这样做不安全！！！

   - `port 6379`

   端口号，默认端口 6379，可以修改为其他端口。

   - `daemonize no`

   是否以守护进程方式运行，默认是前台启动方式，可以修改为后台启动方式。

   - `dbfilename dump.rdb`

   数据文件，默认数名称为 dump.rdb。

   - `dir ./`

   数据文件存储的位置，默认和配置文件在同一目录下。

   - `logfile ""`

   日志记录的位置，默认是直接输出到控制台中。

   - `database 16`

   数据库个数，默认是 16 个。

   
   
   #### Redis SHUTDOWN 出错问题解决
   
   启动redis之后，在停掉的时候报错，如下：
   
   ```
   c80k2@ubuntu:/usr/local/redis$ bin/redis-cli shutdown
   (error) ERR Errors trying to SHUTDOWN. Check logs.
   ```
   
   需要查看日志。那日志在哪里呢？进配置文件 redis.conf 查看：
   
   ```
   # Specify the log file name. Also the empty string can be used to force
   # Redis to log on the standard output. Note that if you use standard
   # output for logging but daemonize, logs will be sent to /dev/null
   logfile ""
   ```
   
   为了能更方便地查看日志，我们更改一下配置
   
   ```
   logfile "/usr/local/redis/log/redis.log
   ```
   
   保存退出后，创建对应的日志文件，并给上读写执行权限，这里直接给了0777
   
   ```
   注：此时可以通过kill -9 [redis对应的进程id]
   ```
   
   重启Redis，让配置生效，再次尝试关闭Redis
   
   ```
   ./redis-cli shutdown
   ```
   
   仍报错
   
   ```
   (error) ERR Errors trying to SHUTDOWN. Check logs.
   ```
   
   进入刚刚新建的日志文件查看日志
   
   ```
   42661:M 30 May 10:45:15.477 # User requested shutdown...
   42661:M 30 May 10:45:15.478 * Saving the final RDB snapshot before exiting.
   42661:M 30 May 10:45:15.478 # Failed opening the RDB file dump.rdb (in server root dir /usr/local/redis) for saving: Permission denied
   42661:M 30 May 10:45:15.478 # Error trying to save the DB, can't exit.
   ```
   
   看到是因为保存RDB snapshot快照的时候，由于权限问题，打开失败，进而保存失败，退出失败
   
   进配置文件看看
   
   ```
   # The working directory.
   #
   # The DB will be written inside this directory, with the filename specified
   # above using the 'dbfilename' configuration directive.
   #
   # The Append Only File will also be created inside this directory.
   #
   # Note that you must specify a directory here, not a file name.
   dir ./
   ```
   
   工作目录： 数据库DB将被写入这个目录，使用上面的 'dbfilename' 配置指令的文件名。AOF将同样再这个目录下被创建。注意你必须指明一个目录，而不是文件名
   
   对应的dbfilename配置如下，也就是说redis的DB数据将被写入这个文件
   
   ```
   # The filename where to dump the DB
   dbfilename dump.rdb
   ```
   
   可在执行redis-server的同级目录查找到 `dump.rdb` 文件。如果没有找到请沿着目录去上级目录查找
   
   找到 `dump.rdb` 文件之后可查看到是因为当前帐号的权限不错造成的，
   
   指定db文件坐在目录
   
   ```
   dir /usr/local/redis/db [自定义的数据库放置文件夹]
   ```
   
   然后将存放数据文件的文件夹赋予权限
   
   ```
   sudo chmod -R 777 /usr/local/redis/db
   ```
   
   重新启动，让配置生效，再次尝试关闭，没有报错
   
   ```
   c80k2@ubuntu:/usr/local/redis$ bin/redis-server redis.conf
   c80k2@ubuntu:/usr/local/redis$ bin/redis-cli SHUTDOWN
   ```
   
   日志
   
   ```
   
   ```
   
   