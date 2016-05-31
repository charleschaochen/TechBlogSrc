title: CSS Flex布局
date: 2015-12-29 09:20:00
tags:
- flex
categories:
- WEB前端

---

弹性布局（flex ）可以让子元素自适应填充父元素的显示区域，防止在不同设备和屏幕下内容溢出或布局异常。
弹性布局改进了CSS的块模型，方便我们实现一些特殊的布局。

<!-- more -->

传统的块模型，用来display，position，float等属性来实现布局，对于某些特殊的布局并不是很方便。例如水平布局下，如果要让三个子元素平分容器的宽度，我们会设置每个子容器的width:33%以适应不同的屏幕宽度，但如果每个子元素有边框，用百分比来分配宽度将不准确，容易发生溢出。多个子元素水平布局，有某个元素需要垂直居中，用传统的块模型也十分麻烦。

不同于块级元素的垂直方向布局和行内元素的水平方向布局，弹性布局的算法和方向无关。


### flex浏览器兼容情况 ###

flex layout的浏览器兼容性如下：

![](http://7x00n0.com1.z0.glb.clouddn.com/flex-img.png)

详细情况可以参照： [http://caniuse.com/#search=flex](http://caniuse.com/#search=flex "http://caniuse.com/#search=flex")

### flex布局的原理 ###

引用网上一张传统的flex布局示例图：

![](http://7x00n0.com1.z0.glb.clouddn.com/flex-img-1.png)


当父元素指定display属性为flex或inline-flex之后，该容器就实现了弹性布局，容器中所有子元素就是弹性布局项，即图中的flex item。

	.container {
	 display: flex;
	 display: -webkit-flex;
	}

注意：

1. 不同内核浏览器设置弹性布局，是把前缀加在属性值前而不是属性名前。

2. 使用Flex布局以后，子元素的一些布局属性注入float、clear和vertical-align属性将失效。

弹性布局几个重要的概念：主轴(Main Axis，当布局方向为水平时，主轴为水平轴，当布局方向为垂直时，主轴为垂直轴)，交叉轴(Cross Axis)。主轴开始位置和结束位置分别对应图中的main start和main end，交叉轴的开始结束位置为cross start和cross end。

### flex相关属性 ###

作用于父元素的属性有：

flex-direction: 子元素布局的方向，垂直或水平，取值有 row（水平布局）， row-reverse（水平反向布局，即按代码书写顺序从右到左布局），column（垂直布局），column-reverse（反向布局，即按代码书写顺序从下到上布局）垂直

flex-wrap: 当子元素溢出时的换行形式，取值有：nowrap（不换行），wrap（换行），wrap-reverse（换行，首行在下方）

flex-flow: flex-direction和flex-wrap的组合属性

justify-content：定义flex item在主轴上的对齐方式，取值有：flex-start（所有元素左对齐），flex-end（所有元素右对齐），center（所有元素居中对齐），space-between（两端对齐，item之间的间隔相等），space-around（item左右的间隔相等）

align-items：定义flex item在交叉轴上的对齐方式，取值有：flex-start, flex-end, center, stretch（延伸为100%），baseline（所有flex item的第一行文字基准线对齐）


作用于子元素的属性有：

order： 定义元素的排列顺序，值越小越靠前，顺序相同的元素按书写顺序排

flex-grow：元素的放大比例，默认为0，为0时保留元素宽度，当容器有剩余空间时也不会放大。如果元素的flex-grow都为1，元素将平分父元素的宽度，大于1时则按比例划分

flex-shrink：元素的缩小比例，默认为1。如果元素的flex-shrink都为1，当屏幕缩小空间不足时，所有元素都按比例缩小，flex-shrink为0时当空间不足也不会缩小

flex-basis：元素占主轴的空间，可用该属性来取代width或height，主轴根据元素的flex-basis来判断是否有空余空间。

flex: flex-grow，flex-shrink， flex-basis的组合属性

align-self：个别元素在交叉轴的对齐方式，可以覆盖父元素的align-items



