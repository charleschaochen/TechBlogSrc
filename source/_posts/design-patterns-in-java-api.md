title: Java API中的设计模式
date: 2014-08-04 16:45:13.0
tags:
- 设计模式
categories:
- 设计模式

---

原文链接： stackoverflow 翻译： ImportNew.com - 刘 家财译文链接： http://www.importnew.com/12526.html本文由 ImportNew - 刘 家财 翻译自 stackoverflow。欢迎加入Java小组。转载请参见文章末尾的...

<!-- more -->

原文链接： [stackoverflow][] 翻译： [ImportNew.com ][ImportNew.com]\- [刘 家财][Link 1]  
译文链接： [http://www.importnew.com/12526.html][http_www.importnew.com_12526.html]  


  


  


本文由 [ImportNew][ImportNew.com] - [刘 家财][Link 1] 翻译自 [stackoverflow][]。欢迎加入[Java小组][Java]。转载请参见文章末尾的要求。

提问：我正在学习GoF的《[设计模式][Link 2]》，想了解些它们在实际中的应用的例子。大家能给我举一些使用设计模式的好例子吗？尤其是在Java类库中。

赞同最高的回答：

你可以通过[Wikipedia][]对设计模式有个整体上的理解。Wikipedia上也提高了GoF所涉及到的模式。我这里总结一下，并且尽可能指出在JavaSE与JavaEE的API中是如何运用这些模式的。

  


# [创建型设计模式][Link 3] #

## [抽象工厂模式][Link 4] ##

特点：创建方法返回一个可以用来创建抽象类或接口的工厂类。

 *  [javax.xml.parsers.DocumentBuilderFactory\#newInstance()][javax.xml.parsers.DocumentBuilderFactory_newInstance]
 *  [javax.xml.transform.TransformerFactory\#newInstance()][javax.xml.transform.TransformerFactory_newInstance]
 *  [javax.xml.xpath.XPathFactory\#newInstance()][javax.xml.xpath.XPathFactory_newInstance]

##     ##

## [生成器模式][Link 5] ##

特点:创建方法返回这个实例本身。

 *  [java.lang.StringBuilder\#append()][java.lang.StringBuilder_append]不同步
 *  [java.lang.StringBuffer\#append()][java.lang.StringBuffer_append]同步
 *  [java.nio.ByteBuffer\#put()][java.nio.ByteBuffer_put]（除此之外，还有[CharBuffer][],[ShortBuffer][],[IntBuffer][],[LongBuffer][],[FloatBuffer][]与[DoubleBuffer][]）
 *  [javax.swing.GroupLayout.Group\#addComponent()][javax.swing.GroupLayout.Group_addComponent]
 *  [java.lang.Appendable][]的所有实现

##     ##

## [工厂方法模式][Link 6] ##

特点：创建方法返回抽象类或接口的实现。

 *  [java.util.Calendar\#getInstance()][java.util.Calendar_getInstance]
 *  [java.util.ResourceBundle\#getBundle()][java.util.ResourceBundle_getBundle]
 *  [java.text.NumberFormat\#getInstance()][java.text.NumberFormat_getInstance]
 *  [java.nio.charset.Charset\#forName()][java.nio.charset.Charset_forName]
 *  [java.net.URLStreamHandlerFactory\#createURLStreamHandler(String)][java.net.URLStreamHandlerFactory_createURLStreamHandler_String]对于每个协议（protocol）返回一个单例对象

###     ###

###  ###

## 原型[模式][Link 6] ##

特点：创建方法返回一个同类型且具有相同属性的另一个实例。

 *  [java.lang.Object\#clone()][java.lang.Object_clone]（这个类必须实现[java.lang.Cloneable][]）

##     ##

## [单例模式][Link 7] ##

特点：创建方法返回同一个实例（无论在何时调用）。

 *  [java.lang.Runtime\#getRuntime()][java.lang.Runtime_getRuntime]
 *  [java.awt.Desktop\#getDesktop()][java.awt.Desktop_getDesktop]

#     #

# [结构型模式][Link 8] #

## [适配器模式][Link 9] ##

特点：创建方法接受一个与当前类不同的抽象类或接口的实例作为参数，返回一个经过修饰或重写给定参数实例的抽象类或接口的实现。

 *  [java.util.Arrays\#asList()][java.util.Arrays_asList]
 *  [java.io.InputStreamReader(InputStream)][java.io.InputStreamReader_InputStream]返回一个Reader对象
 *  [java.io.OutputStreamWriter(OutputStream)][java.io.OutputStreamWriter_OutputStream]返回一个Writer对象
 *  [javax.xml.bind.annotation.adapters.XmlAdapter\#marshal()][javax.xml.bind.annotation.adapters.XmlAdapter_marshal]与[\#unmarshal()][unmarshal]

##     ##

## [桥接模式][Link 10] ##

特点：创建方法接受一个与当前类不同的抽象类或接口的实例作为参数，返回一个经过代理或使用给定参数实例的抽象类或接口的实现。

 *  暂时没有想到，一个可以想到的例子是new LinkedHashMap(LinkedHashSet, List)，这个方法返回一个不可修改的linkedMap，它就没有拷贝参数中的元素（item），而是直接使用它们。[java.util.Collections\#newSetFromMap()][java.util.Collections_newSetFromMap]和[singletonXXX()][singletonXXX]方法也与之类似。

##     ##

## [组合模式][Link 11] ##

特点：行为方法把相同抽象类或接口的实例转化为一个树结构。

 *  [java.awt.Container\#add(Component)][java.awt.Container_add_Component]（几乎对所有的Swing都适用）
 *  [javax.faces.component.UIComponent\#getChildren()][javax.faces.component.UIComponent_getChildren]（几乎对所有的JSF UI都适用）

##     ##

## [装饰模式][Link 12] ##

特点：创建方法以一个抽象类或接口的实例为参数，返回值是增加了额外方法的给参数实例。

 *  [java.io.InputStream][]、[OutputStream][]、[Reader][]、[Writer][]这些类的所有子类，它们都有一个接受相同类型作为参数的构造函数。
 *  [java.util.Collections][]中[checkedXXX()][checkedXXX]、[synchronizedXXX()][synchronizedXXX]、[unmodifiableXXX()][unmodifiableXXX]方法
 *  [javax.servlet.http.HttpServletRequestWrapper][]与[HttpServletResponseWrapper][]

##     ##

## [外观模式][Link 13] ##

特点：行为方法在内部使用完全不同的抽象类或接口的实例做封装。

 *  [javax.faces.context.FacesContext][]，这个类在内部使用了抽象类或接口[LifeCycle][]、[ViewHandler][]、[NavigationHandler][]以及其他一些用户不需要关心的类（通常这些类都是可通过注入重写的）。
 *  [javax.faces.context.ExternalContext][]，这个类在内部使用了[ServletContext][]、[HttpSession][]、[HttpServletRequest][]、[HttpServletResponse][]等。

##     ##

## [享元模式][Link 14] ##

特点：创建方法返回一个缓存的实例，与多例模式有些类似。

 *  [java.lang.Integer\#valueOf(int)][java.lang.Integer_valueOf_int]（除此之外，类似的还有[Boolean][]、[Byte][]、[Character][]、[Short][]、[Long][]）

##     ##

## [代理模式][Link 15] ##

特点：创建方法返回一个给定抽象类或接口的实例，这个实例代理或使用了这个给定抽象类或接口的另一个实现。

 *  [java.lang.reflect.Proxy][]
 *  [java.rmi.\*][java.rmi.]包下的所有类

恕我直言，Wikipedia上的类子不是很好，惰性加载实际上和代理模式一点关系也没有。

##     ##

## [行为模式][Link 16] ##

## [职责链模式][Link 17] ##

特点:行为方法间接调用队列中同一抽象类或接口的另一实例的同名方法。

 *  [java.util.logging.Logger\#log()][java.util.logging.Logger_log]
 *  [javax.servlet.Filter\#doFilter()][javax.servlet.Filter_doFilter]

##     ##

## [命令模式][Link 18] ##

特点：一个抽象类或接口中的行为方法调用另一个在创建时经命令方法包装的抽象类或接口实现的另一个方法。

 *  [java.lang.Runnable][]的所有实现
 *  [javax.swing.Action][]的所有实现

##     ##

## [解释器模式][Link 19] ##

特点：行为方法返回结构上不同的抽象类或接口的实例，需要注意的是解析或格式化过程并不是这个模式的一部分，而这个过程决定了解释器如何将要去应用并实施这个变换。

 *  [java.util.Pattern][]
 *  [java.text.Normalizer][]
 *  [java.text.Format][]类的所有子类
 *  [javax.el.ELResolver][]类的所有子类

##     ##

## [迭代模式][Link 20] ##

特点：行为方法连续地返回队列中的相同对象的不同实例。

 *  [java.util.Iterator][]类的所有实现（[java.util.Scanner][]也类似）
 *  [java.util.Enumeration][]类的所有实现

##     ##

## [中介者模式][Link 21] ##

特点：行为方法接受一个不同的抽象类或接口的实例（一般使用命令模式）作为参数，而这个参数同时也代理了其它给定抽象类或接口的实例。

 *  [java.util.Timer][]所有的scheduleXXX()方法
 *  [java.util.concurrent.Executor\#execute()][java.util.concurrent.Executor_execute]
 *  [java.util.concurrent.ExecutorService][]的invokeXXX()与submit()方法
 *  [java.util.concurrent.ScheduledExecutorService][]所有scheduleXXX()方法
 *  [java.lang.reflect.Method\#invoke()][java.lang.reflect.Method_invoke]

##     ##

## [备忘模式][Link 22] ##

特点：行为方法在内部改变整个实例的状态。

 *  [java.util.Date][]（setter方法就是典型的例子，Date对象在内容是用一个long值来表示的）
 *  [java.io.Serializable][]的所有实现
 *  [javax.faces.component.StateHolder][]的所有实现

##     ##

## [观察者（发布/订阅）模式][Link 23] ##

特点：行为方法根据其自身的状态，去调用另一个抽象类或接口实例的方法。

 *  [java.util.Observer][]/[java.util.Observable][]在实际中用的比较少
 *  [java.util.EventListener][]的所有实现（几乎对所以Swing对象都适用）
 *  [javax.servlet.http.HttpSessionBindingListener][]
 *  [http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSessionAttributeListener.html][http_docs.oracle.com_javaee_6_api_javax_servlet_http_HttpSessionAttributeListener.html]
 *  [javax.faces.event.PhaseListener][]

##     ##

## [状态模式][Link 24] ##

特点：行为方法根据它能够控制的状态来变自身的行为。

 *  [javax.faces.lifecycle.LifeCycle\#execute()][javax.faces.lifecycle.LifeCycle_execute]由[FacesServlet][]控制，它的行为依赖与当前JSF的生命周期所处的阶段

##     ##

## [策略模式][Link 25] ##

特点：在一个抽象类或接口的行为方法会调用作为一种策略实现而通过参数传入的另一个抽象类或接口实例的方法。

 *  [java.util.Comparator\#compare()][java.util.Comparator_compare]，这个方法被Collections\#sort()所调用
 *  [javax.servlet.http.HttpServlet][]类中的service()与所有的doXXX()方法接受HttpServletRequest与HttpServletResponse这两个参数（并不是通过像成员变量这种方式容纳它们），这样其所有子类都必须去处理它们
 *  [javax.servlet.Filter\#doFilter()][javax.servlet.Filter_doFilter]

##     ##

## [模板方法模式][Link 26] ##

特点：具有由抽象类型定义默认行为的行为方法。

 *  [java.io.InputStream][]、[java.io.OutputStream][OutputStream]、[java.io.Reader][Reader]与[java.io.Writer][Writer]类的所有非抽象方法
 *  [java.util.AbstractList][]、[java.util.AbstractSet][]、[java.util.AbstractMap][]的所有非抽象方法
 *  [javax.servlet.http.HttpServlet][]类的所有doXXX()方法默认发送HTTP 405“方法不允许”这个错误给response，你可以自由地选择是否去实现它们

##     ##

## [访问者模式][Link 27] ##

特点：由两个不同的抽象类或接口，它们都有接受对方做参数的方法，被调用的方法会去调用另一个对象的方法，然后根据制定好的策略执行。

 *  [javax.lang.model.element.AnnotationValue][]与[AnnotationValueVisitor][]
 *  [javax.lang.model.element.Element][]与[ElementVisitor][]
 *  [javax.lang.model.type.TypeMirror][]与[TypeVisitor][]

  



[stackoverflow]: http://stackoverflow.com/questions/1673841/examples-of-gof-design-patterns
[ImportNew.com]: http://www.importnew.com/
[Link 1]: http://www.importnew.com/author/liujiacai
[http_www.importnew.com_12526.html]: http://www.importnew.com/12526.html
[Java]: http://www.jobbole.com/groups/9/
[Link 2]: http://www.amazon.cn/gp/product/B001130JN8/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&tag=importnew-23&linkCode=as2&camp=536&creative=3200&creativeASIN=B001130JN8
[Wikipedia]: http://en.wikipedia.org/wiki/Design_pattern_%28computer_science%29#Classification_and_list
[Link 3]: http://en.wikipedia.org/wiki/Creational_pattern
[Link 4]: http://en.wikipedia.org/wiki/Abstract_factory_pattern
[javax.xml.parsers.DocumentBuilderFactory_newInstance]: http://docs.oracle.com/javase/6/docs/api/javax/xml/parsers/DocumentBuilderFactory.html#newInstance%28%29
[javax.xml.transform.TransformerFactory_newInstance]: http://docs.oracle.com/javase/6/docs/api/javax/xml/transform/TransformerFactory.html#newInstance%28%29
[javax.xml.xpath.XPathFactory_newInstance]: http://docs.oracle.com/javase/6/docs/api/javax/xml/xpath/XPathFactory.html#newInstance%28%29
[Link 5]: http://en.wikipedia.org/wiki/Builder_pattern
[java.lang.StringBuilder_append]: http://docs.oracle.com/javase/6/docs/api/java/lang/StringBuilder.html#append%28boolean%29
[java.lang.StringBuffer_append]: http://docs.oracle.com/javase/6/docs/api/java/lang/StringBuffer.html#append%28boolean%29
[java.nio.ByteBuffer_put]: http://docs.oracle.com/javase/6/docs/api/java/nio/ByteBuffer.html#put%28byte%29
[CharBuffer]: http://docs.oracle.com/javase/6/docs/api/java/nio/CharBuffer.html#put%28char%29
[ShortBuffer]: http://docs.oracle.com/javase/6/docs/api/java/nio/ShortBuffer.html#put%28short%29
[IntBuffer]: http://docs.oracle.com/javase/6/docs/api/java/nio/IntBuffer.html#put%28int%29
[LongBuffer]: http://docs.oracle.com/javase/6/docs/api/java/nio/LongBuffer.html#put%28long%29
[FloatBuffer]: http://docs.oracle.com/javase/6/docs/api/java/nio/FloatBuffer.html#put%28float%29
[DoubleBuffer]: http://docs.oracle.com/javase/6/docs/api/java/nio/DoubleBuffer.html#put%28double%29
[javax.swing.GroupLayout.Group_addComponent]: http://docs.oracle.com/javase/6/docs/api/javax/swing/GroupLayout.Group.html#addComponent%28java.awt.Component%29
[java.lang.Appendable]: http://docs.oracle.com/javase/6/docs/api/java/lang/Appendable.html
[Link 6]: http://en.wikipedia.org/wiki/Factory_method_pattern
[java.util.Calendar_getInstance]: http://docs.oracle.com/javase/6/docs/api/java/util/Calendar.html#getInstance%28%29
[java.util.ResourceBundle_getBundle]: http://docs.oracle.com/javase/6/docs/api/java/util/ResourceBundle.html#getBundle%28java.lang.String%29
[java.text.NumberFormat_getInstance]: http://docs.oracle.com/javase/6/docs/api/java/text/NumberFormat.html#getInstance%28%29
[java.nio.charset.Charset_forName]: http://docs.oracle.com/javase/6/docs/api/java/nio/charset/Charset.html#forName%28java.lang.String%29
[java.net.URLStreamHandlerFactory_createURLStreamHandler_String]: http://docs.oracle.com/javase/6/docs/api/java/net/URLStreamHandlerFactory.html
[java.lang.Object_clone]: http://docs.oracle.com/javase/6/docs/api/java/lang/Object.html#clone%28%29
[java.lang.Cloneable]: http://docs.oracle.com/javase/6/docs/api/java/lang/Cloneable.html
[Link 7]: http://en.wikipedia.org/wiki/Singleton_pattern
[java.lang.Runtime_getRuntime]: http://docs.oracle.com/javase/6/docs/api/java/lang/Runtime.html#getRuntime%28%29
[java.awt.Desktop_getDesktop]: http://docs.oracle.com/javase/6/docs/api/java/awt/Desktop.html#getDesktop%28%29
[Link 8]: http://en.wikipedia.org/wiki/Structural_pattern
[Link 9]: http://en.wikipedia.org/wiki/Adapter_pattern
[java.util.Arrays_asList]: http://docs.oracle.com/javase/6/docs/api/java/util/Arrays.html#asList%28T...%29
[java.io.InputStreamReader_InputStream]: http://docs.oracle.com/javase/6/docs/api/java/io/InputStreamReader.html#InputStreamReader%28java.io.InputStream%29
[java.io.OutputStreamWriter_OutputStream]: http://docs.oracle.com/javase/6/docs/api/java/io/OutputStreamWriter.html#OutputStreamWriter%28java.io.OutputStream%29
[javax.xml.bind.annotation.adapters.XmlAdapter_marshal]: http://docs.oracle.com/javase/6/docs/api/javax/xml/bind/annotation/adapters/XmlAdapter.html#marshal%28BoundType%29
[unmarshal]: http://docs.oracle.com/javase/6/docs/api/javax/xml/bind/annotation/adapters/XmlAdapter.html#unmarshal%28ValueType%29
[Link 10]: http://en.wikipedia.org/wiki/Bridge_pattern
[java.util.Collections_newSetFromMap]: http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#newSetFromMap%28java.util.Map%29
[singletonXXX]: http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#singleton%28T%29
[Link 11]: http://en.wikipedia.org/wiki/Composite_pattern
[java.awt.Container_add_Component]: http://docs.oracle.com/javase/6/docs/api/java/awt/Container.html#add%28java.awt.Component%29
[javax.faces.component.UIComponent_getChildren]: http://docs.oracle.com/javaee/6/api/javax/faces/component/UIComponent.html#getChildren%28%29
[Link 12]: http://en.wikipedia.org/wiki/Decorator_pattern
[java.io.InputStream]: http://docs.oracle.com/javase/6/docs/api/java/io/InputStream.html
[OutputStream]: http://docs.oracle.com/javase/6/docs/api/java/io/OutputStream.html
[Reader]: http://docs.oracle.com/javase/6/docs/api/java/io/Reader.html
[Writer]: http://docs.oracle.com/javase/6/docs/api/java/io/Writer.html
[java.util.Collections]: http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html
[checkedXXX]: http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#checkedCollection%28java.util.Collection,%20java.lang.Class%29
[synchronizedXXX]: http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#synchronizedCollection%28java.util.Collection%29
[unmodifiableXXX]: http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#unmodifiableCollection%28java.util.Collection%29
[javax.servlet.http.HttpServletRequestWrapper]: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequestWrapper.html
[HttpServletResponseWrapper]: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletResponseWrapper.html
[Link 13]: http://en.wikipedia.org/wiki/Facade_pattern
[javax.faces.context.FacesContext]: http://docs.oracle.com/javaee/6/api/javax/faces/context/FacesContext.html
[LifeCycle]: http://docs.oracle.com/javaee/6/api/javax/faces/lifecycle/Lifecycle.html
[ViewHandler]: http://docs.oracle.com/javaee/6/api/javax/faces/application/ViewHandler.html
[NavigationHandler]: http://docs.oracle.com/javaee/6/api/javax/faces/application/NavigationHandler.html
[javax.faces.context.ExternalContext]: http://docs.oracle.com/javaee/6/api/javax/faces/context/ExternalContext.html
[ServletContext]: http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContext.html
[HttpSession]: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSession.html
[HttpServletRequest]: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html
[HttpServletResponse]: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletResponse.html
[Link 14]: http://en.wikipedia.org/wiki/Flyweight_pattern
[java.lang.Integer_valueOf_int]: http://docs.oracle.com/javase/6/docs/api/java/lang/Integer.html#valueOf%28int%29
[Boolean]: http://docs.oracle.com/javase/6/docs/api/java/lang/Boolean.html#valueOf%28boolean%29
[Byte]: http://docs.oracle.com/javase/6/docs/api/java/lang/Byte.html#valueOf%28byte%29
[Character]: http://docs.oracle.com/javase/6/docs/api/java/lang/Character.html#valueOf%28char%29
[Short]: http://docs.oracle.com/javase/6/docs/api/java/lang/Short.html#valueOf%28short%29
[Long]: http://docs.oracle.com/javase/6/docs/api/java/lang/Long.html#valueOf%28long%29
[Link 15]: http://en.wikipedia.org/wiki/Proxy_pattern
[java.lang.reflect.Proxy]: http://docs.oracle.com/javase/6/docs/api/java/lang/reflect/Proxy.html
[java.rmi.]: http://docs.oracle.com/javase/6/docs/api/java/rmi/package-summary.html
[Link 16]: http://en.wikipedia.org/wiki/Behavioral_pattern
[Link 17]: http://en.wikipedia.org/wiki/Chain_of_responsibility_pattern
[java.util.logging.Logger_log]: http://docs.oracle.com/javase/6/docs/api/java/util/logging/Logger.html#log%28java.util.logging.Level,%20java.lang.String%29
[javax.servlet.Filter_doFilter]: http://docs.oracle.com/javaee/6/api/javax/servlet/Filter.html#doFilter%28javax.servlet.ServletRequest,%20javax.servlet.ServletResponse,%20javax.servlet.FilterChain%29
[Link 18]: http://en.wikipedia.org/wiki/Command_pattern
[java.lang.Runnable]: http://docs.oracle.com/javase/6/docs/api/java/lang/Runnable.html
[javax.swing.Action]: http://docs.oracle.com/javase/6/docs/api/javax/swing/Action.html
[Link 19]: http://en.wikipedia.org/wiki/Interpreter_pattern
[java.util.Pattern]: http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html
[java.text.Normalizer]: http://docs.oracle.com/javase/6/docs/api/java/text/Normalizer.html
[java.text.Format]: http://docs.oracle.com/javase/6/docs/api/java/text/Format.html
[javax.el.ELResolver]: http://docs.oracle.com/javaee/6/api/javax/el/ELResolver.html
[Link 20]: http://en.wikipedia.org/wiki/Iterator_pattern
[java.util.Iterator]: http://docs.oracle.com/javase/6/docs/api/java/util/Iterator.html
[java.util.Scanner]: http://docs.oracle.com/javase/6/docs/api/java/util/Scanner.html
[java.util.Enumeration]: http://docs.oracle.com/javase/6/docs/api/java/util/Enumeration.html
[Link 21]: http://en.wikipedia.org/wiki/Mediator_pattern
[java.util.Timer]: http://docs.oracle.com/javase/6/docs/api/java/util/Timer.html
[java.util.concurrent.Executor_execute]: http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/Executor.html#execute%28java.lang.Runnable%29
[java.util.concurrent.ExecutorService]: http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/ExecutorService.html
[java.util.concurrent.ScheduledExecutorService]: http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/ScheduledExecutorService.html
[java.lang.reflect.Method_invoke]: http://docs.oracle.com/javase/6/docs/api/java/lang/reflect/Method.html#invoke%28java.lang.Object,%20java.lang.Object...%29
[Link 22]: http://en.wikipedia.org/wiki/Memento_pattern
[java.util.Date]: http://docs.oracle.com/javase/6/docs/api/java/util/Date.html
[java.io.Serializable]: http://docs.oracle.com/javase/6/docs/api/java/io/Serializable.html
[javax.faces.component.StateHolder]: http://docs.oracle.com/javaee/6/api/javax/faces/component/StateHolder.html
[Link 23]: http://en.wikipedia.org/wiki/Observer_pattern
[java.util.Observer]: http://docs.oracle.com/javase/6/docs/api/java/util/Observer.html
[java.util.Observable]: http://docs.oracle.com/javase/6/docs/api/java/util/Observable.html
[java.util.EventListener]: http://docs.oracle.com/javase/6/docs/api/java/util/EventListener.html
[javax.servlet.http.HttpSessionBindingListener]: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSessionBindingListener.html
[http_docs.oracle.com_javaee_6_api_javax_servlet_http_HttpSessionAttributeListener.html]: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSessionAttributeListener.html
[javax.faces.event.PhaseListener]: http://docs.oracle.com/javaee/6/api/javax/faces/event/PhaseListener.html
[Link 24]: http://en.wikipedia.org/wiki/State_pattern
[javax.faces.lifecycle.LifeCycle_execute]: http://docs.oracle.com/javaee/6/api/javax/faces/lifecycle/Lifecycle.html#execute%28javax.faces.context.FacesContext%29
[FacesServlet]: http://docs.oracle.com/javaee/6/api/javax/faces/webapp/FacesServlet.html
[Link 25]: http://en.wikipedia.org/wiki/Strategy_pattern
[java.util.Comparator_compare]: http://docs.oracle.com/javase/6/docs/api/java/util/Comparator.html#compare%28T,%20T%29
[javax.servlet.http.HttpServlet]: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServlet.html
[Link 26]: http://en.wikipedia.org/wiki/Template_method_pattern
[java.util.AbstractList]: http://docs.oracle.com/javase/6/docs/api/java/util/AbstractList.html
[java.util.AbstractSet]: http://docs.oracle.com/javase/6/docs/api/java/util/AbstractSet.html
[java.util.AbstractMap]: http://docs.oracle.com/javase/6/docs/api/java/util/AbstractMap.html
[Link 27]: http://en.wikipedia.org/wiki/Visitor_pattern
[javax.lang.model.element.AnnotationValue]: http://docs.oracle.com/javase/6/docs/api/javax/lang/model/element/AnnotationValue.html
[AnnotationValueVisitor]: http://docs.oracle.com/javase/6/docs/api/javax/lang/model/element/AnnotationValueVisitor.html
[javax.lang.model.element.Element]: http://docs.oracle.com/javase/6/docs/api/javax/lang/model/element/Element.html
[ElementVisitor]: http://docs.oracle.com/javase/6/docs/api/javax/lang/model/element/ElementVisitor.html
[javax.lang.model.type.TypeMirror]: http://docs.oracle.com/javase/6/docs/api/javax/lang/model/type/TypeMirror.html
[TypeVisitor]: http://docs.oracle.com/javase/6/docs/api/javax/lang/model/type/TypeVisitor.html