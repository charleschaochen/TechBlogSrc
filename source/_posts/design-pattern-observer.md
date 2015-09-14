title: 行为型设计模式 — 观察者模式
date: 2014-08-09 14:27:53.0
tags:
- 设计模式
- 观察者模式
categories:
- 设计模式

---

什么是观察者模式观察者模式定义了类之间一对多的依赖关系，多个观察者对象同时监听一个目标对象，当目标对象发生状态的改变时，会通知所有观察者对象，使它们自动执行一些行为。观察者模式也称发布-订阅模式，类似我们常用的RSS订阅，当RSS源发生更新时，RSS阅读器会自动加载更新的内容。Java图形化用户界面编程中的控件事件机制也是观察者模式的应用案例，例如一个Button控件，我们可以在上面注册点击监听器...

<!-- more -->

#### **什么是观察者模式** ####

观察者模式定义了类之间一对多的依赖关系，多个观察者对象同时监听一个目标对象，当目标对象发生状态的改变时，会通知所有观察者对象，使它们自动执行一些行为。

观察者模式也称发布-订阅模式，类似我们常用的RSS订阅，当RSS源发生更新时，RSS阅读器会自动加载更新的内容。

Java图形化用户界面编程中的控件事件机制也是观察者模式的应用案例，例如一个Button控件，我们可以在上面注册点击监听器ActionListener作为观察者，当点击事件发生时，通过回调监听器的方法来通知观察者执行某些行为。

观察者模式的类图如下：

**![Image 1][]**  


  


**AbstractSubject**： 抽象目标类，声明了一个观察者对象列表，并定义了register，remove和notifyObservers方法用来注册、移除和通知观察者。

**ConcreteSubject**：具体的目标类，继承自AbstractSubject。声明了state变量表示其状态，定义了change方法来改变状态，change方法中，调用了notifyObservers方法来通知observers列表中所有的观察者对象，执行其update方法

**Observer**：观察者接口，声明update方法

**ConcreteObserver**：具体的观察者对象

####     ####

#### **为什么要使用观察者对象？** ####

1. 观察者模式实现了类与类之间的消息通知机制，当被一个对象发生变化时，可以通知其他一些对象执行某些行为

2. 观察者模式实现观察者与被观察者的分离，被观察者不需要知道具体的观察者以及观察者的数量，而是通过持有被观察者接口的集合，并提供添加、移除观察者的接口，让客户端根据需要添加和移除观察者。

3. 灵活，容易扩展。当需要扩展新的观察者，只需要实现新的类，并通过被观察者的添加接口添加到观察者集合中。

####     ####

#### **如何实现观察者模式？** ####

从前面提到的观察者模式类图可以看出，实现观察者模式主要需要实现四类角色：抽象被观察者类，具体被观察者类，观察者接口以及具体观察者类。其实JDK本身已经提供了对观察者模式的支持。在java.util工具包下，提供了Oberver接口和Observable类，作为观察者接口和被观察者模板类。

Observer接口主要提供update方法，当被观察者发生状态的改变时，会调用所有观察者的update方法，并将自身作为参数传递给观察者，观察者可以回调被观察者的方法

![Image 1][]  


  


Observable类相当于一个观察者的模板类，持有Observer接口的Vector集合，并提供addObserver和deleteObserver接口添加和移除观察者，以及notifyObservers方法提醒观察者。

![Image 1][]  


  


Observable接口维护一个changed布尔标识，当被观察者状态发生变化时，可以调用setChanged方法将changed标识设为true。当调用notifyObservers方法时，首先会判断当前changed标识是否为true，如果是执行提醒所有观察者的操作，最后将changed标识设置为false。

notifyObservers方法源码

    public void notifyObservers(Object arg) {
           /*
             * a temporary array buffer, used as a snapshot of the state of
             * current Observers.
             */
            Object[] arrLocal;
    
           synchronized (this ) {
              /* We don't want the Observer doing callbacks into
               * arbitrary code while holding its own Monitor.
               * The code where we extract each Observable from
               * the Vector and store the state of the Observer
               * needs synchronization, but notifying observers
               * does not (should not).  The worst result of any
               * potential race-condition here is that:
               * 1) a newly-added Observer will miss a
               *   notification in progress
               * 2) a recently unregistered Observer will be
               *   wrongly notified when it doesn't care
               */
              if (!changed)
                    return;
                arrLocal = obs.toArray();
                clearChanged();
            }
    
            for (int i = arrLocal.length-1; i>=0; i--)
                ((Observer)arrLocal[i]).update( this, arg);
        }

  


我们利用Observer接口和Observable类轻松地实现观察者模式。例如我们可以模拟一个RSS订阅的过程，定义RssSource模拟RSS源，作为被观察者，实现一个RssReader模拟RSS阅读器，作为观察者。类图如下：

![Image 1][]  


  


示例代码

被观察者 — RssSource订阅源类实现

    package patterns.behavior.observer;
    
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;
    import java.util.Observable;
    
    public class RssSource extends Observable {
    	private List<String> articles = new ArrayList<String>();
    
    	/**
    	 * Post a new article
    	 * 
    	 * @param article
    	 */
    	public void postArticle(String article) {
    		if (!articles.contains(article)) {
    			articles.add(article);
    			this.setChanged();
    		}
    		this.notifyObservers(new Date());
    	}
    
    	/**
    	 * Delete a article
    	 * 
    	 * @param article
    	 */
    	public void delArticle(String article) {
    		if (articles.remove(article)) {
    			this.setChanged();
    		}
    		this.notifyObservers(new Date());
    	}
    
    	/**
    	 * List articles
    	 */
    	public void listArticles() {
    		for (String article : articles) {
    			System.out.println(article);
    		}
    	}
    }

  


观察者 — RSS阅读器类实现

    package patterns.behavior.observer;
    
    import java.text.SimpleDateFormat;
    import java.util.Date;
    import java.util.Observable;
    import java.util.Observer;
    
    public class RssReader implements Observer {
    	/**
    	 * Observer update method, being called in notifyObservers method in
    	 * Observable
    	 */
    	public void update(Observable o, Object arg) {
    		// TODO Auto-generated method stub
    		if (arg instanceof Date) {
    			Date date = (Date) arg;
    			System.out.println(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss")
    					.format(date)
    					+ "  Articles list is changed");
    		}
    
    		if (o instanceof RssSource) {
    			RssSource source = (RssSource) o;
    			System.out.println("New articles list:");
    			source.listArticles();
    		}
    	}
    
    }

  


客户端测试程序

    package patterns.behavior.observer;
    
    public class ClientTest {
    
    	/**
    	 * @param args
    	 */
    	public static void main(String[] args) {
    		// TODO Auto-generated method stub
    		RssSource source = new RssSource();
    		// Add a observer
    		source.addObserver(new RssReader());
    
    		// Add articles
    		source.postArticle("Introduce to Java observer design pattern");
    		source.postArticle("How to use Java util Observer and Observable");
    
    		// Delete a article
    		source.delArticle("Introduce to Java observer design pattern");
    
    	}
    
    }

  


程序输出：

2014-08-09 13:12:28  Articles list is changed  
New articles list:  
Introduce to Java observer design pattern  
2014-08-09 13:12:28  Articles list is changed  
New articles list:  
Introduce to Java observer design pattern  
How to use Java util Observer and Observable  
2014-08-09 13:12:28  Articles list is changed  
New articles list:  
How to use Java util Observer and Observable  



[Image 1]: 