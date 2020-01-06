## nginx配置项说明

# Nginx 配置

在了解具体的Nginx配置项之前我们需要对于Nginx配置文件的构成有所概念，一般来说，Nginx配置文件会由如下几个部分构成：

```text
# 全局块
...              
# events块
events {         
   ...
}
# http块
http      
{
    # http全局块
    ...   
    # 虚拟主机server块
    server        
    { 
        # server全局块
        ...       
        # location块
        location [PATTERN]   
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    # http全局块
    ...     
}
```

在上述配置中我们可以看出，Nginx配置文件由以下几个部分构成：

- 全局块：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
- events块：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
- http块：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
- server块：配置虚拟主机的相关参数，一个http中可以有多个server。
- location块：配置请求的路由，以及各种页面的处理情况。

```text
########### 每个指令必须有分号结束。#################
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;  #允许生成的进程数，默认为1
#pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
events {
    accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;    #最大连接数，默认为512
}
http {
    include       mime.types;   #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。

    # 定义常量
    upstream mysvr {   
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    error_page 404 https://www.baidu.com; #错误页 
    
    #定义某个负载均衡服务器   
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
} 
```

# 虚拟主机与静态站点

> - [SERVING STATIC CONTENT](https://link.zhihu.com/?target=https%3A//www.nginx.com/resources/admin-guide/serving-static-content/)

本部分概述如何配置Nginx进行静态内容服务，Nginx的静态内容分发能力还是非常强大的。

```text
http {
 server {
     listen          80;
     server_name     www.domain1.com;
     access_log      logs/domain1.access.log main;
     location / {
         index index.html;
         root  /var/www/domain1.com/htdocs;
     }
 }
 server {
     listen          80;
     server_name     www.domain2.com;
     access_log      logs/domain2.access.log main;
     location / {
         index index.html;
         root  /var/www/domain2.com/htdocs;
     }
 }
}
```

## 虚拟主机配置详解

### 主机与端口

```text
listen 127.0.0.1:8000;
listen *:8000;
listen localhost:8000;
# IPV6
listen [::]:8000;
# other params
listen 443 default_server ssl;
listen 127.0.0.1 default_server accept_filter=dataready backlog=1024
```

### 服务域名

```text
# 支持多域名配置
server_name www.barretlee.com barretlee.com;
# 支持泛域名解析
server_name *.barretlee.com;
# 支持对于域名的正则匹配
server_name ~^\.barret\.com$;
```

### URI匹配

```text
location = / {
    # 完全匹配  =
    # 大小写敏感 ~
    # 忽略大小写 ~*
}
location ^~ /images/ {
    # 前半部分匹配 ^~
    # 可以使用正则，如：
    # location ~* \.(gif|jpg|png)$ { }
}
location / {
    # 如果以上都未匹配，会进入这里
}
```

## 文件路径配置

### 根目录

```text
location / {
    root /home/barret/test/;
}
```

### 别名

```text
location /blog {
    alias /home/barret/www/blog/;
}
location ~ ^/blog/(\d+)/([\w-]+)$ {
    # /blog/20141202/article-name  
    # -> /blog/20141202-article-name.md
    alias /home/barret/www/blog/$1-$2.md;
}
```

### 首页

```text
index /html/index.html /php/index.php;
```

### 重定向页面

```text
error_page    404         /404.html;
error_page    502  503    /50x.html;
error_page    404  =200   /1x1.gif;
location / {
    error_page  404 @fallback;
}
location @fallback {
    # 将请求反向代理到上游服务器处理
    proxy_pass http://localhost:9000;
}
```

### try_files

```text
try_files $uri $uri.html $uri/index.html @other;
location @other {
    # 尝试寻找匹配 uri 的文件，失败了就会转到上游处理
    proxy_pass  http://localhost:9000;
}
location / {
    # 尝试寻找匹配 uri 的文件，没找到直接返回 502
    try_files $uri $uri.html =502;
}
```

## 缓存配置

> - [HTTP 缓存的四种风味与缓存策略](https://link.zhihu.com/?target=https%3A//segmentfault.com/a/1190000006689795)

### Expire:过期时间

在Nginx中可以配置缓存的过期时间：

```text
 location ~* \.(?:ico|css|js|gif|jpe?g|png)$ {
        expires 30d;
        add_header Vary Accept-Encoding;
                access_log off;
    }
```

我们也可以添加更复杂的配置项：

```text
    location ~* ^.+\.(?:css|cur|js|jpe?g|gif|htc|ico|png|html|xml|otf|ttf|eot|woff|svg)$ {

        access_log off;
        expires 30d;
        ## No need to bleed constant updates. Send the all shebang in one
        ## fell swoop.
        tcp_nodelay off;
        ## Set the OS file cache.
        open_file_cache max=3000 inactive=120s;
        open_file_cache_valid 45s;
        open_file_cache_min_uses 2;
        open_file_cache_errors off;
    }
```

# 反向代理

```text
events{

}
http{
    upstream ggzy {
       server 127.0.0.1:1398 weight=3;
       server 127.0.0.1:1399;
    }
    # 80端口配置，可配置多个Virtual Host
    server {
        listen  80;
        index index index.htm index.py index.html;

        server_name app.truelore.cn;

        location / {
            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_pass http//ggzy;
        }
    }
}
```

## NodeJS Application

```text
const http = require('http');
http.createServer((req, res) => {
    res.end('hello world');
}).listen(9000);
```

任何请求过来都返回 hello world，简版的 Nginx 配置如下，

```text
events {
    # 这里可不写东西
    use epoll;
}
http {
    server {
        listen 127.0.0.1:8888;
        # 如果请求路径跟文件路径按照如下方式匹配找到了，直接返回
        try_files $uri $uri/index.html;
        location ~* ^/(js|css|image|font)/$ {
            # 静态资源都在 static 文件夹下
            root /home/barret/www/static/;
        }
        location /app {
            # Node.js 在 9000 开了一个监听端口
            proxy_pass http://127.0.0.1:9000;
        }
        # 上面处理出错或者未找到的，返回对应状态码文件
        error_page    404            /404.html;
        error_page    502  503  504  /50x.html;
    }
}
```

首先 try_files，尝试直接匹配文件；没找到就匹配静态资源；还没找到就交给 Node 处理；否则就返回 4xx/5xx 的状态码。

## Upstream Cache

> - [A Guide to Caching with NGINX and NGINX Plus](https://link.zhihu.com/?target=https%3A//www.nginx.com/blog/nginx-caching-guide/)

```text
http {
    ,,,,,
    proxy_cache_path /var/cache/nginx/cache levels=1:2 keys_zone=imgcache:100m inactive=1d max_size=10g;
    server {
    ........
        location ~* ^.+\.(js|ico|gif|jpg|jpeg|png|html|htm)$ {
        log_not_found off;
        access_log off;
        expires 7d;
        proxy_pass http://img.example.com ;
        proxy_cache imgcache;
        proxy_cache_valid 200 302 1d;
        proxy_cache_valid 404 10m;
        proxy_cache_valid any 1h;
        proxy_cache_use_stale error timeout invalid_header updating http_500 http_502 http_503 http_504;
        }
    }
}
```

# HTTPS

> - [HTTPS 理论详解与实践](https://link.zhihu.com/?target=https%3A//segmentfault.com/a/1190000004985253)

## Let's Encrypt 证书申请

Let's Encrypt 为我们提供了非常方便的命令行工具[certbot](https://link.zhihu.com/?target=https%3A//certbot.eff.org/%23ubuntuxenial-nginx)，笔者是在Ubuntu 16.04的机器上进行配置，因此只要执行如下命令即可:

```text
# 安装letsencrypt命令行

$ sudo apt-get install letsencrypt 
# 独立的为example.com与www.example.com申请证书
$ letsencrypt certonly --standalone -d example.com -d www.example.com
# 自动执行证书刷新操作
$ letsencrypt renew --dry-run --agree-tos
```

## 基本HTTPS配置

基本的HTTPS支持配置如下:

```text
server {  
    listen 192.168.1.11:443;  #ssl端口  
    server_name  test.com;  
    #为一个server{......}开启ssl支持  
    ssl on;  
    #指定PEM格式的证书文件   
    ssl_certificate      /etc/nginx/test.pem;   
    #指定PEM格式的私钥文件  
    ssl_certificate_key  /etc/nginx/test.key;  
}  
```

在真实的生产环境中，我们的配置如下:

```text
server {
    # 如果需要spdy也可以加上,lnmp1.2及其后版本都默认支持spdy,lnmp1.3 nginx 1.9.5以上版本默认支持http2
    listen 443 ssl;   
    # 这里是你的域名
    server_name www.vpser.net;     
    index index.html index.htm index.php default.html default.htm default.php;
    # 网站目录
    root /home/wwwroot/www.vpser.net;            
    # 前面生成的证书，改一下里面的域名就行
    ssl_certificate /etc/letsencrypt/live/www.vpser.net/fullchain.pem;    
    # 前面生成的密钥，改一下里面的域名就行
    ssl_certificate_key /etc/letsencrypt/live/www.vpser.net/privkey.pem;   
    ssl_ciphers "EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5";
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;

    #这个是伪静态根据自己的需求改成其他或删除
    include wordpress.conf;  
    
    #error_page 404 /404.html;
    
    location ~ [^/]\.php(/|$)
    {
        # comment try_files $uri =404; to enable pathinfo
        try_files $uri =404;
        fastcgi_pass unix:/tmp/php-cgi.sock;
        fastcgi_index index.php;
        # lnmp 1.0及之前版本替换为include fcgi.conf;
        include fastcgi.conf;     
        #include pathinfo.conf;
    }

    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
    {
    expires 30d;
    }

    location ~ .*\.(js|css)?$
    {
    expires 12h;
    }

    access_log off;
}
```

## 强制HTTP转到HTTPS

### Nginx Rewrite

```text
server {  
    listen  192.168.1.111:80;  
    server_name test.com;  
    rewrite ^(.*)$  https://$host$1 permanent;  
}  
```

### Nginx 497错误码

利用error_page命令将497状态码的链接重定向到[https://test.com](https://link.zhihu.com/?target=https%3A//test.com)这个域名上

```text
server {  
    listen       192.168.1.11:443;  #ssl端口  
    listen       192.168.1.11:80;   #用户习惯用http访问，加上80，后面通过497状态码让它自动跳到443端口  
    server_name  test.com;  
    #为一个server{......}开启ssl支持  
    ssl                  on;  
    #指定PEM格式的证书文件   
    ssl_certificate      /etc/nginx/test.pem;   
    #指定PEM格式的私钥文件  
    ssl_certificate_key  /etc/nginx/test.key;  
        
    #让http请求重定向到https请求   
    error_page 497  https://$host$uri?$args;  
}  
```

### Meta刷新，前端跳转

在HTTP正常返回的页面中添加meta属性：

```text
<html>  
<meta http-equiv="refresh" content="0;url=https://test.com/">  
</html>  
server {  
    listen 192.168.1.11:80;  
    server_name test.com;  
        
    location / {  
                #index.html放在虚拟主机监听的根目录下  
        root /srv/www/http.test.com/;  
    }  
        #将404的页面重定向到https的首页  
    error_page  404 https://test.com/;  
}  
```



### 问题：

#### X-Forwarded-For和相关几个头部的理解

- **$remote_addr**
   是nginx与客户端进行TCP连接过程中，获得的客户端真实地址. Remote Address 无法伪造，因为建立 TCP 连接需要三次握手，如果伪造了源 IP，无法建立 TCP 连接，更不会有后面的 HTTP 请求
- **X-Real-IP**
   是一个自定义头。X-Real-Ip 通常被 HTTP 代理用来表示与它产生 TCP 连接的设备 IP，这个设备可能是其他代理，也可能是真正的请求端。需要注意的是，X-Real-Ip 目前并不属于任何标准，代理和 Web 应用之间可以约定用任何自定义头来传递这个信息
- **X-Forwarded-For**
   X-Forwarded-For 是一个扩展头。HTTP/1.1（RFC 2616）协议并没有对它的定义，它最开始是由 Squid 这个缓存代理软件引入，用来表示 HTTP 请求端真实 IP，现在已经成为事实上的标准，被各大 HTTP 代理、负载均衡等转发服务广泛使用，并被写入 RFC 7239（Forwarded HTTP Extension）标准之中.

X-Forwarded-For请求头格式非常简单，就这样：

```
  X-Forwarded-For:client, proxy1, proxy2
```

可以看到，XFF 的内容由「英文逗号 + 空格」隔开的多个部分组成，最开始的是离服务端最远的设备 IP，然后是每一级代理设备的 IP。

如果一个 HTTP 请求到达服务器之前，经过了三个代理 Proxy1、Proxy2、Proxy3，IP 分别为 IP1、IP2、IP3，用户真实 IP 为 IP0，那么按照 XFF 标准，服务端最终会收到以下信息：

```
X-Forwarded-For: IP0, IP1, IP2
```

Proxy3 直连服务器，它会给 XFF 追加 IP2，表示它是在帮 Proxy2 转发请求。列表中并没有 IP3，IP3 可以在服务端通过 $remote_address 字段获得。我们知道 HTTP 连接基于 TCP 连接，HTTP 协议中没有 IP 的概念，$remote_address  来自 TCP 连接，表示与服务端建立 TCP 连接的设备 IP，在这个例子里就是 IP3。

详细分析一下，这样的结果是经过这样的流程而形成的：

1. 用户IP0---> 代理Proxy1（IP1），Proxy1记录用户IP0，并将请求转发个Proxy2时，带上一个Http Header
    `X-Forwarded-For: IP0` 
2. Proxy2收到请求后读取到请求有 `X-Forwarded-For: IP0`，然后proxy2 继续把链接上来的proxy1 ip**追加**到 X-Forwarded-For 上面，构造出`X-Forwarded-For: IP0, IP1`，继续转发请求给Proxy 3
3. 同理，Proxy3 按照第二部构造出 `X-Forwarded-For: IP0, IP1, IP2`,转发给真正的服务器，比如NGINX，nginx收到了http请求，里面就是 `X-Forwarded-For: IP0, IP1, IP2` 这样的结果。所以Proxy 3 的IP3，不会出现在这里。
4. nginx 获取proxy3的IP 能通过$remote_address获取到，因为这个$remote_address就是真正建立TCP链接的IP，这个不能伪造，是直接产生链接的IP。**$remote_address  无法伪造，因为建立 TCP 连接需要三次握手，如果伪造了源 IP，无法建立 TCP 连接，更不会有后面的 HTTP 请求。** 

#### x-forwarded-for 实践研究：

1. uwsgi_pass的情况下，nginx 没有设置proxy_pass x-forwarded-for: $proxy_add_x_forwarded_for;
    如果请求头传了XFF，在flask里面能正常读取请求头里面的XFF,就是当是一个普通的头读出；如果header不传这个XFF的话，就读不到
2. proxy_pass 情况下

- 没有传 # proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for 的话，跟上面的uwsgi_pass 一样，都是在没有设置header XFF情况下，读不到。
- 如果传了  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for， header 不传xff 的话，也是可以在程序里面读到Xff 头： X-Forwarded-For: 10.0.2.2 （这个IP就是真正连上nginx 的IP， 也就是$remote_address），因为这句proxy_set_header 会让nginx追加一个$remote_address到XFF。
- header 传xff的话， 程序里面可以读到Xff 头： X-Forwarded-For: 188.103.19.120, 10.0.2.2 （第一个是我自己编的，第二个是$remote_address），nginx还是会因为proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for 这句而追加$remote_addr到XFF。

总结：

1. 只要nginx前端（例如lvs， varnish）转发请求给nginx的时候，带了x-forwarded-for ,那么程序就一定能读到这个字段，如果nginx还设置了proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for， 那么程序能读到XFF是：ip0, ip1 (客户端Ip，lvs或者varnishIP)。 如果nginx没有设置，那么nginx还是会原样把http头传给程序，也就是说程序也能读到XFF，而且XFF就是ip0 客户端IP。
2. proxy_pass 设置这个头  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 是站在一个作为代理的角度把。能继续传输多级代理的头。
3. nginx的日志格式写了$http_x_forwared_for 说明前端（lvs）确实传了这个头过来。所以是程序是读取到的
4. uwsgi_pass 不能设置 proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 这个头，是因为这个头是对http代理来说，用来传递IP的，uwsgi 不可能充当一个代理。
5. nginx->程序，这里其实有两个链接过程，其他IP与nginx的TCP链接， nginx与程序的TCP链接。所以$remote_addr都是对各自来说的。
    程序的remote_addr: remote_addr 127.0.0.1 (跟它链接的是nginx 内网127.0.0.1)
    nginx的remote_addr : X-Real-Ip: 10.0.2.2 （跟它链接的是我的电脑，IP 10.0.2.2）

1. 对程序来说，读取的request.remote_addr 也永远是直接跟他链接的ip， 也就是反向代理nginx
2. The access_route attribute uses the [X-Forwarded-For](http://en.wikipedia.org/wiki/X-Forwarded-For)[ ](http://en.wikipedia.org/wiki/X-Forwarded-For)[header](http://en.wikipedia.org/wiki/X-Forwarded-For), falling back to the REMOTE_ADDRWSGI variable;  也就是说access_route默认读取XFF头，如果没有，降级读取WSGI的REMOTE_ADDR变量,这个 WSGI的REMOTE_ADDR变量 就是 $remote_addr
3. request.envron 是WSGI的变量，都是wsgi server转过来的，普通的头都是加了HTTP_前缀的 ，包括proxy_set_header Host            $host:8000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    添加的头都会出现在处理，因为他们就是普通的http头
4. LVS->nginx的情况下， 请求的时候主动加XFF，程序读取的时候没显示。因为LVS设置XFF的时候，直接把直连的IP赋值给LVS，忽略掉所有本来有的XFF，要从LVS这里开始。 所以程序读到的XFF是 ：XFF headers 218.107.55.254, 10.120.214.252
    前面的是我的IP， 后面的是LVS的IP

```
{
  "wsgi.multiprocess": "False",
  "SERVER_SOFTWARE": "Werkzeug/0.11.10",
  "SCRIPT_NAME": "",
  "REQUEST_METHOD": "GET",
  "PATH_INFO": "/api/get_agreement_url/",
  "SERVER_PROTOCOL": "HTTP/1.0",
  "QUERY_STRING": "",
  "werkzeug.server.shutdown": "<function shutdown_server at 0x7f4a2f4e5488>",
  "CONTENT_LENGTH": "",
  "SERVER_NAME": "127.0.0.1",
  "REMOTE_PORT": 58284,
  "werkzeug.request": "",
  "wsgi.url_scheme": "http",
  "SERVER_PORT": "6000",
  "HTTP_POSTMAN_TOKEN": "666cfd97-585b-c342-f0bd-5c785dfff27d",
  "wsgi.input": "",
  "wsgi.multithread": "False",
  "HTTP_CACHE_CONTROL": "no-cache",
  "HTTP_ACCEPT": "*/*",
  "wsgi.version": "(1, 0)",
  "wsgi.run_once": "False",
  "wsgi.errors": "",
  "CONTENT_TYPE": "",
  "REMOTE_ADDR": "127.0.0.1",

  "HTTP_CONNECTION": "close",
  "HTTP_USER_AGENT": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36",
  "HTTP_ACCEPT_LANGUAGE": "zh-CN,zh;q=0.8,en;q=0.6",
  "HTTP_X_FORWARDED_FOR": "10.0.2.2",
  "HTTP_ACCEPT_ENCODING": "gzip, deflate, sdch",
  "HTTP_HOST": "[test.mumu.nie.netease.com:8000](http://test.mumu.nie.netease.com:8000/)",

}
```

$proxy_add_x_forwarded_for; nginx的这个变量含义就是，每次都追加$remote_address 到 xff头，如果xff头不存在，那么xff就被设置成跟$remote_address 一样了。如果本来就存在，就追加了 ip1, ip2这样的形式

