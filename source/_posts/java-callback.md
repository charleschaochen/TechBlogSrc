title: Java回调机制
date: 2014-07-26 21:16:31.0
tags:
- 回调
- 异步调用
categories:
- 技术研发

---

我们知道，软件模块之间的调用方式分为三类：同步调用、回调和异步调用。同步调用是一种阻塞式调用，调用方要等待对方执行完毕才返回，它是一种单向调用。回调是一种双向调用模式，被调用方在接口被调用时也会调用调用方的接口。异步调用：与同步调用相反，是一种非阻塞的调用，当调用方调用被调用方的接口后，无需等待其执行完毕，可以继续执行其他操作。当被调用方执行完毕后，通过调用调用方的接口告知其执行完毕的消息。异步调...

<!-- more -->

我们知道，软件模块之间的调用方式分为三类：**同步调用、回调**和**异步调用**。

同步调用是一种阻塞式调用，调用方要等待对方执行完毕才返回，它是一种单向调用。

回调是一种双向调用模式，被调用方在接口被调用时也会调用调用方的接口。

异步调用：与同步调用相反，是一种非阻塞的调用，当调用方调用被调用方的接口后，无需等待其执行完毕，可以继续执行其他操作。当被调用方执行完毕后，通过调用调用方的接口告知其执行完毕的消息。

异步调用需要通过回调机制来实现，当调用被调用方接口时，调用方通常需要将自身实例作为参数传入到被调用方的接口中，那么当调用方执行完毕后，才可以通过调用该实例的接口向调用方传递消息。

####     ####

#### **Java回调机制** ####

C++的回调函数指的是通过函数指针调用的函数，我们可以把一个函数指针作为参数传入到另一个函数中，在该函数执行过程中通过函数指针回调指针所指向的该函数。

而我们知道Java语言并没有指针，但我们可以通过接口实现回调机制。关于Java的回调，我们可以搜索到各种形象的应用场景举例与实现。总结来说，回调的应用场景有两类：

  


1. 类似事件处理机制的场景。定义回调接口，将其实现类的实例作为参数传入调用方的接口中，调用方在调用过程中通过传入的回调接口实例回调其方法。我们可以通过回调来模拟一个按钮的点击事件。例如，我们定义了一个MyButton类，具有click方法，它接受一个回调接口MyActionListener作为参数，执行时回调该接口的具体处理逻辑，实现点击操作的事件处理。示例代码：

  


MyActionListener接口

    package callback;
    
    public interface MyActionListener { 
    	void execute();
    }

  


MyActionListener实现类 

    package callback;
    
    public class MyActionListenerImpl implements MyActionListener {
    
       @Override
       public void execute() {
             // TODO Auto-generated method stub
            System. out .println("Process button click action" );
      }
    }

  


MyButton类

    package callback;
    
    /**
     * Monitor button behavior
     * 
     * @author Charles Chen
     * 
     */
    public class MyButton {
    	/**
    	 * Click action
    	 * 
    	 * @param listener
    	 */
    	public void click(MyActionListener listener) {
    		System.out.println("Button is clicked");
    		listener.execute();
    	}
    }

  


客户端测试类

    package callback;
    
    public class ClientTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
             // TODO Auto-generated method stub
            MyButton button = new MyButton();
            button.click( new MyActionListenerImpl());
      }
    }

  


客户端调用时也可以使用匿名内类方式

    package callback;
    
    public class ClientTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
             // TODO Auto-generated method stub
            MyButton button = new MyButton();
            button.click( new MyActionListener() {
    
                   @Override
                   public void execute() {
                         // TODO Auto-generated method stub
                        System. out .println("Process button click action");
                  }
    
            });
      }
    }

  


看到这里你可能会觉得疑惑，明明只有MyButton调用了MyActionListener的接口，回调所谓的双向调用体现在哪儿呢？ 

我的理解是，这里的调用方是客户端程序，被调用方是按钮API。客户端程序利用按钮API提供的回调接口MyActionListener实现了回调方法，对于按钮来说，客户端程序调用了其click方法传入回调接口实例，即“you call me”，而按钮的click方法在执行过程中又调用了回调方法，即“I call you back”，于是实现了双向调用。

Java API中也提供了类似这种场景的很多回调接口，例如java.io.File类的list方法，可以接受一个FilenameFilter过滤器作为参数，该过滤器就是一个回调接口，我们可以看下以下程序：

    package callback;
    
    import java.io.File;
    import java.io.FilenameFilter;
    import java.util.Arrays;
    
    
    public class ListFileTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
             // TODO Auto-generated method stub
            String dirPath = "d:/" ;
            File dir = new File(dirPath);
            String[] files = dir.list( new FilenameFilter() {
    
                   @Override
                   public boolean accept(File dir, String name) {
                         // TODO Auto-generated method stub
                        String suffix = ".xml" ;
                         if (name.indexOf(suffix) > -1) {
                               return true ;
                        }
                         return false ;
                  }
    
            });
            System. out .println(Arrays.toString(files));
      }
    }

  


FilenameFilter就是一个回调接口，它定义了一个回调方法accept用于过滤文件，当File对象调用list方法时，首先会获取该文件夹下所有文件列表，然后根据传入的FilenameFilter实现类实例，调用其accept方法过滤文件列表。

 

2. 类似消息传递机制的场景，一般用于异步调用中。调用方调用被调用方接口时，将自身实例作为参数传入被调用方接口，被调用方执行完毕后，通过传入的实例回调调用方的方法实现消息传递。例如，我们定义了一个回调接口MyCallback，具有回调方法callback，调用者MyCaller实现了MyCallback接口，并定义了call方法调用MyCallable线程，传入自身实例，当MyCallable线程执行完毕后，回调MyCaller的callback方法，流程图如下：

  


![Image 1][]  


  


实例程序;

MyCallback接口

    package callback.async;
    
    public interface MyCallback { 
    	void callback();
    }

 

MyCaller类

 

    package callback.async;
    
    public class MyCaller implements MyCallback {
    
       public void call() {
             new Thread( new MyCallable( this)).start();
            System. out .println("MyCallable is called" );
      }
    
       @Override
       public void callback() {
             // TODO Auto-generated method stub
            System. out .println("MyCallable is done" );
      }

  


MyCallable类

    package callback.async;
    
    import java.util.concurrent.TimeUnit;
    
    public class MyCallable implements Runnable { 
    
       private MyCaller caller ;
    
       public MyCallable(MyCaller caller) {
             this .caller = caller;
      }
    
       @Override
       public void run() {
             // TODO Auto-generated method stub
             try {
                  TimeUnit. SECONDS .sleep(1);
                  System. out .println("MyCallable is executing" );
            } catch (InterruptedException e) {
                   // TODO Auto-generated catch block
                  e.printStackTrace();
            }
             caller .callback();
      }
    }

  


客户端测试程序

    package callback.async;
    
    public class ClientTest {
    
    	/**
    	 * @param args
    	 */
    	public static void main(String[] args) {
    		// TODO Auto-generated method stub
    		MyCaller caller = new MyCaller();
    		caller.call();
    	}
    
    }

  


  


#### **回调的作用** ####

1, 回调可以实现事件机制与异步调用过程中的消息传递

2. 回调有利于程序的扩展，增强程序的灵活性。我们可以在自己的程序中提供回调接口，让客户端自己去实现回调的具体逻辑

  


####     ####

#### **回调与策略模式** ####

乍一看回调和策略模式的实现方法非常接近，可以说是没什么区别，但二者本质上还是有区别的。

首先回调是一种机制，而策略模式是一种行为型的设计模式，我认为，策略模式是基于回调机制实现的一种设计模式，如果说策略模式是一种设计思想，那么回调机制便是这种思想的实现方式。

另外，策略模式的应用场景比回调机制更加细化，它一般应用在算法决策系统，当一个问题有多种解决方法，且程序中需要在多种方法之间进行切换，可以使用策略模式。回调机制多用于事件处理或异步调用中的消息传递。


[Image 1]: 