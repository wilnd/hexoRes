---
title: docker安装Nginx
date: 2019-01-28 11:00:50
tags: docker
categories: docker
---
1. 拉取官方镜像
```
docker pull nginx
```
2. 启动参数

```
docker run --detach \
        --name ch-nginx \
        -p 443:443\
        -p 80:80 \
        -v /home/git:/home/git:rw\
        -v /usr/local/nginx/config/nginx.conf:/etc/nginx/nginx.conf/:rw\
        -v /usr/local/nginx/config/conf.d/default.conf:/etc/nginx/conf.d/default.conf:rw\
        -v /usr/local/nginx/logs:/var/log/nginx/:rw\
        -v /usr/local/nginx/ssl:/ssl/:rw\
        -d nginx
```
参数解析:
1. -p 将容器内部使用的网络端口映射到我们使用的主机上。
```
映射端口443，用于https请求
映射端口80，用于http请求；
```
2. --name 
```
将容器命名为mynginx
```
3.  -v /usr/local/nginx/data:/usr/share/nginx/html:rw 
```
nginx的默认首页html的存放目录映射到host盘的目录， /home/evan/workspace/wxserver/nginx/data
```
4. -v /usr/local/nginx/config/nginx.conf:/etc/nginx/nginx.conf/:rw
```
nginx的配置文件映射到host盘的文件，/home/evan/workspace/wxserver/nginx/config/nginx.conf
```
5. -v /usr/local/nginx/logs:/var/log/nginx/:rw\

```
日志文件挂载
```
6. /usr/local/nginx/ssl:/ssl/:rw\

```
ssl
```
6. -d 让容器在后台运行.


配置文件:
- conf
```

#运行nginx的用户
user  nginx;
#启动进程设置成和CPU数量相等
worker_processes  1;

#全局错误日志及PID文件的位置
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

#工作模式及连接数上限
events {
        #单个后台work进程最大并发数设置为1024
    worker_connections  1024;
}


http {
        #设定mime类型
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

        #设定日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

        #设置连接超时的事件
    keepalive_timeout  65;

        #开启GZIP压缩
    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```
- default

```

server {
    listen    80;       #侦听80端口，如果强制所有的访问都必须是HTTPs的，这行需要注销掉
    server_name  chblog.site,www.chblog.site;             #域名

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

        # 定义首页索引目录和名称
    location / {
        root   /home/git;
        index  index.html index.htm;
    }

    #定义错误提示页面
    #error_page  404              /404.html;

    #重定向错误页面到 /50x.html
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}

server {
    listen    443;
    server_name chblog.site,www.chblog.site;             #域名


    # 增加ssl
    ssl on;        #如果强制HTTPs访问，这行要打开
    ssl_certificate /ssl/chblog.site.pem;
    ssl_certificate_key /ssl/chblog.site.key;

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

     # 指定密码为openssl支持的格式
     ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

         # 密码加密方式
     ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
         # 依赖SSLv3和TLSv1协议的服务器密码将优先于客户端密码
     ssl_prefer_server_ciphers on;

     # 定义首页索引目录和名称
     location / {
        root   /home/git;
        index  index.html index.htm;
     }

    #重定向错误页面到 /50x.html
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}

```

