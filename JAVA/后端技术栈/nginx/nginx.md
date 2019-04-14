[TOC]

## nginx 出现504 Gateway Time-out的解决方法
fastcgi_connect_timeout
fastcgi_send_timeout
fastcgi_read_timeout
fastcgi连接超时时间，默认60秒
nginx 进程向 fastcgi 进程发送请求过程的超时时间，默认值60秒
fastcgi 进程向 nginx 进程发送输出过程的超时时间，默认值60秒

```conf
http {
	include       mime.types;
	default_type  application/octet-stream;

	log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
		'$status $body_bytes_sent "$http_referer" '
		'"$http_user_agent" "$http_x_forwarded_for"'
		'"$gzip_ratio"';
	log_format  download  '$remote_addr - $remote_user [$time_local] "$request" '
		'$status $body_bytes_sent "$http_referer" '
		'"$http_user_agent" "$http_x_forwarded_for"'
		'"$http_range" "$sent_http_content_range"';

	client_max_body_size  20m;
	client_header_buffer_size 32k;
	large_client_header_buffers 4 32k;
	sendfile        on;
	tcp_nopush     on;
	tcp_nodelay     on;

	keepalive_timeout  65;
	client_header_timeout 60;
	client_body_timeout 10;
	send_timeout        10;
	
    fastcgi_connect_timeout 1200s;
    fastcgi_send_timeout 1200s;
    fastcgi_read_timeout 1200s;

	# HTTPS server

	server {
		listen 443 ssl;
		server_name www.xiaoyougaotou.com;
        ssl on;
		ssl_certificate www_full_chain.pem;
		ssl_certificate_key www_private.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers ECDH:AESGCM:HIGH:!RC4:!DH:!MD5:!aNULL:!eNULL;
        ssl_prefer_server_ciphers on;
		
        location / {
            root html;
			index index.html index.htm;
			proxy_set_header  X-Forwarded-For $remote_addr;
			proxy_set_header  X-Forwarded-Host $server_name;
			proxy_set_header Host $host;
			proxy_pass http://localhost:58080;
            proxy_connect_timeout   18000;
            proxy_send_timeout      18000;
            proxy_read_timeout      18000;
		}
	}
}
```

## 监听同一域名下的 /api/ 和 /
```conf
server {
	listen 443 ssl;
	server_name xiaoyougaotou.com;
	ssl on;
	ssl_certificate xygt.crt;
	ssl_certificate_key xygt.key;
	ssl_session_timeout 5m;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers ECDH:AESGCM:HIGH:!RC4:!DH:!MD5:!aNULL:!eNULL;
	ssl_prefer_server_ciphers on;

	location /api/ {
		root html;
		index index.html index.htm;
		proxy_set_header  X-Forwarded-For $remote_addr;
		proxy_set_header  X-Forwarded-Host $server_name;
		proxy_set_header Host $host;
		rewrite ^/api/(.*)$ /$1 break;
		proxy_pass http://localhost:8765;
	}

	location / {
		root html;
		index index.html index.htm;
		proxy_set_header  X-Forwarded-For $remote_addr;
		proxy_set_header  X-Forwarded-Host $server_name;
		proxy_set_header Host $host;
		proxy_pass http://localhost:6688;
	}
}

```