title: IntelliJ Idea初体验
date: 2014-01-20 19:19:03.0
tags:
- IntelliJ Idea
categories:
- 技术研发

---

      IntelliJ Idea是JetBrains公司的产品，是公认最好的Java IDE之一，除了作为Java的IDE，在Groovy，Javascript，python等语言的开发上，Idea也变得越来越流行。其实个人认为eclipse已经够强大够好用了，平时也用惯了eclipse，快捷键什么的也用得风生水起。但最近身边有人推荐Inte...

<!-- more -->

      IntelliJ Idea是JetBrains公司的产品，是公认最好的Java IDE之一，除了作为Java的IDE，在Groovy，Javascript，python等语言的开发上，Idea也变得越来越流行。其实个人认为eclipse已经够强大够好用了，平时也用惯了eclipse，快捷键什么的也用得风生水起。但最近身边有人推荐IntelliJ Idea，我便稍微去了解了一下这个工具的口碑，大多程序猿普通认为它速度快，UI好，框架和版本控制工具的整合都非常优秀，于是我也想试试转到Idea上做开发。下面总结一下我使用IntelliJ Idea的初体验

  


### **1. UI界面**    ###

下载了IntelliJ Idea 13版本，打开之后给我的感觉就是UI确实做得十分优秀，相比eclipse有些高大上的感觉。

 

![010b3874-4c9e-416b-bbaf-82b9b3dc8250.png][]  


Idea的初始化界面并不是酷炫的黑色，而是比较朴素的白色背景，怎么修改呢？很简单：

进入File - Settings，在IDE Settings下找到Appearance，选择主题Theme为“Darcula”

 

![a027049b-8c91-417d-8d6d-c78fc7bd216d.png][]  


同样在IDE Settings下，展开Editors选项，可以设置编辑器的其他选项，例如字体、大小等等。

 

  


### **2. 初始化配置** ###

一般我使用IDE，喜欢先做一些初始化配置，例如配置代码模板，这个同样是在IDE Settings中可以找到。

找到File and Code Templates，在templates的tab下就可以设置各种文件的代码模板。

例如：我习惯让IDE在创建类的时候，初始化一些注释。只要在templates下找到Class，把注释内容添加到"public class ..."之上就可以了：

 

![1475078b-53b6-4142-8a6f-d6254524c282.png][]  


### **3. 常用快捷键** ###

IDE的快捷键对开发人员来说还是比较重要的，熟悉一个IDE快捷键可以一定程度上提高开发人员的效率。下面列一下一些比较常用的基础快捷键：

 *  Ctrl+Shift+N 查找类
 *  Ctrl+Shift+N 查找文件 
 *  Ctrl+Alt+L  格式化代码  
 *  Ctrl+P 方法参数提示 
 *  Ctrl+Shift+上/下     移动代码到上一行或下一行，若光标在方法声明上，将会移动整个方法到前一个方法之上
 *  Ctrl+X 删除行
 *  Ctrl+D 复制行
 *  Alt+1 快速打开或隐藏Project面板
 *  F2     快速定位到错误位置
 *  调试：
 *  F8 Step over
    
    F7 Step into
    
    F9 Resume

 

**4. 创建J2EE工程**

Idea下常见J2EE工程和eclipse还是有很大区别的，这部分我单独在另外一篇文章中总结，可以进入[这里][Link 1]查看  



[010b3874-4c9e-416b-bbaf-82b9b3dc8250.png]: http://www.charlestech.org/xheditor_img/20140120/010b3874-4c9e-416b-bbaf-82b9b3dc8250.png
[a027049b-8c91-417d-8d6d-c78fc7bd216d.png]: http://www.charlestech.org/xheditor_img/20140120/a027049b-8c91-417d-8d6d-c78fc7bd216d.png
[1475078b-53b6-4142-8a6f-d6254524c282.png]: http://www.charlestech.org/xheditor_img/20140120/1475078b-53b6-4142-8a6f-d6254524c282.png
[Link 1]: http://www.charlestech.org/view_article.shtml?uid=ZWEXSDARQC60