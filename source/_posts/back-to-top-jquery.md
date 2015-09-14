title: 基于JQuery的返回顶部快捷工具
date: 2014-05-26 11:40:54.0
tags:
- 返回顶部
- JQuery
categories:
- 技术研发

---

如果当前页面过长，当滚动到底部时，通过返回顶部按钮可以快速滚回顶部，省去向上滚动页面的人工操作。backTop.js脚本实现了在页面上快捷地创建返回顶部按钮，实现效果可以滚动一下本页面看看，github地址 >>如何使用backTop.js的使用十分简单1.  将images目录下的图片拷贝到与页面同级的images目录下，图片是作为返回顶部按钮的图标使用2.  在...

<!-- more -->

如果当前页面过长，当滚动到底部时，通过返回顶部按钮可以快速滚回顶部，省去向上滚动页面的人工操作。

backTop.js脚本实现了在页面上快捷地创建返回顶部按钮，实现效果可以滚动一下本页面看看，[github地址 >>][github_]

  


**如何使用**

backTop.js的使用十分简单

1.  将images目录下的图片拷贝到与页面同级的images目录下，图片是作为返回顶部按钮的图标使用

2.  在页面中引入jquery和backTop脚本

    <script type="text/javascript" src="scripts/jquery-1.9.1.js"></script>
    <script type="text/javascript" src="scripts/backTop.min.js"></script>

3. 调用backTop对象的createBackTop方法创建返回顶部按钮

    $(function () {
              // Create back to top icon
              backTop.createBackTop();
          });

方法参数有：

    backTop.createBackTop({
      bottom: "50px", // The bottom of the icon, default 50px
      right: "200px", // The right of the icon, default 40px
      style: 0  // The icon style, values: 0, 1, 2, default 0 
    });

bottom： 按钮离页面底部的像素值

right： 按钮离页面右侧的像素值

sytle： 按钮样式，可选值有0, 1， 2。 

  0： 普通的三角形标志样式  ![Image 1][]，不需要图片

  1： 样式 ![Image 1][] ， 需要图片backtop.gif

  2： 火箭图标样式 ![Image 1][] ， 需要图片rocket.png

  


就是简单的3个步骤就可以在页面上创建一个返回顶部的图标，实现快速返回顶部操作

  


**实现原理**

1.  在页面上创建返回顶部图标，默认隐藏。backTop.createBackTop()实现图标的创建

2.  捕捉页面scroll事件， 在事件中判断当前滚动的高度，若超过一定高度，显示返回顶部图标。backTop.onScroll()实现了对滚动高度的检查

    /**
         * Show or hide back to top icon while scrolling
         * @param id
         * @param maxHeight
         */
        onScroll: function (id, maxHeight) {
            // Get height after scrolling
            var scroll_height = document.documentElement.scrollTop + document.body.scrollTop;
    
            if (isNaN(maxHeight)) {
                maxHeight = 200;    // Default 200
            }
    
            if (scroll_height > maxHeight) {
                // If scroll height is larger than maxHeight, show the back to top icon
                $("#" + id).fadeIn(); // Fade in
            } else {
                // If scroll height is smaller than maxHeight, hide the back to top icon
                $("#" + id).fadeOut(); // Fade out
            }
        },

将该事件绑定到当前页面

    $(window).scroll(function () {
             t.onScroll(this.backTopid, 200);
    });

  


3.   绑定返回顶部图标点击事件，点击后返回设置当前页面scrollTop属性

    /**
     * Scroll to the top of the page
     */
    scrollToTop: function () {
    	$("html,body").animate({scrollTop: "0px"}, 200);
    }

  


  



[github_]: https://github.com/charleschaochen/BackTop
[Image 1]: 