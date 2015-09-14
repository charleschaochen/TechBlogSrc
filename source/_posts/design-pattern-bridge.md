title: 结构型设计模式 — 桥接模式
date: 2014-06-26 21:05:15.0
tags:
- 设计模式
- 桥接模式
categories:
- 设计模式

---

什么是桥接模式桥接模式将抽象部分与具体实现部分相互分离，使二者可以独立变化，动态结合。所谓抽象部分，一般表示系统的某些行为，而具体实现部分表示行为所作用的实体。桥接模式将抽象行为与具体实体解耦，客户端程序可以自由选择使用哪一种具体的实体执行哪一种具体的行为。桥接模式下有两种角色：抽象角色与具体角色。抽象角色通常需要维护一个具体角色的实例，通过具体角色的接口来完成业务逻辑。抽象角色和具体角色都可以有...

<!-- more -->

#### **什么是桥接模式** ####

**桥接模式将抽象部分与具体实现部分相互分离，使二者可以独立变化，动态结合**。所谓抽象部分，一般表示系统的某些行为，而具体实现部分表示行为所作用的实体。桥接模式将抽象行为与具体实体解耦，客户端程序可以自由选择使用哪一种具体的实体执行哪一种具体的行为。

桥接模式下有两种角色：抽象角色与具体角色。抽象角色通常需要维护一个具体角色的实例，通过具体角色的接口来完成业务逻辑。抽象角色和具体角色都可以有多个具体的实现，二者独立变化，客户端代码可以自由切换和组合两个角色的不同具体实现。

例如一个消息发送系统，我们可以将消息的发送方式看作是系统的抽象部分，而消息的类型看作是系统的具体部分。消息的发送方式有短信方式、邮件方式等等，消息的类型有通知消息、任务消息等等。我们使用桥接模式去解决此类问题，使消息的发送方式与具体的消息类型相互独立，各自有不同的具体实现，由客户端去选择和组合具体的方式与类型。

下图是传统的桥接模式类图：

![Image 1][]

其中Abstraction就是系统的抽象部分，Implementor是系统的具体部分。RefinedAbstraction是抽象部分的扩展实现，一般需要持有具体部分的对象实例，通过调用具体部分的实例来完成业务操作。ConcreteImplementorA和ConcreteImplementorB是具体部分的不同实现。

 

#### **为什么要使用桥接模式** ####

**使用桥接模式可以实现系统的抽象部分与具体部分的解耦，使两者可以独立变化，二者具体实现的桥接由客户端决定，从而省去了组合这两部分具体实现的代码，同时也有利于系统功能的扩展。**

一个系统往往有多个维度的变化，即行为有很多不同的具体实现，行为所作用的实体又可以有很多不同的具体实现，所以在实体与行为结合时就有很多种可能性。如果使用普通的继承方式来实现，就需要实现很多个类，每个类实现一种组合，并且当一个维度发生变化时，又需要添加多个类去适应这种变化。

例如上面提到的消息发送系统，如果使用普通的继承方式去解决，我们需要定义一个Message接口，该接口提供send方法用来发送消息。NoticeMessage类和TaskMessage类实现Message接口，分别表示两种不同的消息类型：通知消息和任务消息，而对于每一种消息，我们需要扩展若干个子类表示不同的消息发送方式，例如对于NoticeMessage，需要扩展NoticeMessageViaSms和NoticeMessageViaMail发示通过短信和邮件方式发送通知消息，对于TaskMessage，需要扩展TaskMessageViaSms和TaskMessageViaMail发示通过短信和邮件方式发送任务消息，类图如下：

![Image 1][]

如果此时需要对系统进行扩展，例如添加多一种警告消息类型，我们不仅要实现多一个类WarningMessage，还需要为这个类扩展两个子类表示两种发送方式，类图如下：

![Image 1][]

可见，这种实现方式非常不利于系统的扩展。

如果我们使用桥接模式来优化设计，我们可以将发送方式抽象出来，定义抽象类SendMessage作为不同发送方式的父类，而Message接口作为系统的具体部分。优化后的UML类图如下：

![Image 1][]

客户端程序可以自由选择和组合具体的发送方式和消息类型。

示例程序：

消息接口

    package patterns.structure.bridge;
    
    /**
     * 消息接口
     *
     * @author Charles Chen
     *
     */
    public interface Message {
           void send();
    }

通知消息类

    package patterns.structure.bridge;
    
    /**
     * 通知消息类型
     *
     * @author Charles Chen
     *
     */
    public class NoticeMessage implements Message {
           @Override
           public void send() {
                 // TODO Auto-generated method stub
                System. out.println("Message Type: Notice Message" );
          }
    }

任务消息类

    package patterns.structure.bridge;
    
    /**
     * 任务消息类型
     *
     * @author Charles Chen
     *
     */
    public class TaskMessage implements Message {
    
           @Override
           public void send() {
                 // TODO Auto-generated method stub
                System. out.println("Message Type: Task Message" );
          }
    
    }

警告消息类

    package patterns.structure.bridge;
    
    /**
     * 警告消息类型
     *
     * @author Charles Chen
     *
     */
    public class WarningMessage implements Message {
    
           @Override
           public void send() {
                 // TODO Auto-generated method stub
                System. out.println("Message Type: Warning Message" );
          }
    
    }

发送消息接口

    package patterns.structure.bridge;
    
    /**
     * 消息发送方式接口
     *
     * @author Charles Chen
     *
     */
    public abstract class SendMessage {
           protected Message message ;
    
           abstract void sendMessage();
    }

短信发送消息实现类

    package patterns.structure.bridge;
    /**
     * 短信方式发送消息
     * @author Charles
     *
     */
    public class SendMessageViaSms extends SendMessage {
           public SendMessageViaSms(Message message) {
                 this.message = message;
          }
    
           @Override
           void sendMessage() {
                 // TODO Auto-generated method stub
                System. out.println("Send message via SMS" );
                 message.send();
          }
    
    }

邮件发送消息实现类

    package patterns.structure.bridge;
    
    /**
     * 邮件方式发送消息
     *
     * @author Charles Chen
     *
     */
    public class SendMessageViaMail extends SendMessage {
           public SendMessageViaMail(Message message) {
                 this.message = message;
          }
    
           @Override
           void sendMessage() {
                 // TODO Auto-generated method stub
                System. out.println("Send message via email" );
                 message.send();
          }
    
    }

客户端测试程序

    package patterns.structure.bridge;
    
    /**
     * 测试程序
     *
     * @author Charles Chen
     *
     */
    public class ClientTest {
    
           /**
           * @param args
           */
           public static void main(String[] args) {
                 // TODO Auto-generated method stub
                 // 短信方式发送通知消息
                Message mess = new NoticeMessage();
                SendMessage sm = new SendMessageViaSms(mess);
                sm.sendMessage();
                
                 // 邮件方式发送任务消息
                mess = new TaskMessage();
                sm = new SendMessageViaMail(mess);
                sm.sendMessage();
          }
    
    }

#### **JDBC的桥接模式** ####

JDBC桥是使用桥接模式的典型案例，其中具体部分是数据库驱动，所有数据库驱动都实现Driver接口，客户端可以指定使用不同的数据库驱动，例如OracleDirver，MysqlDriver，SQLServerDriver等等。而抽象部分应该是JDBC API，客户端可以面向JDBC API实现不同的具体数据库操作，例如查询数据库、添加数据库记录达到，相当于是对JDBC API的扩展。

而DriverManager既充当了Connection连接对象的工厂，又起到桥接的作用，DriverManager根据具体数据库驱动，创建数据库连接实例，客户端通过DriverManager获取连接实例，调用JDBC API完成数据库操作，因此DriverManager相当于在具体的数据库操作与具体的数据库驱动之间搭了一个桥。

![Image 1][]  



[Image 1]: 