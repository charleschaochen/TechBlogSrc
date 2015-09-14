title: JDK监控和故障处理工具 (II)
date: 2014-09-14 16:27:28.0
tags:
- JVM
- 性能调优
categories:
- Java虚拟机

---

JHAT（1）jhat工具的作用与Eclipse Memory Analysis工具类似，都是用于分析heapdump文件，jhat工具生成的报告相对简陋一些，没有Eclipse MAT那么详细。（2）jhat的使用jhat <heapdump文件路径>指定jhat后会提示：Started HTTP server on port 7000Server is readyjhat会启动一个...

<!-- more -->

### **JHAT** ###

（1）jhat工具的作用与Eclipse Memory Analysis工具类似，都是用于分析heapdump文件，jhat工具生成的报告相对简陋一些，没有Eclipse MAT那么详细。

  


（2）jhat的使用

jhat <heapdump文件路径>

指定jhat后会提示：

Started HTTP server on port 7000Server is ready

jhat会启动一个服务器并监听端口7000，我们可以在浏览器中访问 **http://localhost:7000**，查看heapdump文件的分析报告

从jhat生成报告以package为单元显示当前JVM正在执行的类，同时提供了一些查询，可以查看当前JVM堆的histogram（与jmap -histo功能相同）、等待finalizer线程执行finalize方法的实例等等。jhat也支持OQL（对象查询语言）查询。

我们可以结合hostogram和OQL来分析内存泄露问题。

  


（3） OQL

OQL是一种语法类似SQL的对象查询语言，用于查询堆中的对象信息

一般格式为：

select <目标属性或内建函数返回值> from <类名，正则表达式，地址> where <条件表达式>

  


下面列出几个查询的例子：

select \* from java.lang.String： 查询当前堆中所有String实例的信息

select toString(s) from java.lang.String s： 查询当前堆中所有String实例的字符串内容

select \* from "java.lang..\*"： 查询当前堆中类型为java.lang包下任意类型的对象实例信息

select \* from instanceof jvm.test.Person： 查询当前堆中Person以及Person的子类的实例信息

select p.@name, p.@getAge() from jvm.test.Person p： 查询当前对中Person实例的name属性和getAge方法返回值

  


OQL常用的内建函数：

toString(object)： 相当于object.toString()

dominators(object)：返回直接持有指定对象的对象列表

dominatorof(object)：返回直接持有指定对象的对象列表，如果没有返回-1

inbounds(object)：获取对象的内部引用

outbounds(object)：获取对象的外部引用

classof(object)：获取对象的类型对象

###     ###

### **JSTACK** ###

（1）jstack（Stack Track for Java）用来生成当前虚拟机进程的线程快照（一般称为threaddump或javacore文件）。

**所谓的线程快照指的是线程中正在执行的方法堆栈集合。线程快照常用来定位线程发生长时间停顿的原因，用jstack查看各个线程的调用堆栈，就可以知道线程到底在后台做些什么，或正在等待什么资源。**

JDK1.5之后Thread类提供了getAllStackTrace方法来获取当前JVM进程中各个线程的方法调用堆栈。我们通过以下程序打印JVM所有线程的调用堆栈：

    package jvm.threadstack;
    
    import java.util.Map;
    import java.util.Map.Entry;
    
    /**
     * Print stacks of threads currently
     * 
     * @author Charles Chen
     * @date 2014年9月14日
     */
    public class ThreadStackTrace {
    
    	public static void main(String[] args) {
    		// TODO Auto-generated method stub
    		Map<Thread, StackTraceElement[]> stacktraces = Thread
    				.getAllStackTraces();
    		for (Entry<Thread, StackTraceElement[]> stacktrace : stacktraces
    				.entrySet()) {
    			Thread thread = stacktrace.getKey();
    			// Filter current thread
    			if (Thread.currentThread().equals(thread)) {
    				continue;
    			}
    			System.out.println("线程：" + thread.getName());
    			StackTraceElement[] elems = stacktrace.getValue();
    			for (StackTraceElement elem : elems) {
    				System.out.println(" " + elem);
    			}
    			System.out.println();
    		}
    	}
    }

  


我们也可以利用Thread.getAllStackTrace方法，在服务器部署一个页面用来监控当前服务器虚拟机进程中所有线程的调用堆栈信息。

这里我写了一个JSP页面，只要刷新就可以查看当前服务器所有线程的实时堆栈信息，如图：

![Image 1][]  


  


有需要的可以到我的Github上下载，直接部署在应用上就可以了。 [https://github.com/charleschaochen/ServerThreadStack][https_github.com_charleschaochen_ServerThreadStack]

  



[Image 1]: 
[https_github.com_charleschaochen_ServerThreadStack]: https://github.com/charleschaochen/ServerThreadStack