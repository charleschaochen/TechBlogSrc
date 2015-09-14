title: JS Promise规范
date: 2015-08-10 08:16:00
tags:
- promise
- jquery deferred
categories:
- WEB前端

---

在前端开发过程中，我们经常会遇到一些耗时较长的操作，例如异步请求、IO等等，处理这种行为我们通常采用异步回调的方式，为它们指定一个回调函数，当操作完成后自动回调这些函数执行一些行为。

这些耗时较长的操作就是所谓的deferred操作。

<!-- more -->

### Promise规范 ###


1.Promise是ES6标准的规范，用于一些延期(deferred)和异步计算。一个Promise对象其实代表的是一次将要执行的操作。

语法：

	new Promise(executor);
	new Promise(function(resolve, reject) { ... });

executor是一个有两个形参的function，一个是resolve函数，一个是reject函数。

通常我们把执行逻辑放在executor里，当操作执行成功，调用resolve函数，执行失败，调用reject函数。




2.Promise对象状态
 
一个Promise对象具有三种状态：

- pending: 初始状态，等待被执行
- fullfilled: 操作成功执行结束
- rejected: 操作执行失败


![](http://7x00n0.com1.z0.glb.clouddn.com/promise.png)



promise对象的状态，从Pending转换为Fulfilled或Rejected之后，这个promise对象的状态就不会再发生任何变化。

所以fulfilled和Rejected这两个中的任一状态都可以表示为Settled(不变的)。


3.Promise常用原型函数

（1） Promise.prototype.then(onFulfilled, onRejected)

为某个promise对象添加执行成功和失败的回调句柄，返回一个新的promise对象，该promise对象的执行器就是传入的回调函数句柄，回调函数的返回值将会被传入该promise对象的resolve方法中，以此将数据传递到下一个then链节点。

（2） Promise.prototype.catch(onRejected)

为某个promise对象添加执行失败的回调句柄，返回一个新的promise对象。如果想要对操作的异常进行处理，最好在catch的回调中执行。

示例：
	
	var promise = new Promise(function(resolve, reject){
	    resolve(66);
	});
	promise.then(function(value){
	    console.log(value);
	}).catch(function(error){
	    console.error(error);
	});


如果我们希望在then回调中再执行其他的deferred操作，可以手动返回一个Promise对对象，如：

	new Promise(function (resolve, reject) {
	    console.log("Promise 1");
	    setTimeout(function () {
	        resolve("resolved");
	    }, 1000);
	}).then(function (msg) {
	        return new Promise(function (resolve, reject) {
	            console.log("Promise 2");
	            setTimeout(function () {
	                resolve(msg);
	            }, 1000);
	        })
	    }).then(function (msg) {
	        console.log(msg);
	    });



4.Promise静态方法

（1） Promise.all 

接收一个 promise对象的数组作为参数，当这个数组里的所有promise对象全部变为resolved或rejected状态的时候，它才会去调用 .then 方法。

（2） Promise.race

使用方法和Promise.all一样，接收一个promise对象数组为参数。不同的是只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理。

（3） Promise.resolve(value)

返回一个已经resoved的promise对象，resolve传入的参数就是value

（4） Promise.reject(message)

返回一个已经被reject的promise对象，reject传入的参数就是message




### jquery的promise实现 ###

jquery1.5之后引入了deferred对象，作为其核心的回调解决方案。

jquerydeferred对象的使用场景有：


1.ajax请求

jquery ajax的传统语法是：
	
	$.ajax({
	  url: "",
	  data: "",
	  type: "",
	  success: function(){},
	  error: function(){}
	});



引入deferred对象之后，我们可以采用链式语法：
	
	$.ajax("remote.html").done(function(){
	     console.log("执行成功");
	}).fail(function(){
	     console.log("执行失败");
	});


如果请求成功之后，我们希望多个操作能按顺序依次进行，可以任意在以上的回调函数链中再添加回调函数：

	$.ajax("remote.html").done(function(){
	     console.log("执行成功");
	}).fail(function(){
	     console.log("执行失败");
	}).done(function(){
	     console.log("继续执行操作");
	});



2.为多个ajax请求指定同一个回调函数

如果我们希望在一个以上ajax请求完成之后，执行某个回调，可以采用$.when:

	$.when($.ajax("remote0.html"), $.ajax("remote1.html")).done(fuction(){
	     console.log("两次请求成功");
	});




3.为普通的函数实现链式回调

类似Promise的实现，jquery deferred对象也可以为普通函数实现链式回调。

实现方法：

(1) 创建deferred对象：

var deferred = $.Deferred();


(2) 改成deferred对象状态：

deffered.resolve();
或
deffered.reject();


(3) 返回promise对象：
return deferred.promise;
