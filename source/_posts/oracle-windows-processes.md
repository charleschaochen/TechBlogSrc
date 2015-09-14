title: Oracle数据库Windows后台服务进程
date: 2014-01-09 15:06:27.0
tags:
- Oracle
categories:
- 数据库技术

---

在WIndows平台上，Oracle数据服务一般会在后台产生6种进程，如图所示： 1. OracleService实例名数据库实例的数据库服务。  2. OracleJobScheduler实例名数据库实例的作业调度服务。  3. OracleDBConsole实例名数据库实例的控制台服务，运行Enterprise Manager的时候需要启动该服...

<!-- more -->

在WIndows平台上，Oracle数据服务一般会在后台产生6种进程，如图所示：

![Image 1][]

 

**1. OracleService实例名**

数据库实例的数据库服务。

 

 

**2. OracleJobScheduler实例名**

数据库实例的作业调度服务。

 

 

**3. OracleDBConsole实例名**

数据库实例的控制台服务，运行Enterprise Manager的时候需要启动该服务。

 

**4. OracleOraDb\*\*\_homeTNSListener**

Oracle数据监听服务，用来监听客户端的数据库连接，并把连接转发到数据库服务器。

 

**5.  OracleOraDb\*\*\_homeClrAgent**

该服务用来开启、配置和关闭exrproc进程。

.NET平台上，.net程序集会被拷贝到ORACLE\_HOME/BIN/CLR目录下，并加载到extproc进程中运行，当调用.net存储过程时，oracle将与这个进程进行通信，获取传入参数并执行检索。

 

 

**6. Oracle 实例名 VSS Writer Service**

VSS（卷映射拷贝）是windows平台的基础服务，一个映射拷贝就是一个连续的数据时间点映像。Oracle VSS Writer Service支持Oracle数据库在windows平台上创建这些数映射拷贝。

  


  


  



[Image 1]: 