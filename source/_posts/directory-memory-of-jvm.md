title: Java堆外内存
date: 2014-10-02 14:10:45.0
tags:
- JVM
- 堆外内存
categories:
- Java虚拟机

---

什么是堆外内存Java对象分配的内存包括堆内存和堆外内存。堆外内存即计算机内存中除JVM堆内存之外的内存，例如一台32位的Windows服务器内存时2GB，其中1.6GB分配给了Java堆，那么剩余的0.4GB就是堆外内存。堆外内存的垃圾回收堆外内存的垃圾回收需要主要注意的一点是，堆外内存并不是JVM管辖的范围，堆外内存不足时并不会通知GC进行垃圾回收，只有等到JVM进行Full GC时才会顺带清...

<!-- more -->

#### **什么是堆外内存** ####

Java对象分配的内存包括堆内存和堆外内存。堆外内存即计算机内存中除JVM堆内存之外的内存，例如一台32位的Windows服务器内存时2GB，其中1.6GB分配给了Java堆，那么剩余的0.4GB就是堆外内存。

####     ####

#### **堆外内存的垃圾回收** ####

堆外内存的垃圾回收需要主要注意的一点是，**堆外内存并不是JVM管辖的范围，堆外内存不足时并不会通知GC进行垃圾回收**，只有等到JVM进行Full GC时才会顺带清理这部分内存。解决方案是指定JVM参数**-XX:MaxDirectMemorySize**，那么当堆外内存超过该限制时，就会触发一次Full GC进行回收，如果Full GC后还是没办法获取足够的直接内存，就会抛出OOM。

####     ####

#### **如何分配堆外内存** ####

分配堆外内存的方式有两种：

（1）通过Unsafe对象分配

在Java程序中不能直接使用Unsafe，因为这个类是不安全的，使用时有一定限制，但我们可以通过反射来获取Unsafe实例：

    /**
    	 * Get unsafe instance via reflection
    	 * 
    	 * @return
    	 * @throws NoSuchFieldException
    	 * @throws SecurityException
    	 * @throws IllegalArgumentException
    	 * @throws IllegalAccessException
    	 */
    	public static Unsafe getInstance() throws NoSuchFieldException,
    			SecurityException, IllegalArgumentException, IllegalAccessException {
    		Field theUnsafeInstance = Unsafe.class.getDeclaredField("theUnsafe");
    		theUnsafeInstance.setAccessible(true);
    		return (Unsafe) theUnsafeInstance.get(Unsafe.class);
    	}

  


获得unsafe对象后，我们可以调用其allocateMemory方法来分配直接内存：

    long pointer = unsafe.allocateMemory(10 * 1024 * 1024);

allocateMemory方法返回内存地址的指针

通过unsafe对象分配的直接内存必须手动去释放，否则将会导致内存泄露：

    unsafe .freeMemory(pointer);

  


（2）通过NIO ByteBuffer分配

ByteBuffer的allocateDirect方法可以用来分配堆外内存，常用于网络连接中作为收发数据的缓冲区。

    ByteBuffer buff = ByteBuffer.allocateDirect(10 * 1024 * 1024);

  


通过ByteBuffer分配的堆外内存可以被JVM回收，所以不需要手动去释放，但如果Direct ByteBuffer对象晋升到老年代，还是要触发Full GC去清理。