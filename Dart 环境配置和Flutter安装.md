## Dart 环境配置和Flutter安装

### Dart环境配置

[Install homebrew](http://brew.sh/) 然后安装dart

```
brew tap dart-lang/dart
brew install dart
```

更新dart

```
brew upgrade dart
```

删除dart

```
brew unlink dart
```

vscode 代码提示和插件安装

搜索dart插件和code runner插件，并安装

### Flutter安装

1. 安装xcode

2. flutter官网下载sdk，解压

3. 将flutter安装目录的bin目录配置到环境变量，然后把flutter国内镜像也配置到环境变量里面

   ```
   # 编辑配置文件
   vim ~/.bash_profile
   # 添加内容
   export PATH=/Users/zhuzhu/flutter_sdk/flutter/bin:$PATH
   export PUB_HOSTED_URL=https://pub.flutter-io.cn
   export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
   # 环境变量生效
   source ~/.bash_profile
   ```

4. 运行flutter doctor 查看还缺少哪些依赖

5. ```
   # flutter doctor 检测还需要安装的依赖项
   flutter doctor
   # 缺少的依赖项（具体看终端输出的内容）
   brew update
   brew install --HEAD usbmuxd
   brew link usbmuxd
   brew install --HEAD libimobiledevice
   brew install ideviceinstaller
   brew install ios-deploy
   brew install cocoapods
   pod setup
   # 注最后一个命令下载的内容很多
   ```

5. 通过命令行工具创建Flutter项目

  ```
  flutter create xxxx_xxx  
  # 注：项目名称不能是xxx-xxx的形式
  ```
  
7. 使用xcode打开生成的项目，目录路径为：

   ```
   /xxxx_xxx/ios/xxx.xcodeproj 
   ```

8. 选择模拟器运行项目

9. VScode相关插件安装  `Dart、Flutter、Awesome Flutter Snippets`



注意：最好在项目创建完成之后，扩展项目的相应权限，在之后可能会存在常见文件以及读取文件等操作，可能会报权限不足的错误 `sudo chmod -R 777 项目文件夹`

