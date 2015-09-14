title: CSS3 Transition属性
date: 2015-04-14 09:20:50
tags:
- CSS3
- transition
- 渐变
categories:
- WEB前端

---

CSS3 Transition是一个很好用的属性，它允许我们把过渡变化效果应用到普通的CSS属性上。利用这个属性可以制作出一些精致的动态效果，可以很有效地提高前端的用户体验。

<!-- more -->

CSS3的transition属性顾名思义是用来定义渐变过渡效果的规则，使用这个属性，结合css的伪类选择器，使一些以前只能通过flash实现的效果现在只需要几行CSS代码就可以实现了，非常方便和低成本。例如一些炫酷的按钮特效，这里有一个简单的效果实例：
[http://jsfiddle.net/bpu21ou8/](http://jsfiddle.net/bpu21ou8/ "利用transition制作的按钮实例")

## transition用法 ##


    transition: property duration function deplay

例如：    

    div {
		width:100px;
		transition: width 2s;
		-moz-transition: width 2s; /* Firefox 4 */
		-webkit-transition: width 2s; /* Safari 和 Chrome */
		-o-transition: width 2s; /* Opera */
	}

transition 属性是一个简写属性，用于设置四个过渡属性：

- transition-property: 需要实现过渡效果的属性
- transition-duration: 过渡的持续时间
- transition-timing-function: 过渡的时间变化函数
- transition-delay: 过渡效果延迟执行时间

简单来说，transition-property指定的属性，在发生变化时，会依照transition-timing-function指定的渐变效果缓慢变化，持续时间是transition-duration，如果指定了transition-delay，那么变化会在deplay长的时间后发生。

这里是一个简单的示例： [http://jsfiddle.net/4ghjk2cg/](http://jsfiddle.net/4ghjk2cg/ "hover渐变宽度")


## transition-timing-function ##

transition-timing-function有五种常用的函数：

- linear: 线性匀速渐变
- ease: 先加速后减速
- ease-in: 减速进入
- ease-out: 减速退出
- ease-in-out: 减速进入，接着加速，最后减速退出

另外，我们还可以利用cubic-bezier函数自定义贝塞尔曲线。贝塞尔曲线是应用于二维图形的数学曲线，一些矢量图形编辑软件，如Adobe Illustrator，都是靠贝塞尔曲线来精确绘制曲线。一条贝塞尔曲线由四个关键的点决定，如下图：

![](http://7x00n0.com1.z0.glb.clouddn.com/image_20150414201625.png)

CSS3的cubic-bezier函数接受四个参数值，分别对应图中P1，P2的x,y坐标，即cubic-bezier(p1.x, p1.y, p2.x, p2.y)。而P0和P3则固定在坐标抽的原点和右上角。

所以通过调整cubic-bezier函数的参数值，我们可以绘制任意形状的曲线，而transition过渡效果变化的加速度，就是由这条曲线来决定的。也就是说，曲线越陡，表示变化的速度越快，反之，曲线越平缓，变化的速度就越慢。

之前提到的五个常用的transition-timing-function也可以用具体的贝塞尔参数值来实现：

- linear: cubic-bezier(0,0,1,1)
- ease: cubic-bezier(0.25,0.1,0.25,1);
- ease-in: cubic-bezier(0.42, 0.0, 1.0, 1.0)
- ease-out: cubic-bezier(0.0, 0.0, 0.58, 1.0)
- ease-in-out: cubic-bezier(0.42, 0.0, 0.58, 1.0)

cubic-bezier可实现任意我们想要的变化效果，唯一的缺点就是我们没办法准确把握这四个参数值，而[http://cubic-bezier.com/](http://cubic-bezier.com/ "获取贝塞尔曲线参数") 提供了可视化的方式帮我们解决了这个问题，跪谢啊~

## 浏览器支持情况 ##

Internet Explorer 10、Firefox、Opera 和 Chrome 支持 transition 属性。
Safari 支持替代的 -webkit-transition 属性。