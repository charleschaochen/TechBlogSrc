title: Intellij Idea远程调试
date: 2016-08-10 19:14:00
tags:
- Intellij Idea
- Java
- 远程调试
categories:
- 技术研发

---

开发过程中远程调试是家常便饭，Idea的调试功能十分丰富，体验也比较好，这里简单介绍一下比如使用idea进行Java工程的远程调试。

<!-- more -->

远程调试需要远程被调试端开启调试端口，客户端进行调试连接。

1. 打开Idea的Run/Debug Configurations窗口，新建一个Remote项。

![](http://7x00n0.com1.z0.glb.clouddn.com/idea_remote_debug_0.png)

2. idea已经帮我们生成了远程JVM的命令行参数，我们只需要直接复制即可，如果不想使用默认的端口，可以自行修改。

-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005

3. 在Settings处添加host和port

4. 远程Java成功启动时添加命令行参数：
	
	`-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005`

如果使用tomcat容器，可修改${TOMCAT_HOME}/bin/catalina.sh，添加到JAVA_OPTS中：

	JAVA_OPTS = "${JAVA_OPTS} -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005"

 5. 点击Idea的debug按钮，如果出现以下提示，说明已经连接成功


![](http://7x00n0.com1.z0.glb.clouddn.com/idea_remote_debug_1.png)

这样就可以体验远程调试拉~