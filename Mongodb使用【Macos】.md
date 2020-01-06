## Mongodb使用【Macos】

#### mongodb启动参数详解

| mongodb参数 | 参数说明                                       |
| ----------- | ---------------------------------------------- |
| --dbpath    | 数据存放路径                                   |
| --logpath   | 日志文件路径                                   |
| --logappend | 日志输出方式->以追加模式进行记录，默认覆盖记录 |
| --port      | 启用端口号                                     |
| --fork      | 以后台守护进程的方式启动                       |
| --auth      | 是否需要验证权限登录（用户名和密码）           |
| --bind_ip   | 限制访问的ip                                   |

#### 开启权限控制

mongodb安装好后第一次进入是不需要密码的，也没有任何用户

关闭auth认证启动服务 `mongod --config /users/zhuzhu/mongodb/etc/mongodb.conf`

再开启另一个终端 `mongo --port 27017`

现在创建一个帐号，该账号需要有grant权限，即：帐号管理的权限 注意：帐号是跟着库走的，所以在指定库里授权，必须也在指定库里验证

```
use admin
db.createUser(
  {
    user: "myUserAdmin",
    pwd: passwordPrompt(), // or cleartext password
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
  }
)

# passwordPrompt()
# 该方法在4.2之后的命令行内起作用，通过命令行输入的方式，输入密码，低于4.2明文写密码

# userAdminAnyDatabase
# 权限说明：在除local和config之外的所有数据库上提供与userAdmin相同的用户管理操作访问权限。
```

#### 重启mongodb实例，打开权限控制

1. 退出 `use admin` 执行 `db.adminCommand({shutdown:1})`
2. 修改配置文件中的auth，打开权限控制
3. 打开 `mongod --config /users/zhuzhu/mongodb/etc/mongodb.conf`
4. 打开新控制台 `mongo --port 27017  --authenticationDatabase "admin" -u "myUserAdmin" -p`
5. 输入密码后登录

另一种方式

1. 直接进入`mongo --port 27017`
2. 进入到相应的数据库 `use admin` 
3. 进行权限验证 `db.auth(用户名，密码)`

#### 创建用于开发的权限帐号

执行以下命令

```
use test
db.createUser(
  {
    user: "myTester",
    pwd:  passwordPrompt(),   // or cleartext password
    roles: [ { role: "readWrite", db: "test" },
             { role: "read", db: "reporting" } ]
  }
)
```

然后退出，再次进入重复上面登录

注：too many users are authenticated 当报这个错误说明验证的用户过多，需要`ctrl+c` 退出后重新验证

#### 权限说明：

```
数据库用户类

read
非系统集合有查询权限

readWrite
非系统集合有查询和修改权限

数据库管理类

dbAdmin
数据库管理相关，比如索引管理，schema管理，统计收集等，不包括用户和角色管理

dbOwner
提供数据库管理，读写权限，用户和角色管理相关功能

userAdmin
提供数据库用户和角色管理相关功能

集群管理类

clusterAdmin
提供最大集群管理权限

clusterManager
提供集群管理和监控权限

clusterMonitor
提供对监控工具只读权限

hostManager
提供监控和管理severs权限

备份和恢复类

backup
提供数据库备份权限
restore
提供数据恢复权限

All-Database类

readAnyDatabase
提供读取所有数据库的权限除了local和config数据库之外

readWriteAnyDatabase
和readAnyDatabase一样，除了增加了写权限

userAdminAnyDatabase
管理用户所有数据库权限，单个数据库权限和userAdmin角色一样

dbAdminAnyDatabase
提供所有用户管理权限，除了local,config


超级用户类

root
数据库所有权限

内部角色

__system
提供数据库所有对象任何操作的权限，不能分配给用户，非常危险
```

