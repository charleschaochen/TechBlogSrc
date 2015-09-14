title: jQuery图片延迟加载插件
date: 2015-05-04 09:55:50
tags:
- jQuery
- lazyload
categories:
- WEB前端

---

jquery.lazyload是一个实现图片延迟加载的jQuery 插件，它可以延迟加载长页面中的图片。在浏览器可视区域外的图片在初始状态下不会被载入，直到用户将页面滚动到它们所在的位置。这与图片预加载的处理方式正好是相反的。

在包含很多大图片长页面中延迟加载图片可以加快页面加载速度。浏览器将会在加载可见图片之后即进入就绪状态。在某些情况下还可以帮助降低服务器负担。


<!-- more -->

### 如何使用 ###

引入js文件

    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.lazyload/1.9.1/jquery.lazyload.min.js"></script>

修改图片img标签的src属性，由指向实际的图片修改为指向一个图片占位符，这里推荐一个在线获取图片占位符的站点 > [http://placehold.it/](http://placehold.it/ "获取图片占位符")。
然后用data-original属性指定真实的图片地址：

    <img class="lazy" src="images/placeholder.gif" data-original="images/example.png"  width="600"/">

调用lazyload：

    $("img.lazy").lazyload();
    
图片的占位符除了可以通过src属性来指定，也可以通过lazyload函数的参数来指定：

    $("img.lazy").lazyload({
		placeholder: "images/placeholder.gif"
	});
    

### 占位图片事件触发加载 ###

如果我们希望滚到到图片的位置后，还要通过click或hover事件来唤醒图片的加载，我们可以指定lazyload函数的event属性：

    $("img.lazy").lazyload({
		placeholder: "images/placeholder.gif",
		event: "click"
	});

### 图片显示效果 ###
当图片完全加载的时候，插件默认地使用show()方法来将图显示出来。你也可以使用其他的效果，如fadeIn：

    $("img.lazy").lazyload({
		effect: "fadeIn"
	});
    

### 提前加载图片 ###
lazyload插件默认用户滚动到图片位置时才触发加载图片，如果我们希望滚动到距离图片一定位置就触发加载，可以指定lazyload函数的threshold参数：
    $("img.lazy").lazyload({
		threshold: 200
	});
那么，当用户滚动到距离图片200像素时图片就开始加载了。

### 指定加载的容器 ###

lazyload还可以应用到具有滚动条的div容器里，此时调用lazyload函数就需要指定一个container：

    $("img.lazy").lazyload({         
     	container: $("#container")
	});

### 图片不按顺序排列 ###
lazyload的实现原理是，在页面滚动时，会搜索未加载的图片，如果图片在可视范围内就加载，默认情况下当找到第一张不在可见区域的图片时就会停止搜索。而搜索的顺序就是HTML文档中dom节点的定义顺序，所以如果有些图片定义在前面，但由于页面猥琐的布局图片将会呈现在后面，那么就会导致图片没办法正常显示。

此时我们可以通过lazyload函数的failure_limit参数：

    $("img.lazy").lazyload({         
     	failure_limit: 10
	});

这样一来，插件会搜索至少10个未加载的图片才停止搜索，如果你的图片布局比较不规则，可以尝试将这个参数调大。


插件使用示例： 

[https://github.com/charleschaochen/charleschaochen.github.io/blob/master/examples/lazyload/lazyload.html](https://github.com/charleschaochen/charleschaochen.github.io/blob/master/examples/lazyload/lazyload.html "jquery lazyload插件使用示例")