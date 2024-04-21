# 安装nginx
    # 安装 make 和 g++ ,一般系统已安装无需在安装
    yum -y install autoconf automake make
    yum -y install gcc gcc-c++
    # 安装nginx前安装
        yum -y install zlib zlib-devel | gzip模块需要 zlib 库
        yum -y install pcre pcre-devel | rewrite模块需要 pcre 库
        yum -y install openssl openssl-devel | ssl 功能需要openssl库
    # 下载
        cd /usr/local  # 个人是压缩包下载到 创建的 /soft/目录 安装解压到 /usr/local/目录
        wget  http://nginx.org/download/nginx-VERSION.tar.gz # 或自行本地下载上传
        tar -zvxf nginx-1.16.1.tar.gz
    # 到解压目录下执行 ./configure
    # --prefix指定安装目录
    # --with-http_ssl_module安装https模块
    # creating objs/Makefile 代表编译成功
        ./configure   --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module 
    # make编译
    # make install安装
        make & make install
    # 启停命令：
        ./nginx -c nginx.conf的文件。如果不指定，默认为NGINX_HOME/conf/nginx.conf
        ./nginx -s stop  停止
        ./nginx -s quit退出
        ./nginx -s reload 重新加载nginx.conf
    # 日志切割
        编写sh：
            # !/bin/bash
            # 设置日志文件存放目录
            LOG_HOME=/usr/local/nginx/logs/
            # 备分文件名称
            LOG_PATH_BAK=$(date -d yesterday +%Y%m%d%H%M)
            # 重命名日志文件
            mv ${LOG_HOME}/access.log ${LOG_HOME}/access.${LOG_PATH_BAK}.log
            mv ${LOG_HOME}/error.log ${LOG_HOME}/error.${LOG_PATH_BAK}.log
            # 向nginx主进程发信号重新打开日志
            kill -USR1 cat ${LOG_HOME}/nginx.pid

        配置cron：（centos7 命令 crontab -e）
            */1 * * * * /usr/local/nginx/sbin/logcut.sh
    # nginx安装第三方模块echo
        使用第三方模块ngx_echo的功能，请重新配置添加到nginx插件中

        # # 下载第三方模块
            wget https://github.com/openresty/echo-nginx-module/archive/v0.61.tar.gz
            tar -zxvf v0.61.tar.gz		# # 解压
            cd nginx-VERSION			# # 进入nginx源码目录，准备重新配置nginx
        # # 配置，--add-module指向模块目录即会安装插件到nginx中
        ./configure --add-module=/usr/local/src/echo-nginx-module-0.61/ 
        make && make install

# 基本配置
# # # # # # # # # # #  每个指令必须有分号结束。# # # # # # # # # # # # # # # # # 
# user administrator administrators;  # 配置用户或者组，默认为nobody nobody。
# worker_processes 2;  # 允许生成的进程数，默认为1
# pid /nginx/pid/nginx.pid;   # 指定nginx进程运行文件存放地址
    error_log log/error.log debug;  # 制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
    events {
        accept_mutex on;   # 设置网路连接序列化，防止惊群现象发生，默认为on
        multi_accept on;  # 设置一个进程是否同时接受多个网络连接，默认为off
        # use epoll;      # 事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
        worker_connections  1024;    # 最大连接数，默认为512
    }
    http {
        include       mime.types;   # 文件扩展名与文件类型映射表
        default_type  application/octet-stream; # 默认文件类型，默认为text/plain
        # access_log off; # 取消服务日志    
        log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; # 自定义格式
        access_log log/access.log myFormat;  # combined为日志格式的默认值
        sendfile on;   # 允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
        sendfile_max_chunk 100k;  # 每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
        keepalive_timeout 65;  # 连接超时时间，默认为75s，可以在http，server，location块。
# 负载均衡
        upstream mysvr {   
        server 127.0.0.1:7878;
        server 192.168.10.121:3333 backup;  # 热备
        }
        # upstream mysvr { # 默认 1:1 轮询
        # server 127.0.0.1:7878;
        # server 192.168.10.121:3333;       
        # }
        # upstream mysvr { # 加权轮询
        # server 127.0.0.1:7878 weight=1 max_fails=2 fail_timeout=2;
        # server 192.168.10.121:3333 weight=2 max_fails=2 fail_timeout=1;
        # }
        # upstream mysvr { # 相同的客户端ip请求相同的服务器
        # server 127.0.0.1:7878; 
        # server 192.168.10.121:3333;
        # ip_hash;
        # }
        error_page 404 https://www.baidu.com; # 错误页
        server {
            keepalive_requests 120; # 单连接请求上限次数。
            listen       4545;   # 监听端口
            server_name  127.0.0.1;   # 监听地址       
            location  ~*^.+$ {       # 请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
            # root path;  # 根目录
            # index vv.txt;  # 设置默认页
            proxy_pass  http://mysvr;  # 请求转向mysvr 定义的服务器列表
            deny 127.0.0.1;  # 拒绝的ip
            allow 172.18.5.54; # 允许的ip           
            } 
        }
    }
# upstream 代理参数
weight, 加权轮询
ip_hash,相同的客户端ip请求相同的服务器
down，表示当前的server暂时不参与负载均衡。
backup，预留的备份机器。当其他所有的非backup机器出现故障或者忙的时候，才会请求backup机器，因此这台机器的压力最轻。
max_fails，允许请求失败的次数，默认为1。当超过最大次数时，返回proxy_next_upstream 模块定义的错误。
fail_timeout，在经历了max_fails次失败后，暂停服务的时间。max_fails可以和fail_timeout一起使用。


# # 代理服务相关配置
    proxy_connect_timeout 1;   # nginx服务器与被代理的服务器建立连接的超时时间，默认60秒
    proxy_read_timeout 1; # nginx服务器想被代理服务器组发出read请求后，等待响应的超时间，默认为60秒。
    proxy_send_timeout 1; # nginx服务器想被代理服务器组发出write请求后，等待响应的超时间，默认为60秒。
    proxy_http_version 1.0 ; # Nginx服务器提供代理服务的http协议版本1.0，1.1，默认设置为1.0版本。
    # proxy_method get;    # 支持客户端的请求方法。post/get；
    proxy_ignore_client_abort on;  # 客户端断网时，nginx服务器是否终端对被代理服务器的请求。默认为off。
    proxy_ignore_headers "Expires" "Set-Cookie";  # Nginx服务器不处理设置的http相应投中的头域，这里空格隔开可以设置多个。
    proxy_intercept_errors on;    # 如果被代理服务器返回的状态码为400或者大于400，设置的error_page配置起作用。默认为off。
    proxy_headers_hash_max_size 1024; # 存放http报文头的哈希表容量上限，默认为512个字符。
    proxy_headers_hash_bucket_size 128; # nginx服务器申请存放http报文头的哈希表容量大小。默认为64个字符。
    proxy_next_upstream timeout;  # 反向代理upstream中设置的服务器组，出现故障时，被代理服务器返回的状态值。error|timeout|invalid_header|http_500|http_502|http_503|http_504|http_404|off
    # proxy_ssl_session_reuse on; 默认为on，如果我们在错误日志中发现“SSL3_GET_FINSHED:digest check failed”的情况时，可以将该指令设置为off。