## Centos7安装wkhtmltopdf

#### 下载安装包

```
# 地址：https://wkhtmltopdf.org/downloads.html
# 下载 linux CentOS 7 x86_64
```

#### 安装

```
rpm -i 需要安装的包文件
rpm -iv 需要安装的包文件(显示安装详情)
rpm -ivh 需要安装的包文件(显示安装详情及进度)
```

#### 安装依赖（安装过程中可能会报的问题）

```
# 例如：缺少 xorg-x11-fonts-75dpi is needed by wkhtmltox-1:0.12.2-1.x86_64
# 操作方式：
1. 查找
yum search 75dpi
2. 找到目标依赖包安装
yum install xorg-x11-fonts-75dpi.noarch
3. 再次执行安装命令
```

#### 测试

```
# 查看安装位置
whereis wkhtmltopdf
# 测试
wkhtmltopdf http://www.baidu.com ./baidu.pdf
```

#### 常见问题

```
1. 导出的pdf中中文显示空白或者乱码方框
原因：缺少中文字体
解决办法：
	下载宋体（simsun.ttc）文件
	上传到服务器字体包内，详见centos安装中文字体文档
	
2. 命令没找到
解决办法：
	配置.bashrc 文件中的PATH
```

