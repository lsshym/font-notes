## nginx笔记



#### 1 安装

安装nginx要先安装依赖

```
yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
```

[官网下载nginx](http://nginx.org/)

下完了应该是个这么样子

![1581493388(1)](C:\Users\Mr.Giraffe\Desktop\1581493388(1).jpg)

然后解压

```
tar -xvf nginx-1.16.tar.gz
// -x是解压 v是显示过程 f是使用档案名
```

进到文件夹

依次运行

```
./configure
make && make install
```

然后就可以启动服务了

```
在目录 /usr/local/nginx/sbin/nginx
// 另外usr是 Unix System Resource，系统资源
//nginx默认启动80端口，服务器也需要开发这个端口，我用的阿里云比较简单，配置一下安全组就好了
./nginx启动服务，之后访问ip，会出现
welcome nginx
```

### 2nginx命令

```
// 启动
./nginx 
-s表示后面的是指令
./nginx -s stop
./nginx -s reload
// 查看进程
ps aux | grep nginx
```

# nginx配置

常用的几点

```shell
#user nobody不应该是这个，我说怎么查看进程的时候总有一个nobody
#改成user root
#添加一行error_log 然后再nginx文件夹下找到logs
```

设置访问权限

```shell
# 在http下server 的location 中配置
# 花括号前要有一个空格

location / {
   root ./index;
   index index.html;
   allow  192.1.1.22/33;
   deny all;
}
# = 代表精准匹配
location =/admin {

}
# 以及正则表达式匹配
location ~\.php$ {
       deny all;
}
```

设置虚拟主机，其实就是在同一台服务器上开开多个主机

```shell
# 根据端口号，不同端口号跳转不同的页面
server {
        listen 8001;
        server_name localhost;
        root ./index;
        index index.html;
}

# 根据IP，抱歉没这条件
server {
        listen 80;
        server_name 112.74.164.244;
        root ./index;
        index index.html;
}

# 其实也可以换成域名
server{
        listen 80;
        server_name 666.com;
        location / {
                root /index;
                index index.html index.htm;
        }
}
```

设置反向代理解决跨域问题，这段抄的

- 安全性：正向代理的客户端能够在隐藏自身信息的同时访问任意网站，这个给网络安全代理了极大的威胁。因此，我们必须把服务器保护起来，使用反向代理客户端用户只能通过外来网来访问代理服务器，并且用户并不知道自己访问的真实服务器是那一台，可以很好的提供安全保护。
- 功能性：反向代理的主要用途是为多个服务器提供负债均衡、缓存等功能。负载均衡就是一个网站的内容被部署在若干服务器上，可以把这些机子看成一个集群，那Nginx可以将接收到的客户端请求“均匀地”分配到这个集群中所有的服务器上，从而实现服务器压力的平均分配，也叫负载均衡。

```shell
server {
	listen 80;
	# 要访问的ip
	server_name moyuking.xyz;
	location / {
		proxy_pass baidu.com
	}
}
```













```shell
user  root;
worker_processes  1;

error_log  logs/error.log;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65;


    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}

```





```
user  root;
worker_processes  1;

error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  39.106.37.191;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /root/blog/build;
            index  index.html index.htm;

        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

