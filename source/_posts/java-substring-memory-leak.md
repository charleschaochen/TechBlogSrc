title: substring()内存泄露隐患
date: 2014-09-13 13:39:35.0
tags:
- 内存泄露
- substring
categories:
- Java虚拟机

---

 我们知道对于一个字符串，我们可以调用其 substring(int beginIndex, int endIndex)方法返回一个下标从beginIndex到endIndex-1的子字符串。JDK6的substring方法与JDK7的是不同的版本，JDK6的substring方法可能带来内存泄露问题。内存泄露是指Java堆中某些对象已经不被程序所使用，但仍然存在指向这些对象的引用，即存...

<!-- more -->

 我们知道对于一个字符串，我们可以调用其 substring(int beginIndex, int endIndex)方法返回一个下标从beginIndex到endIndex-1的子字符串。JDK6的substring方法与JDK7的是不同的版本，JDK6的substring方法可能带来内存泄露问题。  
  
内存泄露是指Java堆中某些对象已经不被程序所使用，但仍然存在指向这些对象的引用，即存在到GC Root的路径，因此GC没办法回收这些对象所占的内存，导致这部分内存发生泄漏。

内存泄露区别于内存溢出，内存溢出是指程序中所需要创建的对象的内存空间超过堆可用的空间。  
  


**String的数据结构**  
  


String字符串的数据实际上是来自一个字符数组，String对象主要是由三个属性构成：  
（1） char value\[\]  
字符数组  
（2） int offset  
起始下标  
（3） int count  
字符串长度  
  
String字符串的值就是value数组从offset开始长度为count的部分  
  
substring方法发生了什么  
调用了substring方法，事实上是创建了一个新的String对象，JDK6下，这个新的String对象的value数组指向的还是原来的数组，不同的是offset和count的值不同。  
例如以下程序：  
  


    String str = "hello world";
    str = str.substring(1, 5);

  
str的变化如下图：  
![Image 1][]  
  
可以看到虽然str引用指向了新的String实例，但其value属性仍然指向原来的char数组

  


JDK版本的substring源码：  


    // Constructor
    String(int offset, int count, char value[]) {
        this.value = value;
        this.offset = offset;
        this.count = count;
    }
    
    public String substring(int beginIndex, int endIndex) {
        // 如果substring的范围是从0到当前字符串的右边界，则会返回自身实例
        // 否则将使用value作为数据返回新的字符串对象
        return ((beginIndex == 0) && (endIndex == count)) ? this :
            new String(offset + beginIndex, endIndex - beginIndex, value);
    }

  
  
可以看到，substring之后是利用同一个字符数组对象创建新的字符串对象  
  
  
  


**substring方法的内存泄露隐患**  
  
  


JDK6下，如果当前程序只需要用到一个很长的字符串中的一个很小的子串，这很有可能会导致内存泄露。因为程序中只用到了一个很小的子串，却持有了一个很大的字符数组的引用，导致这个字符数组很大一部分空间不能被回收。  
  
JDK7对substring方法进行了改进，substring方法将在堆内存中生成包含所需子串的新字符数组，新的字符对象的value引用指向这个字符数组，如下图：  
  
![Image 1][]  


  


主要的实现逻辑是在初始化String时，根据边界拷贝了一个新的字符数组：  
  


    this.value = Arrays.copyOfRange(value, offset, offset+count);

  
  



[Image 1]: 