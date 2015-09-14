title: JDK监控和故障处理工具 (I)
date: 2014-09-07 15:59:43.0
tags:
- JVM
- 性能调优
categories:
- Java虚拟机

---

JDK提供的命令行工具大多是jdk/lib/tools.jar类库的一层简单的封装， 其中包括了一些在运行时监控虚拟机数据、定位程序故障、优化程序性能的工具。Sun JDK提供的监控和故障处理工具常用的有：jps (JVM Process Status)虚拟机进程状态工具。显示当前系统中正在运行的虚拟机进程状态信息，包括虚拟机进程的本地虚拟机ID（LVMID），以及虚拟机执行的主类jstat (J...

<!-- more -->

JDK提供的命令行工具大多是jdk/lib/tools.jar类库的一层简单的封装， 其中包括了一些在运行时监控虚拟机数据、定位程序故障、优化程序性能的工具。

Sun JDK提供的监控和故障处理工具常用的有：

 *  jps (JVM Process Status)虚拟机进程状态工具。显示当前系统中正在运行的虚拟机进程状态信息，包括虚拟机进程的本地虚拟机ID（LVMID），以及虚拟机执行的主类
 *  jstat (JVM Statistics Monitoring Tool)虚拟机统计监控工具，用来收集JVM各方面的运行数据
 *  jinfo (Configuration Infomation for Java)虚拟机配置信息工具，用来实时查看和调整虚拟机参数
 *  jmap (Memory Map for Java)用来生成JVM的内存转储快照(Heap Dump)文件
 *  jhat (JVM Heapdump Anaysis Tool)用来分析JVM的内存转储快照文件。它会搭建一个HTTP服务器，使用户可以在浏览器上查看分析结果
 *  jstack (Stack trace for Java)显示虚拟机线程快照

####     ####

#### **JPS** ####

(1)  jps用来查看当前系统中运行的所有虚拟机的状态信息，可以查看所有虚拟机的本地虚拟机ID（LVMID，若虚拟机是本地虚拟机，LVMID等同于PID），以及虚拟机执行的之类。

jps还可以用来查看开启了RMI服务的远程虚拟机状态信息。

  


(2)  jps命令选项有

\-q ： 只显示LVMID

\-l ： 显示虚拟机执行的主类的全名（包括包路径），如果虚拟机执行的是jar包，显示jar包路径

\-m ：显示虚拟机执行主类时传入的参数

\-v ： 显示虚拟机启动时的JVM参数

####     ####

#### **JSTAT** ####

(1)  jstat（JVM Statistics Monitoring Tool）是JVM统计监控工具，用来监视虚拟机的各种运行时状态信息，它可以显示本地或远程虚拟机的类装载、内存、垃圾回收、JIT编译等运行时数据。jstat是运行时定位虚拟机性能问题的首选工具

jstat命令格式： jstat \[options\] vmid \[interval\] \[count\]

vmid表示虚拟机ID，如果是本地虚拟机，vmid即lvmid，也即是PID，如果是远程虚拟机，vmid的格式是：protocol://lvmid@hostname\[:port\]/servername

interval和count用来指定查询间隔和查询次数，interval可以为s或ms，不指定interval和count表示只查询一次

  
(2)  jstat常用的选项有：

\-class : 查看类装载卸载的数量、耗时、空间大小

\-gc ： 查看Java堆各区域的内存状况、GC次数、GC耗时等等

\-gccapaciry : 查看Java堆各区域可用的最大、最小空间、GC次数、GC耗时等等

\-gcutil ： 查看Java堆各区域的内存使用百分比，GC次数、GC耗时等等

\-gccause： 与-gcutil功能一样，区别在于-gccause会输出导致上一次GC的原因

\-gcnew : 显示新生代各区域的内存状况、GC次数、GC耗时等等

\-gcold : 显示老年代各区域的内存状况、GC次数、GC耗时等等

\-gcnewcapacity ： 与-gcnew功能基本相同，但输出主要关注各区域可用的最大最小空间

\-gcoldcapacity ：与-gcold功能基本相同，但输出主要关注各区域可用的最大最小空间

\-compiler ： 输出被JIT编译器已经执行的编译任务数、编译失败的任务数、编译失败的方法、耗时等等

\-printcompilation ： 输出已经被JIT编译器编译的方法

  


例如，执行 jstat -gc ，得到以下信息表头：

  


S0C S1C S0U S1U EC EU OC OU PC PU YGC YGCT FGC FGCT GCT

  


SOC: 其中一块Survior的总容量

S1C：另一块Survivor的总容量

S0U：第一块Survivor已使用的容量

S1U：第二块Survivor已使用的容量

EC：Eden区域的总容量

EU：Eden区域已使用的容量

OC：老年代总容量

OU：老年代已使用的容量

PC：永久代总容量

PU：永久代已使用的容量

YGC：Minor GC的次数

YGCT：Minor GC的耗时

FGC：Full GC的次数

FGCT：Full GC的耗时

GCT：GC的总耗时

####     ####

####  ####

#### **JINFO** ####

(1)  jinfo的作用是实时查看和调整虚拟机各项参数

我们知道使用jps -v也可以查看当前系统中所有虚拟机进程的JVM参数，但打印出来的参数都是显示指定的，如果要查看系统默认的JVM参数，就需要用到jinfo

（使用java -XX:+PrintFlagsFinal也可以查看虚拟机默认的参数）

(2) jinfo用法：

 *  jinfo -flag  vmid
    
    查看虚拟机的某个参数
    
    例如：
    
    jinfo -flag SurvivorRatio 8804
    
    结果：-XX:SurvivorRatio=8
 *  jinfo -flag MaxHeapSize 8804
    
    查看最大堆空间
 *  jinfo -flag MaxNewSize 8804
    
    查看最大新生代空间
 *  jinfo -flag \[+|-\] vmid开启或关闭某个虚拟机参数，+为开启，-为关闭
    
    例如：
    
    jinfo -flag +PrintGCDateDetails 8804
    
    虚拟机将开启PrintGCDateDetails参数u，GC时打印日期信息
 *  jinfo -flag name=value vmid设置某个虚拟机参数值
    
    例如：
    
    jinfo -flag MaxHeapSize=20971520 8864
 *  jinfo -flags vmid
    
    查看虚拟机所有显式指定的参数
 *  jinfo -sysprops
    
    打印系统属性

####     ####

#### **JMAP** ####

(1)  jmap(Memory Map for Java)工具用来生成堆转储快照，即heapdump文件。

所谓的heapdump是一个JVM进程在某个时间点的内存快照。我们知道通过一些虚拟机参数也可以获取heapdump文件，例如-XX:+HeapDumpOnOutOfMemoryError可以让虚拟机在发生OOM时生成dump文件，-XX:HeapDumpOnCtrlBreak可以让虚拟机在Ctrl+Break后生成dump文件。

  


(2) jmap使用：

 *  jmap -dump:\[live,\]format=b,file=<filename>
    
    生成heapdump文件，live子参数说明只dump出存活的对象，format=b说明dump文件格式为二进制文件，file指明文件路径，一般后缀为.bin 
 *  jmap -histo
    
    打印堆中对象统计信息，包括类名、实例个数、占用总空间。例如：
    
    num     \#instances         \#bytes  class name  
    \----------------------------------------------  
       1:         19417       82208296  \[I  
       2:        257909       28885808  java.net.SocksSocketImpl  
       3:         29964       25696952  \[B  
       4:        774416       12390656  java.lang.Object  
       5:        256326       10253040  java.util.HashMap$KeyIterator  
       6:         59357        9770416  <constMethodKlass>  
       7:        257902        8252864  java.net.Socket
 *  jmap -heap
    
    打印当前堆详细信息，包括堆相关参数、使用哪种回收器、分代状况等。例如：
    
    Heap Configuration:  
       MinHeapFreeRatio = 40  
       MaxHeapFreeRatio = 70  
       MaxHeapSize      = 1073741824 (1024.0MB)  
       NewSize          = 1310720 (1.25MB)  
       MaxNewSize       = 17592186044415 MB  
       OldSize          = 5439488 (5.1875MB)  
       NewRatio         = 2  
       SurvivorRatio    = 8  
       PermSize         = 21757952 (20.75MB)  
       MaxPermSize      = 174063616 (166.0MB)  
      
    Heap Usage:  
    PS Young Generation  
    Eden Space:  
       capacity = 299237376 (285.375MB)  
       used     = 137029128 (130.68115997314453MB)  
       free     = 162208248 (154.69384002685547MB)  
       45.792784922696285% used  
    From Space:  
       capacity = 29491200 (28.125MB)  
       used     = 10747904 (10.25MB)  
       free     = 18743296 (17.875MB)  
       36.44444444444444% used  
    To Space:  
       capacity = 29163520 (27.8125MB)  
       used     = 0 (0.0MB)  
       free     = 29163520 (27.8125MB)  
       0.0% used  
    PS Old Generation  
       capacity = 715849728 (682.6875MB)  
       used     = 48553344 (46.3040771484375MB)  
       free     = 667296384 (636.3834228515625MB)  
       6.782616812002197% used  
    PS Perm Generation  
       capacity = 43188224 (41.1875MB)  
       used     = 43080424 (41.084693908691406MB)  
       free     = 107800 (0.10280609130859375MB)  
       99.75039492246775% used
    
      
    
    
      
    
 *  jmap -finalizerinfo
    
    打印正在F-Queue中等待Finalizer线程执行finalize()方法的对象，只在Linux/Solaris有效

  
