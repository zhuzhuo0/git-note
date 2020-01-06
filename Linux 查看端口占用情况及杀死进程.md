## Linux 查看端口占用情况及杀死进程

查看端口占用情况

```
sudo lsof -i tcp:3000
```

根据pid杀进程

```
sudo kill -9 进程pid
```

