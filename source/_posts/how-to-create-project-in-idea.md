title: IntelliJ Idea 13创建J2EE工程
date: 2014-01-20 19:12:39.0
tags:
- IntelliJ Idea
- 创建J2EE工程
categories:
- 技术研发

---

捣鼓了好一会，终于在Idea IDE上成功创建并运行了J2EE工程。下面总结一下在IntelliJ Idea下创建J2EE工程、Tomcat配置、应用部署的详细步骤。 1. 创建J2EE工程在IntelliJ IDEA中，"New Project"创建的project相当于eclipse中workspace，而module才相当于eclipse中的project。ID...

<!-- more -->

捣鼓了好一会，终于在Idea IDE上成功创建并运行了J2EE工程。下面总结一下在IntelliJ Idea下创建J2EE工程、Tomcat配置、应用部署的详细步骤。

 

### 1. 创建J2EE工程 ###

在IntelliJ IDEA中，"New Project"创建的project相当于eclipse中workspace，而module才相当于eclipse中的project。IDEA提倡一个workspace存放一个project。

 *  首先，需要创建一个"workspace":

File - New Project - Java，点击Next之后，勾选Web Application，若需要使用其他框架或技术，也可以此时在左侧列表中进行选择。

 

![Image 1][]

  


 *  接下来，我们需要创建一个真正的J2EE工程。右击项目选择Open Module Settings，进入Modules标签，点击+新建New Module。依然选择Java，选择所需要的框架组件，Finish。

> 此时可以看到在project的目录下多一个工程目录。IDEA中，工程的输出存放在project根目录的out文件夹下，\*\*\_exploded文件夹下所有的文件就是该module的发布文件。按照eclipse的习惯，我们可以在该工程目录下/web/WEB-INF下创建classes和lib文件夹作为Java文件编译和Jar包存放的目录。

![Image 1][]

  


 *  创建完毕后，需要手动修改java类编译和jar包存放的默认目录。同样进入Open Module Settings - Modules，点击当前的Module，在右侧窗口中，选择Paths，选中Use module compile output path，选择output path和test path的路径为上一步创建的classes目录

![Image 1][]  


 *  选择Dependencies，点击+新建Java or directories，选择后在弹出窗口中选择jar directory

![Image 1][]  


  


  


  


### **2. 设置Tomcat服务器** ###

 *  点击File - Settings，找到Application Servers选项，添加tomcat服务器目录，OK。这一步就相当于在eclipse的Preferences中配置tomcat。

![Image 1][]  


  


 *  在当前项目下，添加tomcat容器。这一步每次新建project之后都要重新添加。选择 Run - Edit Configurations，点击+添加本地tomcat，

![Image 1][]

  


  


3、部署应用

在上一步添加本地tomcat时，如果不做其他设置，窗口下端会出现提示：

 

![Image 1][]

 

意思是当前tomcat未发布任何artifacts, 此时可以直接点击Fix添加，也可以进入Development标签，手动添加Artifacts。

(artifact是工程的项目输出，可以是JAR包，WAR包,，EAR包或Exploded，项目创建后会默认添加Exploded的artifact，可以直接部署在tomcat上。官方解释：[http://www.jetbrains.com/idea/webhelp/artifact.html][http_www.jetbrains.com_idea_webhelp_artifact.html]。如果没有默认生成，可以右击项目，Open Module Settings，进入Artifacts的tab下，新建Web Application: Exploded。)

添加后，对应的应用就部署到了tomcat服务器上，此时就可以启动tomcat运行应用了。

  


  


 


[Image 1]: 
[http_www.jetbrains.com_idea_webhelp_artifact.html]: http://www.jetbrains.com/idea/webhelp/artifact.html