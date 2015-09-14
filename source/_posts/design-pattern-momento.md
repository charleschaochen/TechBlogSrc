title: 行为型设计模式 — 备忘录模式
date: 2014-09-13 21:14:07.0
tags:
- 设计模式
- 备忘录模式
categories:
- 设计模式

---

什么是备忘录模式备忘录模式（Memento Pattern）又称快照模式（Snapshot Pattern），是对象的一种行为型模式。备忘录模式引入一个备忘录角色，用来记录一个对象某个时刻的状态（对象的状态通常由对象的属性值决定），以便在将来某个时刻恢复这个状态。备忘录模式的类图如下：Originator：原始角色，具有某些属性，提供创建备忘录和恢复备忘录的接口，以实现保存某一时刻状态和恢复状态M...

<!-- more -->

#### **什么是备忘录模式** ####

备忘录模式（Memento Pattern）又称快照模式（Snapshot Pattern），是对象的一种行为型模式。

备忘录模式引入一个备忘录角色，用来记录一个对象某个时刻的状态（对象的状态通常由对象的属性值决定），以便在将来某个时刻恢复这个状态。

备忘录模式的类图如下：

![Image 1][]  


  


Originator：原始角色，具有某些属性，提供创建备忘录和恢复备忘录的接口，以实现保存某一时刻状态和恢复状态

Memento：备忘录角色，具有与原始类需要保存的属性一致的属性，用来保存原始角色实例的状态

Caretaker：负责人角色，用来存储备忘录角色实例，客户端通过负责人获取备忘录实例，从而恢复原始角色的状态。负责人角色应当仅仅用于存储备忘录角色，而不应该读取其内容

#### 备忘录模式的序列图如下：    ####

![Image 1][]  


####     ####

示例程序：

  


Originator原始角色类

    package patterns.behavior.memento;
    
    /**
     * Originator POJO
     *
     * @author Charles Chen
     * @date 2014年9月13日
     */
    public class Originator {
          private String state;
    
          public String getState () {
                return state;
         }
    
          public void setState(String state) {
                this. state = state;
         }
    
          /**
          * Create memento for current object
          *
          * @return
          */
          public Memento createMemento() {
                return new Memento( this. getState());
         }
    
          /**
          * Restore memento for current object
          *
          * @param memento
          */
          public void restoreMemento(Memento memento) {
                this.setState( memento.getState());
         }
         
          public String toString() {
                return "Current state: " + this .getState();
         }
    }

  


  


Memento备忘录角色类

    package patterns.behavior.memento;
    
    /**
     * Memento for saving state of Originator
     *
     * @author Charles Chen
     * @date 2014年9月13日
     */
    public class Memento {
          private String state;
    
          public Memento(String state) {
                // TODO Auto-generated constructor stub
                this. state = state;
         }
    
          public String getState() {
                return state;
         }
    
          public void setState(String state) {
                this. state = state;
         }
    
    }

  


  


Caretaker负责人角色类

    package patterns.behavior.memento;
    
    /**
     * Caretaker for storaging memento
     *
     * @author Charles Chen
     * @date 2014年9月13日
     */
    public class Caretaker {
          private Memento memento;
    
          /**
          * Save memento
          *
          * @param memento
          */
          public void saveMemento(Memento memento) {
                this. memento = memento;
         }
    
          /**
          * Retrieve memento
          *
          * @return
          */
          public Memento retrieveMemento() {
                return this. memento;
         }
    }

  


  


客户端测试程序

    package patterns.behavior.memento;
    
    /**
     * Client test for memento design pattern
     *
     * @author Charles Chen
     * @date 2014年9月13日
     */
    public class ClientTest {
    
          public static void main(String[] args) {
                // TODO Auto-generated method stub
               Originator originator = new Originator();
               Caretaker caretaker = new Caretaker();
                originator.setState( "On");
               System. out.println( originator);
                // Save the state of the originator
                caretaker.saveMemento( originator.createMemento());
    
                // Change state of orginator
                originator.setState( "Off");
               System. out.println( originator);
    
                // Restore state
               originator.restoreMemento( caretaker.retrieveMemento());
               System. out.println( originator);
         }
    
    }

  


#### **为什么要用备忘录模式** ####

使用备忘录模式我们可以保存一个对象某个时刻的状态，从而在将来某个时候恢复对象的状态，常用于操作的撤销。

备忘录模式将对象的状态捕捉起来并存储在外部，不破坏原始对象的封装。

####     ####

#### **窄接口方式的备忘录模式** ####

从传统备忘录模式的实现可以看出，在原始类创建备忘录实例后，客户端或者负责人都可以对这个实例进行修改，而这种操作是不安全的。

备忘录实例应当只有原始类对象可以访问，而对客户端和负责人对象来说都是一个“黑箱”。

所以备忘录角色应当为原始对象提供宽接口，原始对象可以访问修改备忘录对象，而对于客户端和负责人对象，备忘录对象只提供一个窄接口，不允许访问和修改备忘录对象。

为了实现窄接口，我们可以为备忘录角色声明一个空接口，备忘录角色的实现类定义为原始类的私有内部类，那么只有原始类对象可以访问和修改备忘录对象，而外部程序只能访问这个空接口，而无法访问到备忘录具体的内容。

窄接口方式的备忘录模式类图如下：

![Image 1][]  


  


示例程序：

IMemento备忘录窄接口

    /**
     * Narrow interface of memento
     *
     * @author Charles Chen
     * @date 2014年9月13日
     */
    public interface IMemento {
    
    }

  


Originator原始角色类，Memento的实现类是其私有内部类

    package patterns.behavior.memento.narrow;
    
    /**
     * Originator POJO
     *
     * @author Charles Chen
     * @date 2014年9月13日
     */
    public class Originator {
          private String state;
    
          public String getState() {
                return state;
         }
    
          public void setState(String state) {
                this. state = state;
         }
    
          /**
          * Create memento for current object
          *
          * @return
          */
          public IMemento createMemento() {
                return new Memento( this.getState());
         }
    
          /**
          * Restore memento for current object
          *
          * @param memento
          */
          public void restoreMemento(IMemento memento) {
                if ( memento instanceof Memento) {
                     this.setState(((Memento) memento).getState());
               }
         }
    
          /**
          * Memento for saving state of orginator
          *
          * @author Charles Chen
          * @date 2014年9月13日
          */
          private class Memento implements IMemento {
                private String state;
    
                public Memento(String state) {
                     // TODO Auto-generated constructor stub
                     this. state = state;
               }
    
                public String getState() {
                     return state;
               }
    
                public void setState(String state) {
                     this. state = state;
               }
         }
    
          public String toString() {
                return "Current state: " + this .getState();
         }
    }

  


Caretaker负责人角色类

    package patterns.behavior.memento.narrow;
    
    /**
     * Caretaker for storaging memento
     *
     * @author Charles Chen
     * @date 2014年9月13日
     */
    public class Caretaker {
          private IMemento memento;
    
          public void saveMemento(IMemento memento) {
                this. memento = memento;
         }
    
          public IMemento retrieveMemento() {
                return this. memento;
         }
    }

  


客户端测试程序

    package patterns.behavior.memento.narrow;
    /**
     * Client test for memento design pattern with narrow interface
     * @author Charles Chen
     * @date 2014年9月13日
     */
    public class ClientTest {
    
          public static void main(String[] args) {
                // TODO Auto-generated method stub
               Originator originator = new Originator();
               Caretaker caretaker = new Caretaker();
               
                // Set state
                originator.setState( "On");
               System. out.println( originator);
               
                // Save current state
                caretaker.saveMemento( originator.createMemento());
               
                // Change state
                originator.setState( "Off");
               System. out.println( originator);
               
                // Restore state
               originator.restoreMemento( caretaker.retrieveMemento());
               System. out.println( originator);
         }
    
    }

  



[Image 1]: 