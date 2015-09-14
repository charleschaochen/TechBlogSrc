title: 结构型设计模式 — 代理模式
date: 2014-07-13 19:29:46.0
tags:
- 设计模式
- 代理模式
categories:
- 设计模式

---

什么是代理模式？代理模式就是为目标对象提供一种代理以控制管理对目标对象的访问。代理模式下有两种角色：代理类和委托类。代理类负责为委托类添加预处理逻辑、将消息传递给委托类以及添加后处理逻辑。为什么要使用代理模式？代理模式为对象提供一层中间访问层，起到保护目标对象的作用，可以防止客户端直接访问目标对象。除此之外，可以通过代理为目标对象添加一些预处理或后处理逻辑，例如日志操作等，既符合开闭原则，又使得目...

<!-- more -->

#### **什么是代理模式？** ####

代理模式就是为目标对象提供一种代理以控制管理对目标对象的访问。

代理模式下有两种角色：代理类和委托类。

代理类负责为委托类添加预处理逻辑、将消息传递给委托类以及添加后处理逻辑。

####     ####

#### **为什么要使用代理模式？** ####

代理模式为对象提供一层中间访问层，起到保护目标对象的作用，可以防止客户端直接访问目标对象。

除此之外，可以通过代理为目标对象添加一些预处理或后处理逻辑，例如日志操作等，既符合开闭原则，又使得目标对象可以只关注自身的逻辑，而无需关心这些额外的处理操作。

####     ####

#### **如何实现代理模式？** ####

实现代理模式的方式有两种：静态代理和动态代理。

（1）静态代理模式：编写代理类与委托类实现同一个接口，并持有委托对象实例。这种代理方式在编译时完成，代理类经编译生成字节码文件。

举个实际的例子，有一个士兵接口Soldier，提供执行任务doMission()和训练train()方法，该接口有一个实现类SoldierImpl，实现了这两个方法。现在需要在这两个方法记录这两个方法执行前后的时间，我们可以通过代理模式来实现。定义一个代理类SoldierProxy，实现Soldier接口，并持有Solider实例，在将调用消息传递给Solider实例之前和之后打印当前时间。类图如下：

![Image 1][]  


  


示例代码：

Soldier接口

    package patterns.structure.proxy;
    
    /**
     * 士兵接口，提供执行任务和训练方法
     *
     * @author Charles Chen
     *
     */
    public interface Soldier {
       void doMission();
    
       void train();
    }

SoldierImpl实现类

    package patterns.structure.proxy;
    
    import java.util.concurrent.TimeUnit;
    
    /**
     * 士兵实现类
     *
     * @author Charles Chen
     *
     */
    public class SoldierImpl implements Soldier {
    
       @Override
       public void doMission() {
            // TODO Auto-generated method stub
            System. out.println("Soldier is doing mission..." );
            try {
              TimeUnit. MILLISECONDS.sleep(2000);
            } catch (InterruptedException e) {
               // TODO Auto-generated catch block
              e.printStackTrace();
            }
      }
    
       @Override
       public void train() {
            // TODO Auto-generated method stub
            System. out.println("Soldier is training..." );
            try {
              TimeUnit. MILLISECONDS.sleep(1000);
            } catch (InterruptedException e) {
               // TODO Auto-generated catch block
              e.printStackTrace();
            }
      }
    
    }

SoldierProxy代理类

    package patterns.structure.proxy;
    
    import java.text.SimpleDateFormat;
    import java.util.Date;
    
    /**
     * 士兵代理类
     *
     * @author Charles Chen
     *
     */
    public class SoldierProxy implements Soldier {
       private Soldier soldier ;
    
       public SoldierProxy(Soldier soldier) {
            this.soldier = soldier;
       }
    
       @Override
       public void doMission() {
            // TODO Auto-generated method stub
            System. out.println("Start time: "
            + new SimpleDateFormat("HH:mm:ss" ).format(new Date()));
             soldier.doMission();
            System. out.println("Start time: "
            + new SimpleDateFormat("HH:mm:ss" ).format(new Date()));
       }
    
       @Override
       public void train() {
            // TODO Auto-generated method stub
            System. out.println("Start time: "
            + new SimpleDateFormat("HH:mm:ss" ).format(new Date()));
             soldier.train();
            System. out.println("Start time: "
            + new SimpleDateFormat("HH:mm:ss" ).format(new Date()));
        }
    
    }

测试程序

    package patterns.structure.proxy;
    
    public class ProxyTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
            // TODO Auto-generated method stub
            // Create proxy for soldier
            Soldier proxy = new SoldierProxy(new SoldierImpl());
    
             // Invoke methods
            proxy.doMission();
            proxy.train();
      }
    
    }

静态代理模式缺点：

a) 代理类需要实现接口的所有方法。如果接口的方法很多，且全部需要代理，代理添加的逻辑都是一致的，那么就需要为每个方法编写重复的添加逻辑，这会影响编程效率，并且会使代理类十分臃肿；

b) 如果接口需要添加i新的方法，代理类也需要添加新方法的实现。

（2）动态代理模式：在程序运行时动态地通过Java反射机制生成代理类的字节码，生成的字节码并不保存于任何文件中，而是通过类加载器加载到JVM中直接调用执行。

我们所熟知的Spring AOP使用的就是动态代理方式，Spring默认使用的是JDK动态代理，即对接口进行代理，如果需要针对某一具体类进行代理，可以使用CGLib代理。

示例代码：

代理调用处理器

    package patterns.structure.proxy;
    
    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.lang.reflect.Proxy;
    import java.text.SimpleDateFormat;
    import java.util.Date;
    
    /**
    * 动态代理调用处理器，封装代理的添加逻辑
    *
    * @author Charles Chen
    *
    */
    public class SoldierProxyHandler implements InvocationHandler {
     private Soldier soldier;
    
     public SoldierProxyHandler(Soldier soldier) {
      this.soldier = soldier;
     }
    
     /**
     * 获取动态生成的代理类实例
     *
     * @return
     */
     public Soldier getProxy() {
        return (Soldier) Proxy.newProxyInstance(soldier.getClass().getClassLoader(), soldier.getClass().getInterfaces(), this);
     }
    
     @Override
     public Object invoke(Object proxy, Method method, Object[] args)
       throws Throwable {
          // TODO Auto-generated method stub
          System.out.println("Start time: "
        + new SimpleDateFormat("HH:mm:ss").format(new Date()));
          method.invoke(soldier, null);
          System.out.println("Start time: "
        + new SimpleDateFormat("HH:mm:ss").format(new Date()));
          return null;
     }
    
    }

测试程序

    package patterns.structure.proxy;
    
    public class ProxyTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
            // TODO Auto-generated method stub
            // Create proxy for soldier
            SoldierProxyHandler handler = new SoldierProxyHandler(new SoldierImpl());
            Soldier proxy = handler.getProxy();
             // Invoke methods
            proxy.doMission();
            proxy.train();
      }
    
    }


[Image 1]: 