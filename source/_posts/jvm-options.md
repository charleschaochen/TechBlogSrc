title: JVM实用参数系列
date: 2014-07-02 13:45:28.0
tags:
- Java虚拟机
- JVM参数
categories:
- Java虚拟机

---

本文首发于CodeCentric博客的Useful JVM Flags系列，经原作者Sven Ruppert授权由并发编程网翻译并分享。JVM是Java Virtual Machine（Java虚拟机）的缩写，Java通过使用Java虚拟机屏蔽了与具体平台相关的信息，使得Java具备了一次编写，多处运行的特性。JVM一直是Java学习中的重点，也是难点。并发编程网组织翻译了JVM实用参数系列文章，...

<!-- more -->

本文首发于[CodeCentric][]博客的Useful JVM Flags系列，经原作者Sven Ruppert授权由[并发编程网][Link 1]翻译并分享。

JVM是Java Virtual Machine（Java虚拟机）的缩写，Java通过使用Java虚拟机屏蔽了与具体平台相关的信息，使得Java具备了一次编写，多处运行的特性。JVM一直是Java学习中的重点，也是难点。并发编程网组织翻译了JVM实用参数系列文章，旨在帮助大家了解JVM的结构以及相关参数。JVM实用参数系列一共包括八篇文章，由浅入深，从编译器、垃圾回收、内存调优等方面介绍JVM。  


特别感谢CodeCentric AG授权我们翻译此部分文章；同时感谢译者赵峰、郑旭东、Greenster、iDestiny、张军、李洪柱以及参与校对的主编们。由于翻译时间仓促，内容难免有疏漏之处，敬请批评指正。另外我们接下来会翻译一些关于垃圾回收算法和垃圾回收器的优质文章，敬请期待。

1、[JVM实用参数（一）JVM类型以及编译器模式][JVM_JVM]

2、[JVM实用参数（二）参数分类和即时（JIT）编译器诊断][JVM_JIT]

3、[JVM实用参数（三）打印所有XX参数及值][JVM_XX]

4、[JVM实用参数（四）内存调优][JVM]

5、[JVM实用参数（五）新生代垃圾回收][JVM 1]

6、[JVM实用参数（六）吞吐量收集器][JVM 2]

7、[JVM实用参数（七）CMS收集器][JVM_CMS]

8、[JVM实用参数（八）GC日志][JVM_GC]

  


原文出处：[JVM实用参数系列][JVM 3]


[CodeCentric]: https://blog.codecentric.de/en/
[Link 1]: http://ifeve.com/
[JVM_JVM]: http://ifeve.com/useful-jvm-flags-part-1-jvm-types-and-compiler-modes-2/
[JVM_JIT]: http://ifeve.com/useful-jvm-flags-part-2-flag/
[JVM_XX]: http://ifeve.com/useful-jvm-flags-part-3-printing-all-xx-flags-and-their-values/
[JVM]: http://ifeve.com/useful-jvm-flags-part-4-heap-tuning/
[JVM 1]: http://ifeve.com/useful-jvm-flags-part-5-young-generation-garbage-collection/
[JVM 2]: http://ifeve.com/useful-jvm-flags-part-6-throughput-collector/
[JVM_CMS]: http://ifeve.com/useful-jvm-flags-part-7-cms-collector/
[JVM_GC]: http://ifeve.com/useful-jvm-flags-part-8-gc-logging/
[JVM 3]: http://ifeve.com/useful-jvm-flags/