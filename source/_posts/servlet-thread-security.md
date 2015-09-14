title: Servlet的线程安全问题
date: 2014-04-13 14:59:03.0
tags:
- Servlet线程安全
categories:
- 并发编程

---

Servlet是线程安全的吗？  答案是否定的。这是由于Servlet在tomcat中是以单例模式存在的，也就是说，当客户端第一次请求servlet，tomcat容器会根据web.xml生成对应的servlet实例，当客户端第二次请求同一个servlet，tomcat容器不会再生成新的。因此，不同请求共享同一个servlet实例的资源，从而导致线程不安全的问题。 Servlet...

<!-- more -->

### Servlet是线程安全的吗？ ###

  答案是否定的。这是由于Servlet在tomcat中是以单例模式存在的，也就是说，当客户端第一次请求servlet，tomcat容器会根据web.xml生成对应的servlet实例，当客户端第二次请求同一个servlet，tomcat容器不会再生成新的。因此，不同请求共享同一个servlet实例的资源，从而导致线程不安全的问题。

 

### Servlet容器如何处理多个请求？ ###

  Servlet容器通过维护一个线程池来处理多个请求，线程池中维护的是一组工作者线程（Worker Thread）。Servlet容器通过一个调度线程（Dispatcher Thread）来调度线程池中的线程，如图所示。

 ![Image 1][]

  当客户端的servlet请求到来时，调度线程会从线程池中选出一个工作者线程并将请求传递给该线程，该线程就会执行对应servlet实例的service方法。同样，当客户端发起另一个servlet请求时，调度线程会从线程池中选出另一个线程去执行servlet实例的service方法。Servlet容器并不关心这些线程访问的是同一个servlet还是不同的servlet，当多个线程访问同一个servlet时，该servlet实例的service方法将在多个线性中并发执行。

 

### Servlet的线程安全问题 ###

  Servlet是线程不安全的，当多个线程访问同一个servlet实例，就会由于共享资源而导致线程不安全问题，例如一个线程修改覆盖了另一个线程的资源。

  我们可以模拟一个servlet线程不安全的示例，编写以下servlet程序，

    package org.cc.servlet;
    
    import java.io.IOException;
    import java.io.PrintWriter;
    
    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    
    public class ThreadServlet extends HttpServlet {
    	private String user;
    
    	/**
    	 * The doGet method of the servlet.
    	 */
    	public void doGet(HttpServletRequest request, HttpServletResponse response)
    			throws ServletException, IOException {
    
    		response.setContentType("text/html");
    		PrintWriter out = response.getWriter();
    		user = request.getParameter("user");
    		try {
    			Thread.sleep(5000);
    		} catch (InterruptedException e) {
    			// TODO Auto-generated catch block
    			e.printStackTrace();
    		}
    		out.println("I am " + user);
    	}
    
    	/**
    	 * The doPost method of the servlet.
    	 */
    	public void doPost(HttpServletRequest request, HttpServletResponse response)
    			throws ServletException, IOException {
    	}
    
    }

  该servlet定义了一个全局变量user，用来接收来自url的参数user，并将其输出到页面上。使用sleep休眠的目的是提高当线程执行时变量被另一个线程修改的几率。

  打开两个浏览器，如果浏览器A先访问[http://localhost:8080/MultiThread/ThreadServlet?user=charles][http_localhost_8080_MultiThread_ThreadServlet_user_charles]， 浏览器B同时访问[http://localhost:8080/MultiThread/ThreadServlet?user=echo][http_localhost_8080_MultiThread_ThreadServlet_user_echo], 我们很容易会发现，浏览器A输出的是"I am echo"，即A显示了B的访问结果。

 

### 编写线程安全的servlet ###

1. 保证变量的线程安全

（1）尽量使用局部变量

我们知道局部变量是在堆栈中分配内存空间的，而每个线程都有维护一个自己的堆栈，不同线程不能访问彼此的堆栈数据，所以不同线程之间不共享局部变量。

（2）使用synchronized同步块

如果一定要使用全局变量，可以将对其的读写操作置于synchronized同步块中，这样不同线程排队依次执行该代码块，从而避免线程不安全情况发生。

 

2. 保证web对象的属性的线程安全

（1）HttpContext对象是线程不安全的，多个线程可以同步对其属性进行读写，可以将读写操作置于同步块中

（2）HttpSession对象是线程不安全的，session对象在同一个会话过程中是以单例模式存在，所以也是线程不安全的，同样也可以将对其属性的读写置于同步块中

（3）HttpRequest对象是线程安全的，每次客户端的请求都会生成一个新的request对象，不同线程使用不同的request实例

 

3. 使用同步的集合类

用Vector代替ArrayList，用HashTable代替HashMap

 

4. 保证外部资源的线程安全

若servlet执行过程中有对外部资源（例如文件）的访问，需要将访问操作置于同步代码块中

 

5. 实现ThreadMode接口

若servlet实现了ThreadMode接口，servlet容器可以保证在同一时刻仅有一个线程执行servlet的service方法。相当于将整个service方法置于同步块中，效率比较低下，所以在servlet规范中已经被抛弃


[Image 1]: 
[http_localhost_8080_MultiThread_ThreadServlet_user_charles]: http://localhost:8080/MultiThread/ThreadServlet?user=charles
[http_localhost_8080_MultiThread_ThreadServlet_user_echo]: http://localhost:8080/MultiThread/ThreadServlet?user=echo