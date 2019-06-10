### 执行brew install命令长时间卡在Updating Homebrew的解决办法

#### 临时处理方式

按住control+c取消本次更新操作，brew跳过更新过程

#### 推荐处理方式

使用Alibaba的Homebrew镜像源进行加速

平时我们执行brew命令安装软件的时候，跟一下3个仓库地址有关

1. brew.git
2. homebrew-core.git
3. homebrew-bottles

通过以下操作将这3个仓库地址全部替换为Alibaba提供的地址

##### 替换/还原brew.git仓库地址

```
# 替换成阿里巴巴的brew.git仓库地址：
cd "$(brew --repo)"
git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
# ======================================== #
# 还原为官方提供的brew.git仓库地址
cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git
```

##### 替换/还原homebrew-core.git仓库

```
# 替换成阿里巴巴的homebrew-core.git 仓库地址：
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git
# ======================================== #
# 还原为官方提供的homebrew-core.git 仓库地址：
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core.git
```

##### 替换/还原homebrew-bottles访问地址

这个步骤跟你的macOS系统使用的shell版本有关系

首先查看当前使用的shell版本

```
echo $SHELL

# 如果你的输出结果是 /bin/zsh 参考zsh终端操作方式
# 如果你的输出结果是 /bin/bash 参考bash终端操作方式
```

###### zsh终端操作方式

```
# 替换成阿里巴巴的homebrew-bottles访问地址
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
# ======================================== #
# 还原为官方提供的homebrew-bottles访问地址
vi ~/.zshrc
# 然后，删除 HOMEBREW_BOTTLE_DOMAIN 这一行配置
source ~/.zshrc
```

###### bash终端操作方式

```
# 替换homebrew-bottles访问URL
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
# ======================================== #
# 还原为官方提供的homebrew-bottles访问地址
vi ~/.bash_profile
# 然后，删除 HOMEBREW_BOTTLE_DOMAIN 这一行配置
source ~/.bash_profile
```

