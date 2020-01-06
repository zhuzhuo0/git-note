## Python 配置操作

#### 清屏：

1. 创建 CreateWindow.py文件，文件内容如下

```
"""

Clear Window Extension
Version: 0.2

Author: Roger D. Serwy
        roger.serwy@gmail.com

Date: 2009-06-14

It provides "Clear Shell Window" under "Options"
with ability to undo.

Add these lines to config-extensions.def

[ClearWindow]
enable=1
enable_editor=0
enable_shell=1
[ClearWindow_cfgBindings]
clear-window=<Control-Key-l>


"""

class ClearWindow:

    menudefs = [
        ('options', [None,
               ('Clear Shell Window', '<<clear-window>>'),
       ]),]
		 
    def __init__(self, editwin):
        self.editwin = editwin
        self.text = self.editwin.text
        self.text.bind("<<clear-window>>", self.clear_window2)

        self.text.bind("<<undo>>", self.undo_event)  # add="+" doesn't work

    def undo_event(self, event):
        text = self.text
        
        text.mark_set("iomark2", "iomark")
        text.mark_set("insert2", "insert")
        self.editwin.undo.undo_event(event)

        # fix iomark and insert
        text.mark_set("iomark", "iomark2")
        text.mark_set("insert", "insert2")
        text.mark_unset("iomark2")
        text.mark_unset("insert2")
        

    def clear_window2(self, event): # Alternative method
        # work around the ModifiedUndoDelegator
        text = self.text
        text.undo_block_start()
        text.mark_set("iomark2", "iomark")
        text.mark_set("iomark", 1.0)
        text.delete(1.0, "iomark2 linestart")
        text.mark_set("iomark", "iomark2")
        text.mark_unset("iomark2")
        text.undo_block_stop()
        if self.text.compare('insert', '<', 'iomark'):
            self.text.mark_set('insert', 'end-1c')
        self.editwin.set_line_and_column()

    def clear_window(self, event):
        # remove undo delegator
        undo = self.editwin.undo
        self.editwin.per.removefilter(undo)

        # clear the window, but preserve current command
        self.text.delete(1.0, "iomark linestart")
        if self.text.compare('insert', '<', 'iomark'):
            self.text.mark_set('insert', 'end-1c')
        self.editwin.set_line_and_column()
 
        # restore undo delegator
        self.editwin.per.insertfilter(undo)
 
```

2. MAC 版本10.13 多个版本Python位置：Homebrew、官网下载、系统自带三种位置

```
# Homebrew 安装python3.6的位置
/usr/local/Cellar/python3/3.x.0/Frameworks/Python.framework/Versions/3.x/lib/python3.x/idlelib
# Python官网下载安装python3.6的位置
/Library/Frameworks/Python.framework/Versions/3.x/lib/python3.x/idlelib
# 系统自带的Python2.7的位置
/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/idlelib

注：可通过命令行 open . 在finder中打开当前文件夹
```

3. 将CreateWindow.py 文件复制到文件夹内
4. 修改config-extensions.def文件，在文件末尾添加如下代码

```
[ClearWindow]
enable=1
enable_editor=0
enable_shell=1
[ClearWindow_cfgBindings]
clear-window=<Command-Key-l>
```

5. 完成后，重启idle，可通过commond+l实现清屏功能