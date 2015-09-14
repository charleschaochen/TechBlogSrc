title: 修改Tomcat的默认根目录
date: 2013-11-27 16:33:25.0
tags:
- J2EE
- Tomcat
categories:
- 技术研发

---

在tomcat服务器上发布J2EE应用后，一般我们通过http://localhost:8080/<AppName>来访问。这种访问方式有个麻烦的地方就是，在应用中我们就不能使用类似"/js/jquery.js"来访问应用中的资源，得用相对路径或使用以下java代码获取应用的绝对路径，String path = request.getContextPath(); ...

<!-- more -->

在tomcat服务器上发布J2EE应用后，一般我们通过http://localhost:8080/<AppName>来访问。

这种访问方式有个麻烦的地方就是，在应用中我们就不能使用类似"/js/jquery.js"来访问应用中的资源，得用相对路径或使用以下java代码获取应用的绝对路径，

    String path = request.getContextPath();
    	String basePath = request.getScheme() + "://"
    			+ request.getServerName() + ":" + request.getServerPort()
    			+ path + "/";

这时候可以考虑将tomcat的默认访问目录设置为当前应用目录，有两种方式：

1、打开$TOMCAT\_HOME/config/server.xml文件，找到<Engine name="Catalina" defaultHost="localhost">标签，在<Host>标签下添加：

<Context docBase="D:\\Development\\TechBlogSpace\\TechBlog\\WebRoot" path="" reloadable="true"></Context>

  


2、在$TOMCAT\_HOME/conf/Catalina/localhost文件夹下部署应用，例如现在要部署TechBlog项目，在该文件夹下创建TechBlog.xml，内容如下，

<?xml version='1.0' encoding='utf-8'?>  
<Context docBase="D:\\Development\\TechBlogSpace\\TechBlog\\WebRoot" path="">  
</Context>  


  


**以上的path属性为应用的访问路径，设置为空表示不使用应用名访问，即设置为tomcat的默认访问目录。**

重启服务器后就可以用http://localhost:8080直接访问了。  
