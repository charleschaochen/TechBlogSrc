title: jasmine - 前端单元测试框架
date: 2014-01-02 10:36:49.0
tags:
- 单元测试
- jasmine
categories:
- 软件测试

---

1. 为什么需要前端的单元测试由于网页用户环境的复杂性与多样性，前端代码往往需要在数十个浏览器上测试器正确性。靠点击点击点击来寻找程序bug。这种方式既费时费力，又无法保证测试的覆盖面。随着web2.0的发展，前端页面日益复杂，背后的JS逻辑也越来越复杂，一个页面里面多达上千行的js文件不在少数。一个函数，一个模块，在修改bug或添加新功能的过程中，很容易就产生新的bug，或使老的bug复活。这种...

<!-- more -->

**1. 为什么需要前端的单元测试**

由于网页用户环境的复杂性与多样性，前端代码往往需要在数十个浏览器上测试器正确性。靠点击点击点击来寻找程序bug。这种方式既费时费力，又无法保证测试的覆盖面。  
随着web2.0的发展，前端页面日益复杂，背后的JS逻辑也越来越复杂，一个页面里面多达上千行的js文件不在少数。一个函数，一个模块，在修改bug或添加新功能的过程中，很容易就产生新的bug，或使老的bug复活。这种情况下，在各个浏览器反复进行黑盒测试，其工作量和测试质量是可想而知的。  
所以为了让测试人员从大量重复性劳动中释放出来，以及保证测试工作的完整和全面性，JS代码需要自动化测试。

  


**2. 怎么样进行单元测试**

单元测试，主要涉及到两种思想。

TDD - Test Driven Development，测试驱动开发

BDD - Behavior Driven Development，行为驱动开发

TDD 理论将就先编写测试代码，再进行开发。 具体开发流程：  
a） 在开始编码前，针对每个关键函数，预先编写好测试用例  
b） 运行测试用列（此时用例肯定会失败，因为代码还未生鲜）  
c） 编写实现代码  
d） 针对实现代码，运行测试用列  
e） 根据测试结果，重构实现代码  
f） 回归用例，解决bug

该过程不断循环下去，直到达到测试完成的标准，这就是测试驱动开发。

  


**3. 什么是jasmine**

（1） jasmine是一个有名的javascript单元测试框架，它是独立的行为驱动开发(BDD)。

（2） jasmine框架由以下四个部分组成：

a） jasmine的核心组件(jasmine.css 、jasmine.js 、jasmine-html.js)。核心组件实现了对测试用例的解析、运行和展现样式的封装。

b） 待测试的js文件

c） 对应待测试js文件的测试用列  
  
d） 一个结果展示页面，他将上述相关联的文件都引入，将单元测试用例运行的结果展示在页面上

 

**4. jasmine使用实例**

（1）项目文件目录结构

lib

| - jasmine-1.3.0

   | - jasmine.css

   | - jasmine.js

   | - jasmine-html.js

   | - MIT.LICENSE

spec

| - sourceSpec.js (测试用例文件)

  


src

  


| - source.js (待测试的源代码文件)

  


SpecRunner.html  (结果展示页面)

 

（2） 利用jasmine进行前端单元测试

 测试用例文件也是一个普通的js文件，由一个或多个describe函数结构构成，每个decribe表示一个用例suite，decribe函数有两个参数：一个描述字符串和一个函数体，描述字符串用来描述这个suite的功能，函数体包含若干个it函数结构，每个it函数表示一个测试用例。接下来用一个“hello word”来介绍如何使用jasmine进行前端单元测试。

a） 首先在spec文件夹下，创建测试用例文件sourceSpec.js：

    describe("addition", function() {
      it("should be equal to 3", function() {
        expect(add(1,2)).toEqual(3);
      });
    });

该文件定义了1个测试用例，标题是addition加法，用例调用了add函数，以1和2作为参数，以toEqual(3)作为用例执行成功的标准

b）在src文件夹下编写待测试的的源代码source.js：

    /*
     * Get sum of a and b
     */
    function add(a, b) {
    	return a+b;
    }

待测试函数是一个简单的加法函数

c）在根目录创建测试结果显示页面SpecRunner.html:

    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
      "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
      <title>Jasmine Spec Runner</title>
    
      <link rel="stylesheet" type="text/css" href="lib/jasmine-1.3.0/jasmine.css">
      <script type="text/javascript" src="lib/jasmine-1.3.0/jasmine.js"></script>
      <script type="text/javascript" src="lib/jasmine-1.3.0/jasmine-html.js"></script>
    
      <!-- include source files here... -->
      <script type="text/javascript" src="src/source.js"></script>
    
      <!-- include spec files here... -->
      <script type="text/javascript" src="spec/sourceSpec.js"></script>
    
      <script type="text/javascript">
        window.onload = function() {
    		var jasmineEnv = jasmine.getEnv();
    		jasmineEnv.updateInterval = 1000;
    		var htmlReporter = new jasmine.HtmlReporter();
    		jasmineEnv.addReporter(htmlReporter);
    		jasmineEnv.specFilter = function(spec) {
    		return htmlReporter.specFilter(spec);
    		};
    		jasmineEnv.execute();
    	};
      </script>
    
    </head>
    
    <body>
    </body>
    </html>

该页面创建了一个jasmineEnv对象，并执行生成报表

  


打开SpecRunner.html页面，显示如下，表示用例执行成功:

![Image 1][]

这是运行成功的报表显示，如果我们在sourceSpec.js再加一个异常用例，如：

    describe("addition", function() {
      it("should be equal to 3", function() {
        expect(add(1,2)).toEqual(3);
      });
      
      it("should be equal to 9", function() {
        expect(add(3,5)).toEqual(9);
      });
    });

再次打开SpecRunner.html页面，显示如下：

![Image 1][]  


报表显示两个用例中有一个执行失败，并显示失败的信息。

  
 

**jasmine项目github地址： [https://github.com/pivotal/jasmine][https_github.com_pivotal_jasmine]**


[Image 1]: 
[https_github.com_pivotal_jasmine]: https://github.com/pivotal/jasmine