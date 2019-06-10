## Git配置【用户名和邮箱，SSH密钥】

运行命令配置用户名和邮箱

```
git config --global user.name "zhuzhuo0"
git config --global user.email "422914219@qq.com"
```

##### git使用ssh密钥

git支持https和git两种传输协议，github分享链接时会有两种协议可选

git使用https协议，每次pull, push都会提示要输入密码，使用git协议，然后使用ssh密钥，这样免去每次都输密码的麻烦

SSH 公钥默认储存在账户的主目录下的 ~/.ssh 目录

```
cd ~/.ssh
ls
```

看一下有没有id_rsa和id_rsa.pub(或者是id_dsa和id_dsa.pub之类成对的文件)，有 .pub 后缀的文件就是公钥，另一个文件则是密钥。

假如没有这些文件，甚至连 .ssh 目录都没有，可以用 ssh-keygen 来创建。该程序在 Linux/Mac 系统上由 SSH 包提供，而在 Windows 上则包含在 MSysGit 包里：

```
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
> Enter a file in which to save the key (/home/you/.ssh/id_rsa): [Press enter]
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]

# 一路回车，最后显示如下
Your public key has been saved in /home/you/.ssh/id_rsa.pub.
The key fingerprint is: # 01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your_email@youremail.com
```

到此为止，你本地的密钥对就生成了。

##### 添加公钥到你的远程仓库（github）

```
# 查看你生成的公钥
$ cat ~/.ssh/id_rsa.pub
# 复制内容
# 登陆你的github帐户。点击你的头像，然后 Settings -> 左栏点击 SSH and GPG keys -> 点击 New SSH key
# 然后你复制上面的公钥内容，粘贴进“Key”文本域内。 title域，自己随便起个名字。
# 点击 Add key。

# 验证
$ ssh -T git@github.com
Attempts to ssh to github

# 显示
Hi xxx! You've successfully authenticated, but GitHub does not # provide shell access.
```

##### 修改git的remote url

使用命令 git remote -v 查看你当前的 remote url

```
$ git remote -v
origin https://github.com/someaccount/someproject.git (fetch)
origin https://github.com/someaccount/someproject.git (push)
```

如果是以上的结果那么说明此项目是使用https协议进行访问的（如果地址是git开头则表示是git协议）

你可以登陆你的github，就像本文开头的图例，你在上面可以看到你的ssh协议相应的url，类似：

复制此ssh链接，然后使用命令 git remote set-url 来调整你的url。

```
git remote set-url origin git@github.com:someaccount/someproject.git
```

然后你可以再用命令 git remote -v 查看一下，url是否已经变成了ssh地址。