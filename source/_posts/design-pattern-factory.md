title: 创建型设计模式 — 工厂模式
date: 2014-05-27 10:55:06.0
tags:
- 设计模式
- 工厂模式
categories:
- 设计模式

---

什么是工厂模式由工厂来统一负责对象的实例化工作，用工厂方法代替new操作符，就是所谓的工厂模式。工厂模式广泛应用于各个流行的框架中，例如Spring的核心IoC容器，就是一个管理和实例化所有bean的工厂。 为什么要使用工厂模式首先，使用工厂来统一负责对象的实例化，可以简化客户端调用代码的初始化工作。其次，工厂模式可以将对象的客户端调用从具体实现中分离出来，客户端代码只需要关注对象必须满...

<!-- more -->

#### 什么是工厂模式 ####

由工厂来统一负责对象的实例化工作，用工厂方法代替new操作符，就是所谓的工厂模式。工厂模式广泛应用于各个流行的框架中，例如Spring的核心IoC容器，就是一个管理和实例化所有bean的工厂。

 

#### 为什么要使用工厂模式 ####

首先，使用工厂来统一负责对象的实例化，可以简化客户端调用代码的初始化工作。其次，工厂模式可以将对象的客户端调用从具体实现中分离出来，客户端代码只需要关注对象必须满足的接口，而不需要关注对象的具体实例化过程，实现程序的解耦。

 

#### 工厂模式的实现 ####

##### 普通工厂模式 #####

创建一个工厂类负责实现同一接口的类的实例化工作。在该工厂类中，需要声明一个方法用来返回产品实例，该方法通常接受一个参数，通过该参数决定返回的具体实例。

 

代码示例：

    import patterns.factory.beans.*;
    
    /**
     * 简单工厂模式，声明produce方法，根据传入的type参数返回对应的产品
     *
     * 缺点：produce方法需要对传入的参数进行判断以决定生产哪种具体的产品，增加了编程难度，并且如果传入的参数不合法，会导致对象无法正常创建
     *
     * @author Charles Chen
     *
     */
    public class SimpleFruitFactory {
           /**
           * Produce fruit with the specific type
           *
           * @param type
           * @return Fruit
           */
           public Fruit produce(String type) {
                 if ("apple" .equals(type)) {
                       return new Apple();
                }
                 if ("banana" .equals(type)) {
                       return new Banana();
                }
                System. out.println("Please input correct type." );
                 return null ;
          }
    
           /**
           * @param args
           */
           public static void main(String[] args) {
                 // TODO Auto-generated method stub
                SimpleFruitFactory factory = new SimpleFruitFactory();
                Fruit fruit = factory.produce( "apple");
                 if (fruit != null) {
                      fruit.introduce();
                }
          }
    
    }

**缺点：普通工厂模式通常需要定义一个返回类实例的方法，该方法通常需要接收一个参数，通过该参数的值来决定实例化哪个具体的类。这种方式会增加编程的复杂性，而且如果传入的参数不正确会导致无法正确地实例化。**

 

##### 工厂方法模式 #####

工厂方法模式是对普通工厂模式的改良，工厂类中不使用一个统一的方法来实例化所有的对象，而是针对每一类产品对象都声明一个独立的方法负责实例化。静态工厂方法模式就是在工厂方法模式的基础上，将所有实例化的方法声明为static，这样一来客户端代码不需要实例化工厂，就可以调用其工厂方法。Java并发包中的Executors类就是实现了静态工厂方法模式，来实例化多种线程池。

示例代码：

    import patterns.factory.beans.*;
    
    /**
     * 工厂方法模式，声明不同的方法负责生产不同的产品
     *
     * 缺点：不符合开闭原则，当需要添加新的产品时需要修改工厂类
     *
     * @author Charles Chen
     *
     */
    public class MethodFruitFactory {
          /**
          * Produce apple
          *
          * @return Apple
          */
          public static Fruit produceApple() {
                 return new Apple();
          }
    
          /**
           * Produce banana
           *
           * @return Banana
           */
           public Fruit produceBanana() {
                 return new Banana();
           }
    
           /**
           * @param args
           */
           public static void main(String[] args) {
                 // TODO Auto-generated method stub
                MethodFruitFactory factory = new MethodFruitFactory();
                Fruit fruit = factory.produceBanana();
                fruit.introduce();
          }
    }

**缺点：工厂方法模式的缺点是不符合开闭原则，即对扩展开放，对修改关闭。当程序需要扩展时，例如需要添加新的产品，我们不得不去修改工厂类**

 

##### 抽象工厂模式 #####

抽象工厂模式将工厂抽象为接口，该接口可以有多个具体的工厂实现类，每个具体的工厂类负责实例化一个具体的产品。这样设计方式符合了开闭原则，当需要添加新的产品时，只需要多实现一个工厂类去负责其实例化，而不需要修改工厂接口。

实例代码：

    import patterns.factory.beans.Fruit;
    
    /**
     * 抽象工厂模式，将工厂抽象为一个接口，每个实现该接口的工程类负责生产一种产品
     * 当需要添加新的产品时，只需要实现新的工程类即可
     * @author chaoch
     *
     */
    public interface AbstractFruitFactory {
           public Fruit produce();
    }
    
    
    import patterns.factory.beans.Apple;
    import patterns.factory.beans.Fruit;
    
    /**
     * Apple factory, produce apples
     *
     * @author Charles Chen
     *
     */
    public class AppleFactory implements AbstractFruitFactory {
    
           @Override
           public Fruit produce() {
                 // TODO Auto-generated method stub
                 return new Apple();
          }
    
           /**
           * @param args
           */
           public static void main(String[] args) {
                 // TODO Auto-generated method stub
                AppleFactory factory = new AppleFactory();
                Fruit fruit = factory. produce();
                fruit.introduce();
          }
    }
    
    
    import patterns.factory.beans.Banana;
    import patterns.factory.beans.Fruit;
    
    /**
     * Banana factory, produce bananas
     *
     * @author Charles Chen
     *
     */
    public class BananaFactory implements AbstractFruitFactory {
    
           @Override
           public Fruit produce() {
                 // TODO Auto-generated method stub
                 return new Banana();
          }
    
           /**
           * @param args
           */
           public static void main(String[] args) {
                 // TODO Auto-generated method stub
                BananaFactory factory = new BananaFactory();
                Fruit fruit = factory.produce();
                fruit.introduce();
          }
    }

 

 

 