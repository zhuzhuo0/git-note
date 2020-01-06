## Linux 操作 [wget]

#### wget

```
# 实用wget下载单个文件，从网络下载一个文件并保存到当前目录
wget http://cn.wordpress.org/wordpress-3.1-zh_CN.zip

# -P 指定下载到哪个目录
wget -P /root/soft https://nodejs.org/dist/v12.13.1/node-v12.13.1-linux-x64.tar.xz

# -O 下载并以不同的文件名保存
wget默认会以最后一个符合”/”的后面的字符来命令，对于动态链接的下载通常文件名会不正确。
错误：下面的例子会下载一个文件并以名称download.php?id=1080保存
wget https://www.centos.bz/download?id=1
即使下载的文件是zip格式，它仍然以download.php?id=1080命令。
正确：为了解决这个问题，我们可以使用参数-O来指定一个文件名：
wget -O wordpress.zip https://www.centos.bz/download.php?id=1080

# 实用wget -limit -rate限速下载
当你执行wget的时候，它默认会占用全部可能的宽带下载。但是当你准备下载一个大文件，而你还需要下载其它文件时就有必要限速了。
wget --limit-rate=300k http://cn.wordpress.org/wordpress-3.1-zh_CN.zip

# 使用wget -c断点续传
使用wget -c重新启动下载中断的文件:
wget -c http://cn.wordpress.org/wordpress-3.1-zh_CN.zip
对于我们下载大文件时突然由于网络等原因中断非常有帮助，我们可以继续接着下载而不是重新下载一个文件。需要继续中断的下载时可以使用-c参数。

# 使用wget -b后台下载
对于下载非常大的文件的时候，我们可以使用参数-b进行后台下载。
wget -b http://cn.wordpress.org/wordpress-3.1-zh_CN.zip
Continuing in background, pid 1840.
Output will be written to `wget-log'.
你可以使用以下命令来察看下载进度
tail -f wget-log

# 伪装代理名称下载
有些网站能通过根据判断代理名称不是浏览器而拒绝你的下载请求。不过你可以通过–user-agent参数伪装。
wget --user-agent="Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.16 (KHTML, like Gecko) Chrome/10.0.648.204 Safari/534.16" 下载链接

# 使用wget –spider测试下载链接
当你打算进行定时下载，你应该在预定时间测试下载链接是否有效。我们可以增加–spider参数进行检查。

wget --spider URL
如果下载链接正确，将会显示

1.wget --spider URL
2.Spider mode enabled. Check if remote file exists.
3.HTTP request sent, awaiting response... 200 OK
4.Length: unspecified [text/html]
5.Remote file exists and could contain further links,
6.but recursion is disabled -- not retrieving.

这保证了下载能在预定的时间进行，但当你给错了一个链接，将会显示如下错误

1.wget --spider url
2.Spider mode enabled. Check if remote file exists.
3.HTTP request sent, awaiting response... 404 Not Found
4.Remote file does not exist -- broken link!!!
你可以在以下几种情况下使用spider参数：
*定时下载之前进行检查
*间隔检测网站是否可用
*检查网站页面的死链接

# 使用wget –tries增加重试次数
如果网络有问题或下载一个大文件也有可能失败。wget默认重试20次连接下载文件。如果需要，你可以使用–tries增加重试次数。
wget --tries=40 URL

# 实用wget -i下载多个文件
首先，保存一份下载链接文件
cat > filelist.txt
url1
url2
url3
url4
接着使用这个文件和参数-i下载
wget -i filelist.txt

# 使用wget –mirror镜像网站
下面的例子是下载整个网站到本地。
wget --mirror -p --convert-links -P ./LOCAL URL
–miror:开户镜像下载
-p:下载所有为了html页面显示正常的文件
–convert-links:下载后，转换成本地的链接
-P ./LOCAL：保存所有文件和目录到本地指定目录

# 使用wget –reject过滤指定格式下载
你想下载一个网站，但你不希望下载图片，你可以使用以下命令。
wget --reject=gif url

# 使用wget -o把下载信息存入日志文件
你不希望下载信息直接显示在终端而是在一个日志文件，可以使用以下命令：
wget -o download.log URL

# 使用wget -Q限制总下载文件大小
当你想要下载的文件超过5M而退出下载，你可以使用以下命令:
wget -Q5m -i filelist.txt
注意：这个参数对单个文件下载不起作用，只能递归下载时才有效

# 使用wget -r -A下载指定格式文件
可以在以下情况使用该功能
下载一个网站的所有图片
下载一个网站的所有视频
下载一个网站的所有PDF文件
wget -r -A.pdf url

# 使用wget FTP下载
你可以使用wget来完成ftp链接的下载。
使用wget匿名ftp下载
wget ftp-url
使用wget用户名和密码认证的ftp下载
wget --ftp-user=USERNAME --ftp-password=PASSWORD url

```

#### 完整参数

```
用法： wget [选项]... [URL]...
 
长选项所必须的参数在使用短选项时也是必须的。
 
开始:
  -V,  --version           显示 Wget 的版本信息并退出。
  -h,  --help              打印此帮助。
  -b,  --background        启动后转入后台。
  -e,  --execute=COMMAND   运行一个‘.wgetrc’风格的命令。
 
登入并输入文件:
  -o,  --output-file=FILE    将信息写入 FILE。
  -a,  --append-output=FILE  将信息添加至 FILE。
  -d,  --debug               打印大量调试信息。
  -q,  --quiet               安静模式(无信息输出)。
  -v,  --verbose             详尽的输出(此为默认值)。
  -nv, --no-verbose          关闭详尽输出，但不进入安静模式。
  -i,  --input-file=FILE     下载本地或外部 FILE 中的 URLs。
  -F,  --force-html          把输入文件当成 HTML 文件。
  -B,  --base=URL            解析与 URL 相关的
                             HTML 输入文件（由 -i -F 选项指定）。
 
下载:
  -t,  --tries=NUMBER           设置重试次数为 NUMBER (0 代表无限制)。
        --retry-connrefused       即使拒绝连接也是重试。
  -O,  --output-document=FILE    将文档写入 FILE。
  -nc, --no-clobber              不要重复下载已存在的文件。
                                 
  -c,  --continue                继续下载部分下载的文件。
       --progress=TYPE           选择进度条类型。
  -N,  --timestamping            只获取比本地文件新的文件。
                                  
  -S,  --server-response         打印服务器响应。
       --spider                   不下载任何文件。
  -T,  --timeout=SECONDS         将所有超时设为 SECONDS 秒。
       --dns-timeout=SECS        设置 DNS 查寻超时为 SECS 秒。
       --connect-timeout=SECS    设置连接超时为 SECS 秒。
       --read-timeout=SECS       设置读取超时为 SECS 秒。
  -w,  --wait=SECONDS            等待间隔为 SECONDS 秒。
       --waitretry=SECONDS       在取回文件的重试期间等待 1..SECONDS 秒。
       --random-wait             取回时等待 0...2*WAIT 秒。
       --no-proxy                关闭代理。
  -Q,  --quota=NUMBER            设置取回配额为 NUMBER 字节。
       --bind-address=ADDRESS    绑定至本地主机上的 ADDRESS (主机名或是 IP)。
       --limit-rate=RATE         限制下载速率为 RATE。
       --no-dns-cache            关闭 DNS 查寻缓存。
       --restrict-file-names=OS  限定文件名中的字符为 OS 允许的字符。
       --ignore-case             匹配文件/目录时忽略大小写。
  -4,  --inet4-only              仅连接至 IPv4 地址。
  -6,  --inet6-only              仅连接至 IPv6 地址。
       --prefer-family=FAMILY    首先连接至指定协议的地址
                                 FAMILY 为 IPv6，IPv4 或是 none。
       --user=USER               将 ftp 和 http 的用户名均设置为 USER。
       --password=PASS           将 ftp 和 http 的密码均设置为 PASS。
       --ask-password           提示输入密码。
       --no-iri                关闭 IRI 支持。
       --local-encoding=ENC      IRI 使用 ENC 作为本地编码。
       --remote-encoding=ENC     使用 ENC 作为默认远程编码。
 
目录:
  -nd, --no-directories           不创建目录。
  -x,  --force-directories        强制创建目录。
  -nH, --no-host-directories      不要创建主目录。
       --protocol-directories     在目录中使用协议名称。
  -P,  --directory-prefix=PREFIX  以 PREFIX/... 保存文件
       --cut-dirs=NUMBER          忽略 NUMBER 个远程目录路径。
 
HTTP 选项:
       --http-user=USER        设置 http 用户名为 USER。
       --http-password=PASS    设置 http 密码为 PASS。
       --no-cache              不在服务器上缓存数据。
       --default-page=NAME     改变默认页
                               （默认页通常是“index.html”）。
  -E,  --adjust-extension      以合适的扩展名保存 HTML/CSS 文档。
       --ignore-length         忽略头部的‘Content-Length’区域。
       --header=STRING         在头部插入 STRING。
       --max-redirect          每页所允许的最大重定向。
       --proxy-user=USER       使用 USER 作为代理用户名。
       --proxy-password=PASS   使用 PASS 作为代理密码。
       --referer=URL           在 HTTP 请求头包含‘Referer: URL’。
       --save-headers          将 HTTP 头保存至文件。
  -U,  --user-agent=AGENT      标识为 AGENT 而不是 Wget/VERSION。
       --no-http-keep-alive    禁用 HTTP keep-alive(永久连接)。
       --no-cookies            不使用 cookies。
       --load-cookies=FILE     会话开始前从 FILE 中载入 cookies。
       --save-cookies=FILE     会话结束后保存 cookies 至 FILE。
       --keep-session-cookies  载入并保存会话(非永久) cookies。
       --post-data=STRING      使用 POST 方式；把 STRING 作为数据发送。
       --post-file=FILE        使用 POST 方式；发送 FILE 内容。
       --content-disposition   当选中本地文件名时
                               允许 Content-Disposition 头部(尚在实验)。
       --auth-no-challenge     send Basic HTTP authentication information
                               without first waiting for the server's
                               challenge.
 
HTTPS (SSL/TLS) 选项:
       --secure-protocol=PR     选择安全协议，可以是 auto、SSLv2、
                                SSLv3 或是 TLSv1 中的一个。
       --no-check-certificate   不要验证服务器的证书。
       --certificate=FILE       客户端证书文件。
       --certificate-type=TYPE  客户端证书类型， PEM 或 DER。
       --private-key=FILE       私钥文件。
       --private-key-type=TYPE  私钥文件类型， PEM 或 DER。
       --ca-certificate=FILE    带有一组 CA 认证的文件。
       --ca-directory=DIR       保存 CA 认证的哈希列表的目录。
       --random-file=FILE       带有生成 SSL PRNG 的随机数据的文件。
       --egd-file=FILE          用于命名带有随机数据的 EGD 套接字的文件。
 
FTP 选项:
       --ftp-user=USER         设置 ftp 用户名为 USER。
       --ftp-password=PASS     设置 ftp 密码为 PASS。
       --no-remove-listing     不要删除‘.listing’文件。
       --no-glob               不在 FTP 文件名中使用通配符展开。
       --no-passive-ftp        禁用“passive”传输模式。
       --retr-symlinks         递归目录时，获取链接的文件(而非目录)。
 
递归下载:
  -r,  --recursive          指定递归下载。
  -l,  --level=NUMBER       最大递归深度( inf 或 0 代表无限制，即全部下载)。
       --delete-after       下载完成后删除本地文件。
  -k,  --convert-links      让下载得到的 HTML 或 CSS 中的链接指向本地文件。
  -K,  --backup-converted   在转换文件 X 前先将它备份为 X.orig。
  -m,  --mirror             -N -r -l inf --no-remove-listing 的缩写形式。
  -p,  --page-requisites    下载所有用于显示 HTML 页面的图片之类的元素。
       --strict-comments    开启 HTML 注释的精确处理(SGML)。
 
递归接受/拒绝:
  -A,  --accept=LIST               逗号分隔的可接受的扩展名列表。
  -R,  --reject=LIST               逗号分隔的要拒绝的扩展名列表。
  -D,  --domains=LIST              逗号分隔的可接受的域列表。
       --exclude-domains=LIST      逗号分隔的要拒绝的域列表。
       --follow-ftp                跟踪 HTML 文档中的 FTP 链接。
       --follow-tags=LIST          逗号分隔的跟踪的 HTML 标识列表。
       --ignore-tags=LIST          逗号分隔的忽略的 HTML 标识列表。
  -H,  --span-hosts                递归时转向外部主机。
  -L,  --relative                  只跟踪有关系的链接。
  -I,  --include-directories=LIST  允许目录的列表。
  -X,  --exclude-directories=LIST  排除目录的列表。
  -np, --no-parent                 不追溯至父目录。
```

