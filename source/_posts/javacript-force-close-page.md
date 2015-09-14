title: 分享一个强制关闭页面函数
date: 2013-11-27 15:50:37.0
tags:
- Javascript
- 强制关闭页面
categories:
- WEB前端

---

在WEB前端开发中，有时候需要强制关闭当前页面，可以通过js来实现，但不同浏览器的具体实现有所差异。这里博主分享一个适应多个主流浏览器的关闭页面函数。/\*\* \* Close current page \*/ function close\_page() \{ if (navigator.userAgent.indexOf("MSIE") > 0) \{ if (navi...

<!-- more -->

在WEB前端开发中，有时候需要强制关闭当前页面，可以通过js来实现，但不同浏览器的具体实现有所差异。

这里博主分享一个适应多个主流浏览器的关闭页面函数。

  


    /**
     * Close current page
     */
    function close_page() {
     if (navigator.userAgent.indexOf("MSIE") > 0) {
      if (navigator.userAgent.indexOf("MSIE 6.0") > 0) {
       window.opener = null;
       window.close();
      } else {
       window.open("", "_top");
       window.top.close();
      }
     } else {
      if (navigator.userAgent.indexOf("Firefox") > 0) {
       window.location.href = "about:blank ";
                    //window.history.go(-2);
      } else {
       window.opener = null;
       window.open("", "_self", "");
       window.close();
      }
     }
    }

  
