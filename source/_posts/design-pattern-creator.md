title: 创建型设计模式 — 建造者模式
date: 2014-06-12 11:56:53.0
tags:
- 设计模式
- 建造者模式
categories:
- 设计模式

---

什么是建造者模式建造者模式和工厂模式都是由一个类来负责对象的实例化工作，不同在于，建造者模式是针对那些较为复杂的、创建过程需要多个步骤的对象。建造者模式下一般有三个角色：产品、建造者（Builder）以及指导者（Director）。建造者中通常声明了多个方法，每个方法表示一个独立的创建产品的步骤，而指导者负责将建造者的步骤组合起来，从而完成对象的构建。其中，建造者通常抽象为一个接口，可以有多个具体...

<!-- more -->

#### 什么是建造者模式 ####

建造者模式和工厂模式都是由一个类来负责对象的实例化工作，不同在于，建造者模式是针对那些较为复杂的、创建过程需要多个步骤的对象。

建造者模式下一般有三个角色：产品、建造者（Builder）以及指导者（Director）。建造者中通常声明了多个方法，每个方法表示一个独立的创建产品的步骤，而指导者负责将建造者的步骤组合起来，从而完成对象的构建。

其中，建造者通常抽象为一个接口，可以有多个具体的建造者实现类，不同的构造类具有不同的构造对象的步骤。而指导者中，通常提供一个方法接受建造者实例，组合建造者中的创建步骤，最终返回一个完整的对象，指导者不需要关心创建对象的具体逻辑，只需要负责组合这些步骤。

 

#### 为什么要使用建造者模式 ####

使用建造者模式的优点有：

1.  简化创建对象的客户端代码。客户端不需要关注创建对象的复杂步骤，只需要调用指导者接口就可以完成对象的创建
2.  实现对象的创建与具体步骤的分离解耦。指导者可以接受不同的构建者实例，创建出不同的对象，当创建对象的具体步骤需要变化时，我们只需要实现多一个建造者，而不需要修改指导者的构建代码，符合“多扩展开放，对修改关闭的原则”

####   ####

#### 建造者模式的实现 ####

举一个实际的示例，

Product产品有三个属性：partA, partB, partC，构造Product对象需要三个步骤来设置这三个属性

Builder是构造者接口，声明了三个方法分别设置Product的三个属性，还有一个build方法用于获取设置完的对象

Director是指导者类，声明了construct方法，接收一个Builder对象参数，并调用Builder对象的三个步骤，最后返回build方法的返回的对象

UML类图如下：

![Image 1][]

示例代码：

Product产品类

 

    /**
     * 产品角色
     *
     * @author Charles Chen
     *
     */
    public class Product {
           private String partA ;
           private String partB ;
           private String partC ;
    
           public String getPartA() {
                 return partA ;
          }
    
           public void setPartA(String partA) {
                 this.partA = partA;
          }
    
           public String getPartB() {
                 return partB ;
          }
    
           public void setPartB(String partB) {
                 this.partB = partB;
          }
    
           public String getPartC() {
                 return partC ;
          }
    
           public void setPartC(String partC) {
                 this.partC = partC;
          }
    
           public String toString() {
                 return "PartA: " + getPartA() + "\nPartB: " + getPartB() + "\nPartC: "
                            + getPartC();
          }
    }

Builder建造者接口

    /**
     * 建造者接口
     * @author Charles Chen
     *
     */
    public interface Builder {
           void buildPartA();
    
           void buildPartB();
    
           void buildPartC();
    
          Product build();
    }

 

具体的建造者类Builder1, Builder2

    /**
     * 建造者1
     *
     * @author Charles Chen
     *
     */
    public class Builder1 implements Builder {
           private Product product ;
    
           public Builder1() {
                 product = new Product();
          }
    
           @Override
           public Product build() {
                 // TODO Auto-generated method stub
                 return product ;
          }
    
           @Override
           public void buildPartA() {
                 // TODO Auto-generated method stub
                 product.setPartA("Build part A from Builder 1" );
          }
    
           @Override
           public void buildPartB() {
                 // TODO Auto-generated method stub
                 product.setPartB("Build part B from Builder 1" );
          }
    
           @Override
           public void buildPartC() {
                 // TODO Auto-generated method stub
                 product.setPartC("Build part C from Builder 1" );
          }
    
    }
    
    /**
     * 建造者2
     *
     * @author Charles Chen
     *
     */
    public class Builder2 implements Builder {
           private Product product ;
    
           public Builder2() {
                 product = new Product();
          }
    
           @Override
           public Product build() {
                 // TODO Auto-generated method stub
                 return product ;
          }
    
           @Override
           public void buildPartA() {
                 // TODO Auto-generated method stub
                 product.setPartA("Build part A from Builder 2" );
          }
    
           @Override
           public void buildPartB() {
                 // TODO Auto-generated method stub
                 product.setPartB("Build part B from Builder 2" );
          }
    
           @Override
           public void buildPartC() {
                 // TODO Auto-generated method stub
                 product.setPartC("Build part C from Builder 2" );
          }
    
    }

 

Director指导者类

    /**
     * 指导者类
     *
     * @author Charles Chen
     *
     */
    public class Director {
           /**
           * 组合传入的建造者的构造步骤，返回对象实例
           *
           * @param builder
           * @return
           */
           public Product construct(Builder builder) {
                builder.buildPartA();
                builder.buildPartB();
                builder.buildPartC();
                 return builder.build();
          }
    }

客户端测试程序

    /**
           * @param args
           */
           public static void main(String[] args) {
                 // TODO Auto-generated method stub
                Director director = new Director();
    
                Builder builder = new Builder1(); // 创建建造者1
                Product product = director.construct(builder); // 利用建造者1构建对象
                System.out.println(product); // 打印对象
                System.out.println();
    
                builder = new Builder2(); // 创建建造者2
                product = new Director().construct(builder); // 利用建造者2构建对象
                System.out.println(product); // 打印对象
          }

 

##### 建造者模式应用于多属性对象的创建 #####

《Effective Java》中提到一个建造者模式在构建多属性对象中的应用案例，这种设计方式也是非常值得借鉴。

假如一个类有很多个属性，其中有些属性是在初始化时必须赋值的，而有些是可选的。

例如类User：

    public class User {
           private long userId ;
           private String name ;
           private int age ;
           private String email ;
           private String country ;
           private String address ;
           private String phone ;
    }

其中useId和name是必须的，其他事可选的。

那么在设计这样的类时，一般来说有两种方案：

1.  声明一个接收必须属性作为参数的构造器，再声明多个接受不同可选属性的构造器。这种方案需要很多个构造器，会导致类比较臃肿，不容易维护
2.  为每个属性提供getter和setter，客户端显式调用setter去设置可选的属性。这种方案会增加客户端初始化的代码复杂性

此时我们可以考虑使用建造者模式。

在该类的内部定义一个静态内部类作为该类的建造者，负责该类属性的初始化工作，而外围类的构造器作为指导者的角色，接受建造者实例，根据建造者实例为属性赋值，完成初始化。

在建造者内部类中，需要声明与外围类同样的属性列表，并提供一个接受必须属性作为参数的构造器，然后提供一系列流式接口风格的方法为属性设值（所谓流式接口风格，即声明一系列方法，每个方法负责对一个属性进行设值，设值之后返回当前实例，那么外部程序就可以通过连续调用方法的方式初始化对象）。

另外，外围类的构造器需要声明为私有的，防止外部程序通过new方法方式创建实例。

代码示例：

    public class User {
           private long userId ;
           private String name ;
           private int age ;
           private String email ;
    
           /**
           * 私有构造器，接受建造者对象，根据建造者初始化属性值
           *
           * @param builder
           */
           private User(UserBuilder builder) {
                 this.userId = builder.userId;
                 this.name = builder.name;
                 this.age = builder.age;
                 this.email = builder.email;
          }
    
           public static class UserBuilder {
                 private long userId ;
                 private String name ;
                 private int age ;
                 private String email ;
    
                 /**
                 * 构造器，对必须属性赋值
                 *
                 * @param userId
                 * @param name
                 */
                 public UserBuilder(long userId, String name) {
                       this.userId = userId;
                       this.name = name;
                }
    
                 /* 流式接口风格方法 */
                 public UserBuilder age(int age) {
                       this.age = age;
                       return this ;
                }
    
                 public UserBuilder email(String email) {
                       this.email = email;
                       return this ;
                }
    
                 public User build() {
                       return new User(this);
                }
                 /* 流式接口风格方法 */
          }
    
           public long getUserId() {
                 return userId ;
          }
    
           public void setUserId(long userId) {
                 this.userId = userId;
          }
    
           public String getName() {
                 return name ;
          }
    
           public void setName(String name) {
                 this.name = name;
          }
    
           public int getAge() {
                 return age ;
          }
    
           public void setAge(int age) {
                 this.age = age;
          }
    
           public String getEmail() {
                 return email ;
          }
    
           public void setEmail(String email) {
                 this.email = email;
          }
    
           public String toString() {
                 return "User ID: " + userId + "\nUser Name: " + name + "\nUser Age: "
                            + age + "\nUser Email: " + email;
          }
    
           /**
           * 测试程序
           *
           * @param args
           */
           public static void main(String[] args) {
                User user = new User.UserBuilder(1, "chenchao").age(23).email(
                             "399931011@qq.com").build();
                System. out.println(user);
          }
    }

 


[Image 1]: 