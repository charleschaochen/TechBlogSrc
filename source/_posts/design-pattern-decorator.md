title: 结构型设计模式 — 装饰器模式
date: 2014-07-13 19:21:33.0
tags:
- 设计模式
- 装饰器模式
categories:
- 设计模式

---

什么是装饰器模式？装饰器模式就是给一个对象动态地添加一些新的功能，实现对对象的扩展，类似一个装饰的过程。为什么要使用装饰器模式？我们知道继承也可以实现类的扩展，装饰器模式和继承的主要区别在于：继承是通过编写新的类继承原有的类，重写原有的方法，这个过程是在编译时静态完成的。而装饰器模式是通过比编写装饰器类，与被装饰对象实现同一个接口，并持有被装饰对象的实例，通过在调用实例之前或之后添加一些逻辑完成功...

<!-- more -->

#### **什么是装饰器模式？** ####

装饰器模式就是给一个对象动态地添加一些新的功能，实现对对象的扩展，类似一个装饰的过程。

####     ####

#### **为什么要使用装饰器模式？** ####

我们知道继承也可以实现类的扩展，装饰器模式和继承的主要区别在于：继承是通过编写新的类继承原有的类，重写原有的方法，这个过程是在编译时静态完成的。

而装饰器模式是通过比编写装饰器类，与被装饰对象实现同一个接口，并持有被装饰对象的实例，通过在调用实例之前或之后添加一些逻辑完成功能的扩展，而客户端代码需要创建一个对象实例传给装饰器类，相当于在运行时才动态地为对象进行扩展。

另外一方面，某些情况下，装饰器模式比继承要更加灵活。

####     ####

#### **如何实现装饰器模式？** ####

举一个实际的例子，有一个Pizza接口，声明了一个make方法用来制作披萨。现在已经定义了一个原味披萨类OriginPizza实现了Pizza接口并重写make方法，功能是制作不添加原料的披萨。如果需要对该类进行扩展，希望披萨可以加入培根、牛肉、水果原料。

如果通过继承的方式，我们需要编写一个新的类继承OriginPizza，并添加3个方法分别用来制作含有这三种原料的披萨。除此之外，还需要添加很多个方法完成这3种原料的组合。编写的类会十分臃肿，而且当需要添加新的原料时，又需要添加很多方法，既提高了编程难度，又不符合“对扩展开放，对修改关闭的原则”。

而是用装饰器模式，我们只需要编写三个装饰器类分别对应三种原料，每个装饰器类都需要实现Pizza接口，并持有一个Pizza接口的实例，在make方法中调用Pizza接口实例的make方法并添加新的原料。如果需要组合不同原料，很简单，只需要在客户端代码中，自由选择装饰器，动态地进行组合。

类图：

![Image 1][]  


示例代码：

披萨接口

    package patterns.structure.decorator;
    
    /**
     * 披萨接口
     *
     * @author Charles Chen
     *
     */
    public interface Pizza {
       void make();
    }

原味披萨类

    package patterns.structure.decorator;
    
    /**
     * 原味披萨
     *
     * @author Charles Chen
     *
     */
    public class OriginPizza implements Pizza {
    
       @Override
       public void make() {
     // TODO Auto-generated method stub
    System. out.println("制作披萨" );
      }
    
    }

牛肉装饰器

    package patterns.structure.decorator;
    
    /**
     * 牛肉装饰器类，添加牛肉原料
     *
     * @author Charles Chen
     *
     */
    public class BeefDecorator implements Pizza {
       private Pizza pizza ;
    
       public BeefDecorator(Pizza pizza) {
     this.pizza = pizza;
      }
    
       @Override
       public void make() {
     // TODO Auto-generated method stub
     pizza.make();
    System. out.println("添加原料： 牛肉" );
      }
    
    }

培根装饰器

    package patterns.structure.decorator;
    
    /**
     * 培根装饰器类，添加培根原料
     *
     * @author Charles Chen
     *
     */
    public class BaconicDecorator implements Pizza {
       private Pizza pizza ;
    
       public BaconicDecorator(Pizza pizza) {
     this.pizza = pizza;
      }
    
       @Override
       public void make() {
     // TODO Auto-generated method stub
     pizza.make();
    System. out.println("添加原料： 培根" );
      }
    
    }

水果装饰器

    package patterns.structure.decorator;
    
    /**
     * 水果装饰器类，添加水果原料
     *
     * @author Charles Chen
     *
     */
    public class FruitDecorator implements Pizza {
       private Pizza pizza ;
    
       public FruitDecorator(Pizza pizza) {
     this.pizza = pizza;
      }
    
       @Override
       public void make() {
     // TODO Auto-generated method stub
    pizza.make();
    System. out.println("添加原料： 水果" );
      }
    
    }

测试程序

    package patterns.structure.decorator;
    
    public class DecoratorTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
     // TODO Auto-generated method stub
    OriginPizza oPizza = new OriginPizza();
     // 对oPizza进行两层装饰，制作牛肉培根披萨
    Pizza pizza = new BaconicDecorator(new BeefDecorator(oPizza));
    pizza.make();
      }
    
    }

由此可见，装饰器模式比继承更加灵活，并且是动态的。


[Image 1]: 