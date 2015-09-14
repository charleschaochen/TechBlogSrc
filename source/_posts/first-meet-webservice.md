title: 初识Webservice
date: 2014-01-31 11:22:21.0
tags:
- Webservice
categories:
- Webservice

---

1.  什么是Webservice      Webservice是基于web的服务，接受和相应外部系统的请求，为远程系统提供服务，从而实现不同系统之间的交互。举个简单的例子，系统A中需要实现某一个功能，而该功能在系统B中已经实现，并且系统B对外提供该功能的webservice，此时系统A可以向B发送服务请求，系统B处理请求后返回结果给A...

<!-- more -->

## 1.  什么是Webservice ##

      Webservice是基于web的服务，接受和相应外部系统的请求，为远程系统提供服务，从而实现不同系统之间的交互。举个简单的例子，系统A中需要实现某一个功能，而该功能在系统B中已经实现，并且系统B对外提供该功能的webservice，此时系统A可以向B发送服务请求，系统B处理请求后返回结果给A。

      或者如果现在你要做一个app，需要定位当前用户的位置实现LBS服务，这种服务当然不可能也没必要自己去实现，因为已经互联网上已经有很多成熟的LBS服务供调用，这些服务都是webservice。  
 

## 2. Webservice的特点 ##

（1） Webservice以HTTP POST方式传输数据，不用GET  
（2） Webservice采用SOAP协议规范交换数据  
（3） Webservice采用WSDL描述服务接口

  


## 3. Webservice体系结构 ##

（1）Webserive拥有三要素：

1.  SOAP(Simple Object Acess Protocol)，简单数据访问协议。服务请求的协议规范，SOAP定义了一个基于XML的框架，描述通信消息的来源、内容、以及对象等等
2.  UDDI(Universal Description, Discovery and Integration)，通用描述、发现和集成服务。目录服务，提供webservice的注册和发现机制。服务请求客户端向UDDI发送查询请求，UDDI会查询已注册的webservice从而返回具体的服务WSDL。
3.  WSDL(Web Service Description Language)，Web服务描述语言。WSDL是一种基于XML的接口描述语言，用来描述webservice的接口信息等

  


（2）Webservice原理

Webservice架构的系统一般有三个成员：

1.  服务提供者。Web服务的拥有者，对外部系统提供webservice接口
2.  服务中介者。提供web服务的注册和发现服务，一般是UDDI
3.  服务请求者。请求web服务的客户端。

Web service体系架构图：

![df72e5ea-351b-4eb4-8417-82bfab23662a.png][]  


 

  


1.  服务提供者设计好web服务，在UDDI上进行注册，这个过程称为发布
2.  服务请求者向UDDI发送请求，查找指定的webservice
3.  UDDI向server返回服务接口信息(WSDL)给请求者，这个过程称为发现
4.  服务请求者根据WSDL封装请求消息(SOAP)，向指定的服务提供者发送请求，这个过程称为绑定
5.  服务提供者执行服务请求，返回结果给请求者


[df72e5ea-351b-4eb4-8417-82bfab23662a.png]: http://www.charlestech.org/xheditor_img/20140131/df72e5ea-351b-4eb4-8417-82bfab23662a.png