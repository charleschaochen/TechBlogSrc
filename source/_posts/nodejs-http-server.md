title: 使用nodeJs搭建一个简单的HTTP Server
date: 2015-05-04 17:46:00
tags:
- NodeJs
- HttpServer
categories:
- WEB前端

---

NodeJs是一个基于Chrome内核的Javascript执行引擎，它的出现使得javascript可以掉渣天地运行在服务器端，这样一来很多业务逻辑不太复杂的网站完全可以用javascript一站式搭建起来。

NodeJs不仅可以用来编写服务器程序，还可以搭建Http Server，也就是说我们完全可以使用几句简单的js代码搭建起一个非阻塞的http服务器。


<!-- more -->

### Node http库 ###
Node除了用V8引擎来解析 JavaScript外，还提供了高度优化的应用库，用来提高服务器效率。HTTP模块就是一个node库，这个库是用C语言编写的，用于快速搭建非阻塞式http服务器。

我们可以通过require来引入http库：

    var http = require("http");

http的一个重要的API就是：

    http.createServer(function(request, response){
		response.writeHead(200, {'Content-Type': 'text/plain'});
		response.end('I am building a Node Http Server!');
	}).listen(8888);

运行这段简单的代码就启动了一个监听8888端口的http服务器进程！
通过浏览器访问http://localhost:8888/就可以看到：

![](http://7x00n0.com1.z0.glb.clouddn.com/nodeserver.png)

### http服务器逻辑 ###
http服务器就是用来监听、接收和处理客户端请求的。那么要实现一个可用的http服务器，我们就要实现处理客户端请求的逻辑。简单来说，我们要知道客户端请求的目标是什么，然后读取这些资源返回给客户端。

所以，在http.createServer的回调函数内部，我们要对request进行处理，解析请求的url，根据url请求的目标资源，读取资源内容输出到客户端。

解析请求的url可以使用url库：

    var url = require("url");

url的parse函数可以将request.url解析成json对象：

    var oUrl = url.parse(request.url);

解析后的url对象内容包括：

    { protocol: null,
	  slashes: null,
	  auth: null,
	  host: null,
	  port: null,
	  hostname: null,
	  hash: null,
	  search: null,
	  query: null,
	  pathname: '/',
	  path: '/',
	  href: '/' }

通过oUrl.pathname可以获取请求的路径，oUrl.query可以获取请求参数，这样一来就可以知道客户端请求的是哪个资源，请求参数是什么。

### 简单可用的node server示例 ###
差不多久可以开码了。

用webstorm创建一个node工程。这里我将node服务器处理程序和服务器资源组织成如下结构：

![](http://7x00n0.com1.z0.glb.clouddn.com/nodeserver_files.png)

各目录存放的是，

**config**: 存放服务器的配置文件，serverConfig.js包含了服务器的一些基础信息的配置，如端口号，网页资源所在目录，欢迎页面等等，内容如下：

    var serverConfig = {
	    port: "4000",
	    web_root: "../web",
	    welcome_page: "/index.html",
	    error_page: {
	        "404": "/404.html"
	    }
	};


**server**: 存放http服务器的创建和请求处理程序

**web**：网站资源的根目录

主要的代码在于server.js:

    var serverConfig = require("../config/serverConfig.js");
	var http = require("http");
	var url = require("url");
	var fs = require("fs");
	
	// Create a server and start
	var server = http.createServer(function (request, response) {
    var oUrl = url.parse(request.url);
    var sPath = oUrl.pathname;
    var sRoot = serverConfig.config.web_root;
    // If request the home directory, redirect to welcome page
    if (sPath === "/" || /^\/*\/$/.test(sPath)) {
        sPath = serverConfig.config.welcome_page;
    }

    // Read server resource content and output to browser
    fs.readFile(sRoot + sPath, "utf-8", function (err, data) {
        if(err) {
            console.log(err);
            response.writeHead(404);
            // Redirect to 404 page
            fs.readFile(sRoot + serverConfig.config.error_page['404'], "utf-8", function(err,data){
                response.end(data);
            });
            return;
        }

        response.end(data);
    })

	});
	
	server.listen(serverConfig.config.port);

服务器接收到请求后，首先对url进行解析取到请求路径，如果请求的是根目录，则默认重定向到index.html。接着通过fs库的API读取指定资源的内容，如果有错误，则重定向到404页面，最后将数据输出。

程序源代码： [https://github.com/charleschaochen/NodeServer](https://github.com/charleschaochen/NodeServer)