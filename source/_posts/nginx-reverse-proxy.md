title: Nginx配置反向代理和负载均衡
date: 2016-05-31 16:56:00
tags:
- nginx
- 反向代理
- 负载均衡
categories:
- web容器

---

Nginx作为当下比较流行的web容器，最重要的功能之一就是反向代理。Nginx提供简单的配置来实现反向代理，以及负载均衡。

<!-- more -->

### 什么是反向代理 ###

反向代理（Reverse Proxy）方式是指以代理服务器的身份接受客户端的请求，然后将请求转发给内部网络上的服务器，并将从其他服务器上得到的结果返回给客户端。简单来说，反向代理实现了请求的重定向。

Nginx是一个静态的web容器，只能处理静态资源的请求，动态的服务器请求一般交给其他web容器如apache，tomcat等。而nginx提供一个代理，可以将客户端的某些请求重定向到其他web容器。


### 为什么使用反向代理 ###

使用反向代理，使得客户端无需显式指定动态服务端接口的url，可以像访问静态文件一样访问服务端接口。
反向代理解决了：

（1）后端动态服务器的切换不会影响到客户端

（2）前端跨域问题


### 如何配置反向代理 ###

以下是个配置实例：

	location /api {
        # 设置主机头和客户端真实地址，以便服务器获取客户端真实IP
        proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # 禁用缓存
            proxy_buffering off;
        # 反向代理地址
        proxy_pass http://localhost:8080;
    }

所有匹配/api的请求都会重定向到localhost:8080上

### 负载均衡配置 ###

负载均衡是指将客户端的请求分散到多个服务器上，以减轻单个服务器的压力。

Nginx的负载均衡是通过配置upstream的方式。Nginx的upstream默认是以轮询的方式实现负载均衡，这种方式中，每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

另外一种方式是ip_hash：每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。

配置实例：

	upstream backend {
         # 开启ip hash会按ip的hash结果分配
         ip_hash;

         server 192.168.1.251;
         server 192.168.1.252;
         server 192.168.1.247;
     }

location中配置proxy_pass:

	location /api {
        # 设置主机头和客户端真实地址，以便服务器获取客户端真实IP
        proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # 禁用缓存
            proxy_buffering off;
        # 反向代理地址
        proxy_pass http://backend;
    }


以下是实现反向代理将服务端请求转发到其他动态服务器并对动态服务器做负载均衡的完整配置实例：

	#user  nobody;
	worker_processes  1;
	
	error_log   /var/log/nginx/error.log;
	#pid        logs/nginx.pid;
	
	
	events {
	    worker_connections  1024;
	}


	http {
	    include       mime.types;
	    default_type  application/octet-stream;
	
	    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
	                      '$status $body_bytes_sent "$http_referer" '
	                      '"$http_user_agent" "$http_x_forwarded_for"';
	
	    access_log  /var/log/nginx/access.log  main;
	
	    sendfile        on;
	    #tcp_nopush     on;
	
	    #keepalive_timeout  0;
	    keepalive_timeout  65;
	
	    #gzip  on;
	
	    upstream backend {
	    ip_hash;
	        server 127.0.0.1:8080;
	    }
	
	    server {
	        listen       80;
	        server_name  localhost;
	
	        #charset koi8-r;
	
	        #access_log  logs/host.access.log  main;
	
	    location / {
	        root   html;
	            index  index.html index.htm;
	    }
	
	        location /api {
	        # 设置主机头和客户端真实地址，以便服务器获取客户端真实IP
	        proxy_set_header Host $host;
	            proxy_set_header X-Real-IP $remote_addr;
	        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	
	        # 禁用缓存
	            proxy_buffering off;
	        # 反向代理地址
	        proxy_pass http://backend;
	        }
	    }
	
	}
	include servers/*;
