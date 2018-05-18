# Nginx代理tomcat实现https和wss给小程序用
## Nginx设置
server.conf
```
server {
    listen 443;
    server_name app.aianythings.cn;
    ssl on;
    root html;
    index index.html index.htm;
    ssl_certificate   cert/1527435176887.pem;
    ssl_certificate_key  cert/1527435176887.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location /wss {
	    proxy_redirect off;
        proxy_pass http://localhost:8080/WSTD/websocket;
	    proxy_set_header Host $Host;
	    proxy_set_header X-Real-IP $remote_addr;
	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }
    location / {
	      proxy_pass http://localhost:8080/WSTD/;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        add_header Access-Control-Allow-Origin *;

    }
}
```
nginx.conf

```

user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
    }
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

*这里有几点一定要注意：
1. 证书设置
2. location 和 proxy_pass 的/斜杠问题，[可以看这里有说明](https://www.zifangsky.cn/917.html)
