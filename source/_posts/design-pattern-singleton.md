title: 创建型设计模式 — 单例模式
date: 2014-05-27 11:32:27.0
tags:
- 设计模式
- 单例模式
categories:
- 设计模式

---

什么是单例模式单例模式顾名思义就是对类只实例化一次，在程序中复用同一个实例。单例模式能保证对象在一个JVM中只存在一个实例。 为什么要使用单例模式首先，使用单例模式可以减少new关键字的使用，降低开销，减轻垃圾回收器的压力其次，某些业务需求要求对象在整个系统中只存在一个实例，如果存在多个会对业务造成影响。例如一个交易系统的核心交易引擎实例，该实例管理和控制着所有核心交易，如果该实例存在多...

<!-- more -->

#### 什么是单例模式 ####

单例模式顾名思义就是对类只实例化一次，在程序中复用同一个实例。单例模式能保证对象在一个JVM中只存在一个实例。

####   ####

#### 为什么要使用单例模式 ####

首先，使用单例模式可以减少new关键字的使用，降低开销，减轻垃圾回收器的压力其次，某些业务需求要求对象在整个系统中只存在一个实例，如果存在多个会对业务造成影响。例如一个交易系统的核心交易引擎实例，该实例管理和控制着所有核心交易，如果该实例存在多个，必定会对系统造成致命的影响。

####   ####

#### 单例模式的实现 ####

1、普通方式（也称懒汉方式）

一般是声明一个私有的对象属性，然后声明一个静态的获取实例的方法，在方法中判断对象是否已经创建，若已经创建则返回实例，否则new一个实例返回。另外，需要将构造方法声明为私有，防止外部程序通过new方式创建实例。这种方式的缺点是不保证线程安全，多线程下不能保证实例化操作的同步。

代码示例：

    /**
     * 普通单例模式，也称懒汉方式，不保证线程安全
     *
     * @author Charles Chen
     *
     */
    public class LazySingleton {
       private LazySingleton() {
      }
    
       private static LazySingleton singleton;
    
       /**
       * Get instance
       *
       * @return
       */
       public static LazySingleton getInstance() {
     if (singleton == null) {
       singleton = new LazySingleton();
    }
     return singleton ;
      }
    }

2、饿汉方式

在声明对象属性时便进行实例化，然后声明一个静态方法直接返回这个实例。由于类加载的过程是线程互斥的，加载过程中的实例化操作具有原子性，因此这种方式可以保证线程安全，但并没有实现延迟加载，如果对象的实例化过程需要很大的消耗，而且实例不一定会被使用，那么这种方式会对性能造成一定影响。

代码示例：

    /**
     * 饿汉方式，声明对象属性时进行实例化，保证线程安全，不实现延迟加载
     *
     * @author Charles Chen
     *
     */
    public class HungrySingleton {
       private HungrySingleton() {
      }
    
       private static HungrySingleton singleton = new HungrySingleton ();
    
       /**
       * Get instance
       *
       * @return
       */
       public static HungrySingleton getInstance() {
     return singleton ;
      }
    
    }

3、同步方式

为了保证延迟加载，并保证线程安全，一种实现方式就是加同步锁。

（1）同步锁可以加在获取实例的方法上，代码如下：

    /**
    * Get instance
    * @return
    */
    public static synchronized SyncSingleton getInstance(){
     if(singleton == null){
       singleton = new SyncSingleton();
    }
     return singleton ;
    }

（2）将同步锁加在整个方法上会导致每次调用该方法都要对对象加锁，会严重影响性能。事实上，懒汉方式导致线程不安全的根本原因在于判断singleton是否为null与实例化singleton的操作时分开的，于是只需要将锁加在判断与实例化的代码上，代码如下：

    /**
    * Get instance
    *
    * @return
    */
    public static SyncSingleton getInstance() {
     synchronized (SyncSingleton.class) {
       if (singleton == null) {
     singleton = new SyncSingleton();
      }
    }
     return singleton ;
    }

（3）上面这种方式事实上已经将getInstance方法90%的代码加了锁，在性能上几乎没有提升。于是我们可以考虑先检查对象是否为空，当对象真的为空时，才进入同步块，同步块需要进行再一次的检查避免其他线程实例化了对象，这就是所谓的双重校验锁（double-checked locking）方法。代码如下：

    /**
    * Get instance
    *
    * @return
    */
    public static SyncSingleton getInstance() {
     if (singleton == null) {
       synchronized (SyncSingleton.class) {
     if (singleton == null) {
       singleton = new SyncSingleton();
    }
      }
    }
     return singleton ;
    }

双重校验锁的方式是不是就可以保证程序完全没有问题了呢？不一定。首先，从JVM层面上看，实例化一个对象的步骤包括：申请一块内存，调用构造方法初始化内存，将引用指向该内存块。而JVM并没有确定这三个步骤的先后顺序，那么就存在这么一个情况，JVM先申请了一块内存并将引用指向该内存，然后再进行初始化。这种情况下，如果A，B线程同时调用了getInstance方法，A线程中singleton对象已经指向了一块内存，但还未进行初始化，而B此时判断singleton应该不为null，那么B调用getInstacne方法返回的是一个为初始化的对象，使用该实例就会导致程序错误。

4、内部类方式（个人推荐）

在类内部声明一个私有的静态内部类，内部类中声明对象属性并对其进行实例化，获取实例的方法直接返回内部类中的对象。由于静态内部类只有当调用获取实例的方法时才被加载，加载的过程中完成对对象的实例化，因此这种方式既实现了延迟加载，又能保证线程安全。

代码示例：

    /**
     * 内部类方式，声明一个私有的静态内部类，内部类中声明对象属性并进行实例化
     *
     * @author Charles Chen
     *
     */
    public class InnerClassSingleton {
       private InnerClassSingleton() {
      }
    
       private static class InnerClassSingletonInstance {
     private static final InnerClassSingleton singleton = new InnerClassSingleton();
      }
    
       /**
       * Get instance
       * @return
       */
       public static InnerClassSingleton getInstance() {
     return InnerClassSingletonInstance.singleton;
      }
    }

以上4种方式需要将构造器声明为私有，以防止外部通过new方式创建对象，但是，Java的反射机制允许在类外部通过反射设置构造器的访问权限。代码如下：

    Class singletonClass = Class. forName("patterns.singleton.LazySingleton");
    Constructor[] constructors = singletonClass.getDeclaredConstructors();
    constructors[0].setAccessible( true);
    singleton = (LazySingleton) constructors[0].newInstance(null);

解决这种方法我们唯有在构造器中抛出异常。

5、枚举方式

还有另外一种实现单例的方式就是定义单枚举值的枚举类型。枚举事实上也是一个类，继承自java.lang.Enum，每个枚举值都是该类的一个实例。使用枚举方式创建实例的优势有：

（1） 枚举值在枚举类型加载过程中完成实例化，所以能保证线程安全

（2） 枚举实例不能通过new关键字创建，因为枚举类型的构造器是私有的，也不能通过反射机制来创建，会报”java.lang.IllegalArgumentException: Cannot reflectively create enum objects“异常。所以无法在程序外部创建新的实例

（3） 枚举类型可以保证反序列化后返回的是同一个实例。JVM对枚举类型的序列化有特殊的规定，序列化的结果只保存了枚举值的名称，即枚举值的toString方法的返回值，反序列化时会调用该枚举类型的valueOf方法，以枚举值的名称作为参数，返回同一个枚举实例。

唯一的缺点就是没有实现延迟加载。

代码示例：

    /**
     * 枚举方式，保证线程安全，防止反射或反序列化产生新的实例，不实现延迟加载
     * @author Charles Chen
     *
     */
    public enum EnumSingleton {
       INSTANCE;
       public void otherMehod() {
      }
    }

枚举方式应该是最简洁并且相对安全的单例实现模式，只不过枚举是在JDK5以后才有的类型，写法难免有相对生疏。