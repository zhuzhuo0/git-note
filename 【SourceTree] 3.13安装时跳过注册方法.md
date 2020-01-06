## 【SourceTree] 3.13安装时跳过注册方法

#### SourceTree 3.1.3版本跳过bitbucket注册方法

1. 下载并安装好git

2. 下载并执行SourceTreeSetup-3.1.3.exe，会进入登录或注册bitbucket的界面

3. 关闭上述安装窗口，打开 `%LocalAppData%\Atlassian\SourceTree` 目录，创建并修改accounts.json文件

   ```json
   [
     {
       "$id": "1",
       "$type": "SourceTree.Api.Host.Identity.Model.IdentityAccount, SourceTree.Api.Host.Identity",
       "Authenticate": true,
       "HostInstance": {
         "$id": "2",
         "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountInstance, SourceTree.Host.AtlassianAccount",
         "Host": {
           "$id": "3",
           "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountHost, SourceTree.Host.AtlassianAccount",
           "Id": "atlassian account"
         },
         "BaseUrl": "https://id.atlassian.com/"
       },
       "Credentials": {
         "$id": "4",
         "$type": "SourceTree.Model.BasicAuthCredentials, SourceTree.Api.Account",
         "Username": "",
         "Email": null
       },
       "IsDefault": false
     }
   ]
   ```

4. 返回上一级，进入**SourceTree.exe_Url_xxxxxxxxxxxxxxx\3.1.3.3158目录（%LocalAppData%\Atlassian\SourceTree.exe_Url_xxxxxxxxxxxxxxx\3.1.3.3158）**（注：该目录可能和版本相关，不同版本的路径可能不完全一样），打开**user.config**文件，在里面加入六行代码

   ```
   <setting name="AgreedToEULA" serializeAs="String">
       <value>True</value>
   </setting>
   <setting name="AgreedToEULAVersion" serializeAs="String">
       <value>20160201</value>
   </setting>
   ```

   ![2019062710312391](/Users/zhuzhu/Pictures/2019062710312391.png)

5. 再次执行SourceTreeSetup-3.1.3.exe，就可以跳过注册阶段