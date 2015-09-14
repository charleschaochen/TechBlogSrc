title: 纯CSS实现的聊天气泡
date: 2015-03-04 20:29:41
tags:
- CSS
- 聊天气泡
categories:
- WEB前端

---

在实现web设计稿的时候，对于一些相对没那么规则的显示，我们往往会使用图片的方式来解决。所以很多时候我们需要加载很多图片在支持一个设计相对比较美观的页面。其实一些设计我们完全可以使用纯CSS来实现，以减少图片的加载，例如类似聊天气泡等带有一个小尾巴的容器。

<!-- more -->

遇到像三角形、菱形、梯形、原型这种部分在HTML看来不太规则的图形，很多时候我们直接使用图片，其实CSS可以发挥的有很多，巧妙使用CSS提供的各种属性可以让我们的页面更加优雅。
就类似下图这种聊天气泡，完全可以使用纯CSS来完成。

![聊天气泡](http://7x00n0.com1.z0.glb.clouddn.com/image_20150304201632.png)

这里我想分享两种实现类似聊天气泡小尾巴的纯CSS解决方案。

#### border边框法实现 ####

HTML代码：

    <div class="bubble-container">
    气泡内容
    <div class="bubble-tail"></div>
</div>

    
CSS：

    .bubble-container {
    height: 100px;
    line-height: 100px;
    margin-bottom: 2em;
    padding-left: 2em;
    background: #3EC22D;
    position: relative;
    font-family: "微软雅黑";
    color: white;
    border-radius: 10px;
    }
    
    .bubble-container .bubble-tail {
    width: 0px;
    height: 0px;
    position: absolute;
    left: 50px;
    bottom: -30px;
    border-width: 15px;
    border-style: solid;
    border-color: #3EC22D transparent transparent transparent;
    }
    
    
    
这种方式主要利用的是border属性，将div的border设得宽一些，然后隐藏左右和下边框，只保存下边框：

    border-color: #3EC22D transparent transparent transparent;

这样就能显示三角形的形状。同样的道理，如果显示上边框和有边框，就可以得到如下图的效果：

![聊天气泡](http://7x00n0.com1.z0.glb.clouddn.com/image_20150304201633.png)

马上在线试试： [http://jsfiddle.net/charleschaochen/p6feeyc3/](http://jsfiddle.net/charleschaochen/p6feeyc3/ "http://jsfiddle.net/charleschaochen/p6feeyc3/")

#### 字符法实现 ####

要显示类似三角形的形状，最直接的方式是借助特殊字符，例如◆。

HTML：

    <div class="bubble-container">
    Let's chart
    <div class="bubble-tail-char">◆</div>
    </div>

CSS:
    
    .bubble-container {
    height: 100px;
    line-height: 100px;
    padding-left: 2em;
    background: #3EC22D;
    position: relative;
    font-family: "微软雅黑";
    color: white;
    border-radius: 10px;
    }
    
    .bubble-container .bubble-tail-char {
    width: 30px;
    height: 30px;
    font-size: 60px;
    line-height: 0;
    color: #3EC22D;
    position: absolute;
    left: 60px;
    bottom: -30px;
    }
    

这种方式主要利用特殊字符结合line-height属性来实现。

马上在线试试：[http://jsfiddle.net/charleschaochen/dq11v2do/](http://jsfiddle.net/charleschaochen/dq11v2do/ "http://jsfiddle.net/charleschaochen/dq11v2do/")

CSS能发挥的东西还有很多，LZ也在慢慢摸索中。