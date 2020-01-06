## Yarn常见操作

### 查看配置

```
yarn config list
```

### 设置淘宝镜像

```
yarn config registry https://registry.npm.taobao.org
```

### 查看全局安装的依赖

```
yarn global list
```

### 查看全局安装依赖的位置

```
yarn global dir
```

### 获取global bin 的路径

```
yarn global bin
C:\Users\Administrator\AppData\Local\Yarn\bin

# mac 系统:
$ sudo vi ~/.bash_profile
# 在 .bash_profile 中添加下面一行：
export PATH="$PATH:`yarn global bin`"
```

