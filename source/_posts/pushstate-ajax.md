title: 实现异步请求ajax的前进和后退 - PJAX
date: 2015-04-14 09:20:50
tags:
- pjax
- pushState
- HTML5
categories:
- WEB前端

---

我们知道ajax可以实现页面内容的无刷新更新，但页面更新之后如果想返回到之前的状态，传统的ajax是无法实现的。

例如，在一个查询页面上，为了改善用户体验，用户的查询使用了异步请求改变页面上的查询结果。经过若干次查询之后，用户尝试点击浏览器的回退按钮想回到上一次的查询结果，可想而知结果是回到了之前访问的站点，而不是该页面上一次的查询结果，类似这样的需求经常会遇到。难道鱼与熊掌真的不可兼得？HTML5的出现让一切变得十分简单。


<!-- more -->

## window.history ##
在此之前，我们先大概了解一下window.history对象，稍有前端开发经验的童鞋应该了解，可以忽略绕道。

window.history是浏览器的历史访问对象，存放当前浏览器的访问历史栈。window.history常用的接口有：
    
	 history.back(); // 后退
     history.forward(); // 前进
     history.go(n); // 前进或后退n步，例如history.go(-2)表示返回两步，history.go(1)相当于history.forward()


## pushState ##

在HTML5出现之前，我们无法控制或操作一个浏览器上下文的会话记录，直至HTML5牛逼轰轰地引入了新的API：history.pushState和history.replaceState，通过这个接口可以操作window.history对象的内容，并且做到无刷新改变页面URL。

pushState结合ajax使用，就可以实现异步请求的前进和回退。这就是传说中的PJAX（pushState + ajax）。

PJAX的原理是，在执行ajax请求时，将所需要存储的数据（可以是title，url或请求数据）存入一个state对象，调用pushState函数将state对象push入window.history栈中，pushState函数的调用如下：
    
    pushState(state, title, url)
    
pushState接收一个state对象，该对象是一个可序列化的json对象（因此DOM节点不能作为state对象）。title和url是该历史记录的标题和链接，**调用pushState会将当前页面的标题和url无刷新地更新为指定的title和url**。

replaceState和pushState的调用方式一致，不同在于replaceState会**覆盖**当前的历史记录，而不是push一条新的历史记录。


## popstate event ##

**PJAX通常需要结合window.onpopstate来实现请求回退或前进**。

window.onpopstate会在浏览器回退或前进时触发，当事件被触发时，通过event.state可以获取保存在history栈中的state对象，从state对象中我们就可以获取先前我们通过pushState存入的数据，例如title，url或请求数据，根据这些数据，我们就可以重新发起请求，从而实现了请求的回退或前进。

**简单来说，PJAX就是利用window.history作为存储栈，采用HTML5提供的pushState API，实现通过url来跟踪ajax的请求历史**。

说到这里，有人可能会说，我用一个本地的数组来存储每次ajax请求的记录，不是一样也能实现所谓的ajax回退和前进？没错，使用一个本地数组或localStorage等方法也可以实现，但PJAX的优势在于它能**无刷新修改当前页面的URL**，也就是说我们可以做到，直接访问修改后的URL也可以恢复某次ajax请求的状态，可用性更加好，并且这有利于搜索引擎索引我们的页面。
    

## pjax example ##

上面说了那么多，归根到底pjax就只是做了一件事，用url来跟中ajax的请求历史。

那么应用到现实的需求开发中，使用pjax的关键点可以归纳为2个：


1) 执行ajax请求之后，将查询的必要参数拼接成url参数，生成新的url，同时可以将一些额外的数据封装到state对象中，调用pushState将url压入history。
	
	window.history.pushState({key: value}, title, newUrl);   

2) 绑定popstate事件，在回调中获取state对象，通过location.href可以获取当前url，从url中解析请求参数，再次发起请求，从而实现请求的回退或前进。
    
	// Bind popstate
	window.addEventListener('popstate', function (e) {
	if (history.state) {
		var state = e.state;
		var sUrl = location.href;
		/*
		 issue ajax request
		*/
		}
	}, false);	
    
    
在线实例猛戳这里 >> [http://www.charlestech.info/examples/pjax/pjaxTest.html](http://www.charlestech.info/examples/pjax/pjaxTest.html "http://www.charlestech.info/examples/pjax/pjaxTest.html")

代码： [https://github.com/charleschaochen/charleschaochen.github.io/blob/master/examples/pjax/pjaxTest.html](https://github.com/charleschaochen/charleschaochen.github.io/blob/master/examples/pjax/pjaxTest.html "pjax example")