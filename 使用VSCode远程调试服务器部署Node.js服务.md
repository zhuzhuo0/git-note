## 使用VSCode远程调试服务器部署Node.js服务

使用 Node.js 发布的服务，如果出了问题，除了查看日志之外，还可以使用 VSCode 进行远程调试。

服务端启动服务的时候，需要使用 inspect 参数指定远程调试端口。

```
node --inspect=10.10.10.15:9229 app.js
```

其中的 IP 地址需要使用具体的 IP 地址，如果使用127.0.0.1会导致连接不上。端口号需要使用 firewall-cmd 打开。

启动成功后：

```
[root@localhost pdf-server]# node --inspect=172.16.143.163:9292 app.js
Debugger listening on ws://172.16.143.163:9292/6e705f3b-481f-4907-93e6-3105753dbf6d
For help, see: https://nodejs.org/en/docs/inspector
server is running at port 3005
```

在本地新建一个空文件夹，并使用 VSCode 打开

在调试栏中配置 launch.json：

选择环境中使用 Node.js 环境：

默认配置是本地调试配置，使用右下角添加配置功能，添加附加到远程程序配置

添加远程配置之后，修改其address、port和remoteRoot配置：
address配置修改为远程服务器IP，port配置修改为远程服务器调试端口，remoteRoot配置修改为远程服务器代码所在目录绝对路径。name配置也可以自由修改。

```
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "attach",
            "name": "Attach to Remote",
            "address": "172.16.143.163",
            "port": 9292,
            "localRoot": "${workspaceFolder}",
            "remoteRoot": "/root/pdf-server/"
        }
    ]
}
```

将调试中的配置项，选择远程调试配置，并启动调试：

启动调试后，远程服务器会提示Debugger attached，证明连接成功。

然后需要调试哪个文件，往文件夹里按与服务器相同的目录结构扔文件即可。



原文参考：

[使用VSCode远程调试服务部署的nodejs服务]( http://www.baiguangnan.com/2019/03/13/vscoderemotedebugnodejs/) 

