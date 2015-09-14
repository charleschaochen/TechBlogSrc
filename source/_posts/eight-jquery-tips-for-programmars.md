title: 8个对程序员来说有用的jQuery小贴士和技巧
date: 2014-03-03 18:00:27.0
tags:
- Jquery
categories:
- 技术研发

---

原文出处：http://bbs.html5cn.org/thread-79045-1-1.html1) 禁用鼠标右键单击jQuery程序员可以使用此代码在网页上禁用鼠标右键点击。$(document).ready(function() \{ //catch the right-click context menu $(document).bind("contextmenu&...

<!-- more -->

原文出处：[http://bbs.html5cn.org/thread-79045-1-1.html][http_bbs.html5cn.org_thread-79045-1-1.html]  
  


**1) 禁用鼠标右键单击**

jQuery程序员可以使用此代码在网页上禁用鼠标右键点击。  
  


    $(document).ready(function() {
        //catch the right-click context menu
        $(document).bind("contextmenu",function(e) {                
            //warning prompt - optional
            alert("No right-clicking!");
    
            //delete the default context menu
            return false;
        });
    });

  
  
**2) 利用jQuery调整文字大小**  
使用此代码，用户可以重新[网站][Link 1]上文字的大小（增大和减少）  
  


    $(document).ready(function() {
        //find the current font size
        var originalFontSize = $('html').css('font-size');
        //Increase the text size
        $(".increaseFont").click(function() {
            var currentFontSize = $('html').css('font-size');
            var currentFontSizeNumber = parseFloat(currentFontSize, 10);
            var newFontSize = currentFontSizeNumber*1.2;
            $('html').css('font-size', newFontSize);
            return false;
        });
        //Decrease the Text Size
        $(".decreaseFont").click(function() {
            var currentFontSize = $('html').css('font-size');
            var currentFontSizeNum = parseFloat(currentFontSize, 10);
    
            var newFontSize = currentFontSizeNum*0.8;
            $('html').css('font-size', newFontSize);
            return false;
        });
        // Reset Font Size
        $(".resetFont").click(function(){
        $('html').css('font-size', originalFontSize);
      });
    });

  
  
**3) 在新的Windows打开链接**  


    // Try this code and increase your site impressions because using this jquery code users will go on new window after clicking on any link of your site. Code is below: -
    
    $(document).ready(function() {
        //select all anchor tags that have http in the href
        //and apply the target=_blank
        $("a[href^='http']").attr('target','_blank');
    });

  
  
**4) Style Sheets Swap**  
Swap style sheets using this code and the “Style sheets swap” script  is below: -  
  


    $(document).ready(function() {
        $("a.cssSwap").click(function() {
            //swap the link rel attribute with the value in the rel   
            $('link[rel=stylesheet]').attr('href' , $(this).attr('rel'));
        });
    });

  
  
**5) 回到顶部链接**  
That is very common function you can see on eve site nowadays is ” Back to Top”. This functionality is very useful for long pages for make short in a single click. Visit this code below: -  
  


    $(document).ready(function() {
        //when the id="top" link is clicked
        $('#top').click(function() {
            //scoll the page back to the top
            $(document).scrollTo(0,500);
        }
    });

  
  
**6) 获取鼠标光标的x和y轴**  
You can find the values of X and Y coordinator of mouse pointer. Code is blow : -  
  


    $().mousemove(function(e){
        //display the x and y axis values inside the P element
        $('p').html("X Axis : " + e.pageX + " | Y Axis " + e.pageY);
    });

  
  
**7) 检测当前鼠标坐标**  
使用这个脚本，你可以在jQuery所支持的任何Web浏览器找到当前鼠标的坐标。代码如下：  
  


    $(document).ready(function() {
     $().mousemove(function(e){
     $('# MouseCoordinates ').html("X Axis Position = " + e.pageX + " and Y Axis Position = " + e.pageY);
     });
    });

  
  
**8) 在jQuery中预加载图片**  
此图像预加载的脚本有助于非常快速加载图像或网页。你不必等待图像加载。代码：  
  


    jQuery.preloadImagesInWebPage = function(){
     for(var ctr = 0; ctr<arguments.length; ctr++){
     jQuery("<img alt="">").attr("src", arguments[ctr]);
     }
    }
    // To use the above method, you can use the following piece of code:
    $.preloadImages("image1.gif", "image2.gif", "image3.gif");
    // To check whether an image has been loaded, you can use the following piece of code:
    $('#imageObject').attr('src', 'image1.gif').load(function() {
     alert('The image has been loaded…');
    });

  
  
  



[http_bbs.html5cn.org_thread-79045-1-1.html]: http://bbs.html5cn.org/thread-79045-1-1.html
[Link 1]: http://www.html5cn.org/