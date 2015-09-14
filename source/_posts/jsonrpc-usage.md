title: JSON-RPC使用及自动加载服务框架
date: 2014-10-03 23:41:11.0
tags:
- JSON-RPC
- RPC
categories:
- Webservice

---

什么是JSON-RPCJson-RPC是以Json作为数据交换格式的远程过程调用协议，利用该协议我们可以实现在不同平台之间进行远程的服务调用。JSON-RPC常用与应用内的模块集成。Json-RPC-Java是用Java实现的一个Json-RPC框架，它允许我们在web容器中搭建远程过程调用服务，并提供Javascript客户端API，实现使用Js远程调用Java方法。如何使用JSON-RPC-J...

<!-- more -->

#### **什么是JSON-RPC** ####

Json-RPC是以Json作为数据交换格式的远程过程调用协议，利用该协议我们可以实现在不同平台之间进行远程的服务调用。JSON-RPC常用与应用内的模块集成。

Json-RPC-Java是用Java实现的一个Json-RPC框架，它允许我们在web容器中搭建远程过程调用服务，并提供Javascript客户端API，实现使用Js远程调用Java方法。

####     ####

#### **如何使用JSON-RPC-JAVA** ####

1. 下载jsonrpc的jar包以及js库文件

2. 在web.xml中配置JSONRPCServlet

     <servlet>
      <servlet-name>JSONRPCServlet</servlet-name>
      <servlet-class>
          com.metaparadigm.jsonrpc.JSONRPCServlet
      </servlet-class>
      <init-param>
          <param-name>servicePath</param-name>
          <param-value>org.cc.service</param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
      <servlet-name>JSONRPCServlet</servlet-name>
      <url-pattern>/JSON-RPC</url-pattern>
    </servlet-mapping>

3. 编写服务逻辑

    package org.cc.service;
    
    public class TestService {
         public String getHello() {
              return "Hello, JSON-RPC";
         }
    
         public String random(int seed) {
              Integer value = Integer.valueOf((int) (Math.random() * (double) seed));
              return value.toString();
         }
    }

  


4. 在JSP页面上调用JSONRPCBridge的API注册服务

    <jsp:useBean id ="JSONRPCBridge" scope="session"
       class="com.metaparadigm.jsonrpc.JSONRPCBridge" />
    <jsp:useBean id ="testService" scope="session"
       class="org.cc.service.TestService" />
    <%
      JSONRPCBridge.registerObject( "testService", testService);
    %>

  


5. 通过js API访问服务器服务

引入js库

    <script type= "text/javascript" src="js/jsonrpc.js"></script>

调用服务器service

    var jsonrpc = new JSONRpcClient( "JSON-RPC" );
    var result = jsonrpc.testService.getHello();
    var result = jsonrpc.testService.random(5);

  


**web容器启动时自动注册服务**

我们可以继承JSONRPCServlet重写其init方法，在它初始化时注册某些服务：

    package org.cc.rpcservlet;
    
    import javax.servlet.ServletConfig;
    import javax.servlet.ServletException;
    
    import org.cc.service.TestService;
    
    import com.metaparadigm.jsonrpc.JSONRPCBridge;
    import com.metaparadigm.jsonrpc.JSONRPCServlet;
    
    public class AutoRegisterJSONRPCServlet extends JSONRPCServlet {
           @Override
           public void init(ServletConfig servletConfig) {
                 /* BEGIN: Register services */
                JSONRPCBridge bridge = JSONRPCBridge. getGlobalBridge();
                bridge.registerObject( "testService", new TestService());
                 /* END: Register services */
          }
    }

  


然后在web.xml中配置重写的servlet

    <servlet>
     <servlet-name> JSONRPCServlet</servlet-name >
     <servlet-class>
          org.cc.rpcservlet.AutoRegisterJSONRPCServlet
     </servlet-class>
     <init-param>
           <param-name> servicePath</param-name >
           <param-value> org.cc.service</param-value >
     </init-param>
     <load-on-startup> 1</ load-on-startup>
    </servlet>
    <servlet-mapping>
     <servlet-name> JSONRPCServlet</servlet-name >
     <url-pattern> /JSON-RPC</url-pattern >
    </servlet-mapping>

  


####     ####

#### **JSON-RPC自动加载服务框架** ####

编写这个框架的目的是为了让应用能够自动动态地加载自定义的服务，而不是每实现一个新的服务都要手动去注册到全局的bridge中。

该框架的核心原理是，继承JSONRPCServlet并重写init方法，在init方法中，根据web.xml中配置的初始化类路径参数，将制定路径下的类注册到全局的JSONRPCBridge中。编写一个自定义的注解，在指定目录下搜索服务时，只有标注了这个注解的类才会被加载并注册到全局bridge中。

框架源代码：[https://github.com/charleschaochen/JSONRPCAutoRegister][https_github.com_charleschaochen_JSONRPCAutoRegister]

  


使用步骤：

1. 引入lib下的jar包：

commons-logging-1.1.3  
jsonrpc-1.0  
jsonrpc-autoregister

  


2. 配置web.xml，指定service的包路径，如下：

    <servlet>
    	<servlet-name>JSONRPCServlet</servlet-name>
    	<servlet-class>
    		org.charlestech.jsonrpc.AutoRegisterJSONRPCServlet
    	</servlet-class>
    	<init-param>
    		<param-name>servicePath</param-name>
    		<param-value>org.cc.service</param-value>
    	</init-param>
    	<load-on-startup>1</load-on-startup>
    	</servlet>
    	<servlet-mapping>
    	<servlet-name>JSONRPCServlet</servlet-name>
    	<url-pattern>/JSON-RPC</url-pattern>
    </servlet-mapping>

  


3. 在指定的包路径下编写service，使用@RPCService注解，如下：

    import org.charlestech.jsonrpc.RPCService;
    
    @RPCService(serviceName = "testService")
    public class TestService {
    	public String getHello() {
    		return "Hello, JSON-RPC";
    	}
    }

  


应用启动后，该service将自动注册到bridge中

  


4. 客户端使用js直接访问service

    var jsonrpc = new JSONRpcClient("JSON-RPC");
    var result = jsonrpc.testService.getHello();
    alert(result);

  


使用这个框架就可以更加方便地使用JSON-RPC，使得客户端可以根据需要简便地“安装”或“卸载”service。


[https_github.com_charleschaochen_JSONRPCAutoRegister]: https://github.com/charleschaochen/JSONRPCAutoRegister