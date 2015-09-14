title: 自动加载分页导航框架
date: 2014-01-14 18:29:41.0
tags:
- 分页
categories:
- WEB前端

---

分页导航在页面上做数据显示的时候，经常要利用分页。所以需要在前端显示页号作为导航，通常分页导航需要显示的元素有：首页、当前页号、上一页、下一页等等。很多站点都有比较美观还用的分页导航，如：   AutoPagination自动加载分页导航为了快速在页面上添加一个分页导航，避免重复的编码，笔者做了一个自动加载分页导航的前端框架，只需要一个简单的js函数，就可以在页面上呈...

<!-- more -->

### 分页导航 ###

在页面上做数据显示的时候，经常要利用**分页。**

所以需要在前端显示页号作为导航，通常分页导航需要显示的元素有：首页、当前页号、上一页、下一页等等。

很多站点都有比较美观还用的分页导航，如：

 ![Image 1][]

 ![Image 1][]

![Image 1][]

![Image 1][]

###   ###

  


### AutoPagination自动加载分页导航 ###

  


为了快速在页面上添加一个分页导航，避免重复的编码，笔者做了一个自动加载分页导航的前端框架，只需要一个简单的js函数，就可以在页面上呈现如下的一个分页导航：

![Image 1][]

  


#### AutoPagination函数讲解 ####

根据不同需求，提供了两种方式加载分页导航：

 *  load\_pagination(page\_div, pageNum, maxShow, param\_name, link)

加载分页导航，页号url参数名由param\_name给出

参数说明：

page\_div：指定分页导航的div的ID

pageNum：总页数

maxShow：页数太多时，省略的页号之前最大显示的页数，若该值大于等于pageNum的一半，则显示全部页数

param\_name：url参数中表示页号的参数名称

link：页号跳转链接

  


 *  load\_pagination\_pageno(page\_div, pageNum, pageNo, maxShow, link)

加载分页导航，页号url参数名在link中给出

参数说明：

page\_div：指定分页导航的div的ID

pageNum：总页数

pageNo：页号

maxShow：页数太多时，省略的页号之前最大显示的页数，若该值大于等于pageNum的一半，则显示全部页数

link：页号跳转链接，带有页号url参数名

  


#### AutoPagination使用实例 ####

 *   文件结构：

  


js

|\_\_  auto-pagination.min.js

css

|\_\_  auto-pagination.min.css

demo.html

  


 *   demo.html代码如下：

    <!doctype html>
    <html lang="en">
    	<head>
    		<title>Auto Pagination</title>
    		<link rel="stylesheet" type="text/css" href="css/auto-pagination.min.css" />
    		<script type="text/javascript" src="http://www.charlestech.org/js/jquery-1.4.4.min.js"></script>
    		<script type="text/javascript" src="js/auto-pagination.js"></script>
    		<script type="text/javascript">
    			$(function(){
    				load_pagination_pageno("page_area", 20, get_param("page"), 5, "demo.html?page=");	// 方式1
    				load_pagination("page_area_1", 20, 5, "page", "demo.html");	// 方式2
    			});
    		</script>
    	</head>
    	
    	<body>
    		<div class="page_area"></div>
    		<div class="page_area_1"></div>
    	</body>
    </html>

 

**点击[这里][Link 1]，查看在线demo。**

  


#### AutoPagination源码下载 ####

AutoPagination最新源代码已发布在Github上，点击[这里][Link 2]进行查看下载。


[Image 1]: 
[Link 1]: http://www.charlestech.org/demo/autopagination_demo.html
[Link 2]: https://github.com/charleschaochen/AutoPagination