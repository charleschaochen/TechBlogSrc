title: 异步请求代理ajaxProxy
date: 2015-05-27 17:46:00
tags:
- ajax
- block ajax
- ajax chain
categories:
- WEB前端

---

当前web系统的业务逻辑越来越多地向前端倾斜，前端负责的异步请求也越来越多，在日常业务需求开发中，执行异步请求时遇到的问题不少。有两个问题是我经常会遇到的。第一，前端并发触发异步请求，导致请求之间相互影响。第二，异步请求都是通过回调来完成，这必定会导致回调的多层嵌套，形成所谓的回调函数金字塔，这样的代码很丑陋也十分难以维护。ajaxProxy是一个异步请求的代码发送器，提供一种可读性更好的接口来实现异步请求的流程控制，并且提供了阻塞式请求，在执行这类请求前会将页面上其他并发请求中止掉。


<!-- more -->


### 为什么设计ajaxPorxy ###


正如摘要所将，ajaxProxy是一个ajax代理管理器，主要用来管理多个ajax请求，主要两类异步请求的问题：

1.丢弃请求的阻塞。如果页面某个事件会触发异步请求，且用户在页面上频繁触发这类事件，就会导致多个请求并发进行，有可能导致前面请求的回调影响到后面请求的回调，事实上前面N次请求应当被丢弃中止。

举个简单的例子，页面上有多个tab，不同tab点击后会触发不同的异步请求，请求的结果是修改页面的某部分的内容。假设用户在前段频繁切换这些tab，如果有些请求响应较慢，就有可能出现页面内容更新出错。

2.请求排队。在实际开发过程中经常会遇到，多个异步请求需要按次序依次执行，最直接的方式就是将一个ajax请求写在另一个ajax的回调中，层层嵌套，这就会引起所谓的回调金子塔，不利于代码的维护。

ajaxProxy提供了简单的接口来解决以上提到的两类问题，使得ajax请求更加场景化，代码可读性更好，更易于维护。



### ajaxProxy的实现原理和调用方式 ###


**阻塞式请求**

ajaxProxy提供了blockReq函数实现对已丢弃请求的阻塞，且支持多条队列，只有同一队列中的请求才会互相阻塞，所以我们可以在页面的不同区域使用不同的队列，不同区域的请求互不干扰，统一区域的请求互相阻塞。

实现的原理是，调用blockReq时，扩展config对象的beforeSend和complete函数，在请求执行之前，将请求的xhr对象加入对应的队列，当请求完成后，将xhr对象移出对应的队列。执行ajax之前，将对应队列中所有未完成的xhr阻塞（通过调用abort函数）。

blockReq调用方式：

引入ajaxProxy文件，

    ajaxProxy.blockReq({
     url: "",
     data: {},
     success: function(data){
      }
	}, sQueueName)

如果多个请求指定了同一个sQueueName，那么后发起的请求会阻塞前面的请求。

**链式请求**

ajaxProxy实现了类似then.js的异步请求的流程控制。实现的原理很简单，每次加入一个请求，就将该请求的配置加入一个队列，并在该请求上注册complete函数，当请求完成时，从队列中取出下一个请求继续执行，直到队列为空。

req调用方式：

引入ajaxProxy文件，

	ajaxProxy.req({
	     url: "",
	     data: {},
	     success: function(data){
	      }
	}).req({
	     url: "",
	     data: {},
	     success: function(data){
	      }
	}).req({
	     url: "",
	     data: {},
	     success: function(data){
	      }
	}).invoke();

这样的代码看到舒服得多，而且可读性比较好，请求的次序一眼明了。

ajaxProxy的代码在github上开源： [https://github.com/charleschaochen/ajaxProxy](https://github.com/charleschaochen/ajaxProxy)

代码比较简单，有兴趣的童鞋可以稍微花几分钟看看，有什么建议也可以评论告诉我哦