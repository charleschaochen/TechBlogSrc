title: Java会话跟踪技术
date: 2014-04-13 18:24:51.0
tags:
- 会话跟踪
- session
categories:
- 技术研发

---

什么是会话跟踪？  客户端向服务器发起请求并获得响应的全过程称为一次会话。客户端与服务器的通信是基于HTTP协议的，HTTP协议具有无状态的特点，即不能保存会话的信息，也就是说，服务器不能根据HTTP请求判断不同请求来自同一用户还是不同用户。因此需要通过会话跟踪，保存客户端与服务器的通信上下文。  当客户端第一次向服务器发送请求时，服务器会实例化一个HttpSessi...

<!-- more -->

### 什么是会话跟踪？ ###

  客户端向服务器发起请求并获得响应的全过程称为一次会话。客户端与服务器的通信是基于HTTP协议的，HTTP协议具有无状态的特点，即不能保存会话的信息，也就是说，服务器不能根据HTTP请求判断不同请求来自同一用户还是不同用户。因此需要通过会话跟踪，保存客户端与服务器的通信上下文。

  当客户端第一次向服务器发送请求时，服务器会实例化一个HttpSession对象并分配唯一的session id，此后客户端向服务器发送的请求都会附带当前的session id，服务器根据session id区别不同的客户端用户。

 

### 会话跟踪的常用方法 ###

（1） 会话Cookies

HttpSession机制会首先手动Cookie来保存session id。当客户端第一次发送请求时，服务器会实例化一个HttpSession，并将session id存放在客户端浏览器中一个名为JSESSIONID的cookies。

Cookies的保存方式有两种：

  第一种是保存在内存中，那么重启浏览器后会失效，并且只有当前浏览器进程的不同窗口可以共享；

  第二种保存在磁盘中，在有效期内不同浏览器都可以访问。

HttpSession默认将其保存在内存中。

 

（2）URL重写

如果客户端浏览器禁用了Cookies，也可以使用URL重写的方式保存session id。

这里的URL重写是指将session id作为url参数添加到所请求的url中，例如请求的url为[http://www.charlestech.org/blog.shtml][http_www.charlestech.org_blog.shtml]，重写后可能为[http://www.charlestech.org/blog.shtml;jessionid=be8d697876787876befdbde898789098980][http_www.charlestech.org_blog.shtml_jessionid_be8d697876787876befdbde898789098980]。

Servlet程序中使用response对象的encodeURL(String url)和encodeRedirectURL(String url)方法来实现URL的重写，这两个方法都会先判断客户端Cookies是否禁用，如果禁用就添加jsessionid参数，否则返回原url。两者的区别在于：encodeURL可作用于所有的URLs，而encodeRedirectURL只作用于需要传递给sendRedirect方法的URL。

一般情况下，所有请求都应该发送到动态的页面（JSP或Servlet），然后在动态页面中使用encodeURL()和encodeRedirectURL()来添加session id作为url参数，并执行跳转。

 

（3）隐藏表单域

使用<input type="hidden"/>隐藏表单域保存所需要的会话信息。

 

（4）HttpSession

HttpSession对象提供setAttribute和getAttribute方法，允许在会话中添加或获取属性值

 

 


[http_www.charlestech.org_blog.shtml]: http://www.charlestech.org/blog.shtml
[http_www.charlestech.org_blog.shtml_jessionid_be8d697876787876befdbde898789098980]: http://www.charlestech.org/blog.shtml?page=2