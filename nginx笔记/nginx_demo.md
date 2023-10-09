// # 定义HTTP服务器块，监听端口 80
server {
    listen 80;
    server_name yourdomain.com; # 您的域名或服务器 IP 地址

    # 配置日志文件
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # 静态资源处理，通常位于 /var/www/html
    location /static/ {
        alias /var/www/html/static/;
    }

    # 反向代理至后端应用服务器，例如Node.js应用
    location /api/ {
        proxy_pass http://localhost:3000; # 后端应用的地址和端口
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # 默认页面（例如index.html），如果找不到其他匹配项
    location / {
        root /var/www/html;
        index index.html;
    }
}
