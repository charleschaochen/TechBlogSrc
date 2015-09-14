title: 行为型设计模式 — 状态模式
date: 2014-10-12 12:35:14.0
tags:
- 设计模式
- 状态模式
categories:
- 设计模式

---

什么是状态模式状态模式允许对象在状态发生变化时执行不同的行为。状态模式是对象的一种行为模式，它提供一种机制来统一管理对象的多种状态以及状态的转移，使得对对象的调用随着对象状态的改变而执行不同的行为。状态模式的类图如下：状态模式下有三类角色：Context：上下文类，持有State属性并提供客户端感兴趣的接口State：状态接口ConcreteState：具体的状态类示例程序：State接口pack...

<!-- more -->

#### **什么是状态模式** ####

状态模式允许对象在状态发生变化时执行不同的行为。状态模式是对象的一种行为模式，它提供一种机制来统一管理对象的多种状态以及状态的转移，使得对对象的调用随着对象状态的改变而执行不同的行为。

状态模式的类图如下：

![Image 1][]  


  


状态模式下有三类角色：

**Context**：上下文类，持有State属性并提供客户端感兴趣的接口

**State**：状态接口

**ConcreteState**：具体的状态类

  


示例程序：

State接口

    package patterns.behavior.state;
    
    /**
     * State interface
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public interface State {
         void handle();
    }

  


具体的State类

    package patterns.behavior.state;
    
    /**
     * Concrete state 1
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class ConcreteState1 implements State {
    
         @Override
         public void handle() {
                // TODO Auto-generated method stub
               System. out.println( "Handle request by ConcreteState1");
         }
    
    }
    
    package patterns.behavior.state;
    
    /**
     * Concrete state 2
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class ConcreteState2 implements State {
    
         @Override
         public void handle() {
                // TODO Auto-generated method stub
               System. out.println( "Handle request by ConcreteState2");
         }
    
    }

  


Context上下文类

    package patterns.behavior.state;
    
    /**
     * Context
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class Context {
         private State state;
    
         public Context(State state) {
                // TODO Auto-generated constructor stub
                this. state = state;
         }
    
         /**
          * Change state
          *
          * @param state
          */
         public void setState(State state) {
                this. state = state;
         }
    
         /**
          * Request the state
          */
         public void request() {
                this. state.handle();
         }
    }

  


客户端测试程序

    package patterns.behavior.state;
    
    /**
     * Client test for state design pattern sample
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class ClientTest {
         public static void main(String[] args) {
               State state1 = new ConcreteState1();
               Context context = new Context( state1);
                context.request();
               State state2 = new ConcreteState2();
                context.setState( state2); // Change state
                context.request();
         }
    }

  


#### **为什么要使用状态模式** ####

(1) 使用状态模式可以将对象的调用与其状态解耦，对象的具体状态发生变化时，对象的调用不会受到影响

(2) 状态模式有利于对象状态的扩展，当对象有新的状态时，我们只需要实现新的状态类

  


#### **如何使用状态模式** ####

我们可以利用状态模式来实现一个用户下单流程，假设一个网购系统正在做促销活动，规定每个用户对同一个商品只能下单一次。

我们可以用NormalBuyer和RepeatBuyer来代表正常的下单用户和重复的下单用户，他们都实现了Buyer接口，相当于Buyer的两种状态。

使用OrderManager类来充当上下文角色，它维护一个列表记录用户下单的记录，提供order方法供客户端进行下单，order方法中，根据下单的内容判断当前用户是NormalBuyer还是RepeatBuyer，切换状态并执行相应的下单方法。

类图如下：

![Image 1][]  


  


示例程序：

Buyer接口

    package patterns.behavior.state.sample;
    
    /**
     * Buyer interface
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public interface Buyer {
         void order(OrderManager mgr, OrderRecord record);
    }

  


NormalBuyer类

    package patterns.behavior.state.sample;
    
    /**
     * Normal buyer
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class NormalBuyer implements Buyer {
    
         @Override
         public void order(OrderManager mgr, OrderRecord record) {
                // TODO Auto-generated method stub
               System. out.println( "User " + record.getUsername() + " orders "
                         + record.getItem());
                mgr.getRecords().add( record);
         }
    
    }

  


RepeatBuyer类

    package patterns.behavior.state.sample;
    
    /**
     * Buyer who order one item repeatedly
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class RepeatBuyer implements Buyer {
    
         @Override
         public void order(OrderManager mgr, OrderRecord record) {
                // TODO Auto-generated method stub
               System. out.println( "User " + record.getUsername() + " has bought "
                         + record.getItem() + " before, not allow to order again");
         }
    
    }

  


OrderRecord类

    package patterns.behavior.state.sample;
    
    /**
     * An Order record
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class OrderRecord {
         private String username;
         private String item;
    
         public OrderRecord(String username, String item) {
                // TODO Auto-generated constructor stub
                this. username = username;
                this. item = item;
         }
    
         public String getUsername() {
                return username;
         }
    
         public void setUsername(String username) {
                this. username = username;
         }
    
         public String getItem() {
                return item;
         }
    
         public void setItem(String item) {
                this. item = item;
         }
    
         @Override
         public boolean equals(Object obj) {
                // TODO Auto-generated method stub
                if ( this == obj)
                     return true;
                if ( obj instanceof OrderRecord) {
                    OrderRecord other = (OrderRecord) obj;
                     if ( this. username == other. username && this. item == other.item )
                          return true;
               }
                return false;
         }
    }

  


OrderManager类

    package patterns.behavior.state.sample;
    
    import java.util.ArrayList;
    import java.util.List;
    
    /**
     * Order manager, the context for buyers
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class OrderManager {
         private Buyer buyer;
         private List<OrderRecord> records;
    
         public OrderManager() {
                // TODO Auto-generated constructor stub
                records = new ArrayList<OrderRecord>();
         }
    
         /**
          * Order a item
          *
          * @param username
          * @param item
          */
         public void order(String username, String item) {
               OrderRecord record = new OrderRecord( username, item);
                if ( records.contains( record)) {
                     this. buyer = new RepeatBuyer();
               } else {
                     this. buyer = new NormalBuyer();
               }
                buyer.order( this, record);
         }
    
         public List<OrderRecord> getRecords() {
                return records;
         }
    }

  


客户端测试程序

    package patterns.behavior.state.sample;
    
    /**
     * Client test for state design pattern sample
     *
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class ClientTest {
    
         public static void main(String[] args) {
                // TODO Auto-generated method stub
               OrderManager mgr = new OrderManager();
                mgr.order( "charles ", "keyboard");
                mgr.order( "charles ", "mouse");
                mgr.order( "charles ", "keyboard");
         }
    
    }

  


#### **状态模式与策略模式的区别** ####

状态模式与策略模式的UML与实现方式很类似，但二者在应用场景和实现细节上还是有很大不同：

(1) 应用场景不同，策略模式常用于算法决策系统，封装多个算法并提供同一接口，客户端可以自由切换具体的算法。而状态模式是为了统一管理对象的状态和状态的转移

(2) 策略模式下，控制行为变化的是客户端，客户端可以自由切换算法执行不同的行为。而状态模式下，控制行为变化的往往是Context类或状态对象本身。状态在执行行为时可以接收Context类的实例，回调其方法改变状态，从而实现状态的转移。


[Image 1]: 