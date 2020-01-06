## Centos7安装中文字体

在CentOS 4.x开始用fontconfig来安装字体库

```
# 安装fontconfig
yum -y install fontconfig
# 查看已安装的依赖包
yum list installed
```

在CentOS中，字体库的存放位置正是上图中看到的fonts目录，所以我们首先要做的就是找到中文字体文件放到该目录下，而中文字体文件在我们的windows系统中就可以找到，打开c盘下Windows/Fonts目录

我们只需要将我们需要的字体拷贝出来并上传至linux服务器即可，在这之前我们还需要新建目录，首先在/usr/share/fonts目录下新建一个目录chinese

```
# 新建chinese目录
mkdir -p /usr/share/fonts/chinese
# 修改chinese目录的权限
chmod -R 755 /usr/share/fonts/chinese
```

接下来需要安装ttmkfdir来搜索目录中所有的字体信息，并汇总生成fonts.scale文件

```
# 安装ttmkfdir
yum -y install ttmkfdir
```

然后执行ttmkfdir命令即可：

```
# ttmkfdir -e /usr/share/X11/fonts/encodings/encodings.dir
```

最后一步就是修改字体配置文件了，首先通过编辑器打开配置文件：

```
vim /etc/fonts/fonts.conf
```

可以看到一个Font list，即字体列表，

在这里需要把我们添加的中文字体位置加进去：

```
<dir>/usr/share/fonts/chinese</dir>
```

![屏幕快照 2019-08-14 14.07.59](/Users/zhuzhu/Desktop/屏幕快照 2019-08-14 14.07.59.png)

然后输入:wq保存退出，

最后别忘了刷新内存中的字体缓存，这样就不用reboot重启了：

```
# fc-cache
```

这样所有的步骤就算完成了，最后再次通过 fc-list 看一下字体列表。

可以看到已经成功安装上了中文字体，至此安装过程就全部结束

------------------------------------------------------------------------------------------------------------------------

参考文档：

Centos7安装中文字体

https://my.oschina.net/u/2270256/blog/2874383