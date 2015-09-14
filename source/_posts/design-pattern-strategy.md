title: 行为型设计模式 — 策略模式
date: 2014-07-13 19:57:36.0
tags:
- 设计模式
- 策略模式
categories:
- 设计模式

---

什么是策略模式策略模式（Strategy）定义了一系列算法，并将它们各自独立封装，实现一个公共的算法接口，使用算法时通过调用该接口执行具体的算法行为，使得客户端可以在不同算法之间自由切换。策略模式常用于算法决策系统，当需要在不同情况下使用不同的策略时，或者策略需要经常改变时，可以选择使用策略模式。策略模式下有三个角色：抽象策略角色、具体策略角色与环境角色。具体策略角色用来定义算法的具体行为，而抽象...

<!-- more -->

#### **什么是策略模式** ####

策略模式（Strategy）定义了一系列算法，并将它们各自独立封装，实现一个公共的算法接口，使用算法时通过调用该接口执行具体的算法行为，使得客户端可以在不同算法之间自由切换。策略模式常用于算法决策系统，当需要在不同情况下使用不同的策略时，或者策略需要经常改变时，可以选择使用策略模式。

策略模式下有三个角色：抽象策略角色、具体策略角色与环境角色。具体策略角色用来定义算法的具体行为，而抽象策略角色是所有具体策略角色的公共接口。环境角色为客户端提供算法的使用接口，它持有一个抽象策略角色的实例，具体的策略实例由客户端传入，通过调用该实例的接口，在运行时动态绑定到具体的算法调用。

策略模式的UML图如下：

#### **![Image 1][]**    ####

####     ####

#### **为什么要使用策略模式** ####

1.  策略模式实现算法的使用与具体行为之间的解耦，算法的变化并不会影响算法的使用
2.  策略模式有利于算法的扩展，当需要添加新的策略时，只需要实现新的算法类即可。

####     ####

####     ####

#### **如何实现策略模式** ####

编写一个实现策略模式的程序，通常需要以下步骤：1. 编写抽象策略接口2. 实现不同的具体策略类3. 编写环境类，持有抽象策略接口对象，提供该对象的setter和getter方法，或者声明以该接口为参数的构造方法，使客户端可以传入具体的策略类实例。并提供使用策略的方法供客户端调用。

举个实际的例子，系统需要一套实现加、减、乘、除的算法来完成一些简单运算。如果不使用策略模式，我们可能需要编写一个运算类，定义四个运算方法来实现加、减、乘、除运算，这种实现方式相当于将算法的实现与使用耦合起来，当算法需要改变时，需要修改使用算法的方法。且当算法需要扩展时，需要修改运算类，并不符合“对扩展开放，对修改关闭”的原则。

此时我们可以使用策略模式来解决该问题。定义运算算法接口Calculator，作为这套加、减、乘、除的简单运算算法的公共接口，提供calculate()抽象方法实现运算。Calculator接口具有Plus、Minus、Multiply以及Divide四个具体的实现类，CalculatorContext类充当环境角色，提供useCalculator()方法来使用Calculator算法。UML类图如，

![Image 1][]  


  


示例程序：

Calculator接口

    package patterns.behavior;
    
    /**
     * Calculator Interface
     *
     * @author Charles Chen
     *
     */
    public interface Calculator {
       double calculate(double operand1, double operand2);
    }

Plus、Minus、Multiply以及Divide具体运算类

    package patterns.behavior;
    
    /**
     * Add Operation
     *
     * @author Charles Chen
     *
     */
    public class Plus implements Calculator {
    
       @Override
       public double calculate(double operand1, double operand2) {
         // TODO Auto-generated method stub
         return operand1 + operand2;
      }
    
    }
    
    package patterns.behavior;
    
    /**
     * Subtract Operation
     *
     * @author Charles Chen
     *
     */
    public class Minus implements Calculator {
    
       @Override
       public double calculate(double operand1, double operand2) {
         // TODO Auto-generated method stub
         return operand1 - operand2;
      }
    
    }
    
    package patterns.behavior;
    
    /**
     * Multiply Operation
     *
     * @author chaoch
     *
     */
    public class Multiply implements Calculator {
    
       @Override
       public double calculate(double operand1, double operand2) {
         // TODO Auto-generated method stub
         return operand1 * operand2;
      }
    
    }
    
    package patterns.behavior;
    
    /**
     * Divistion Operation
     *
     * @author Charles Chen
     *
     */
    public class Divide implements Calculator {
    
       @Override
       public double calculate(double operand1, double operand2) {
     // TODO Auto-generated method stub
     if (operand1 == 0)
       return 0;
     if (operand2 == 0)
       return Double.MAX_VALUE;
     return operand1 / operand2;
      }
    
    }

环境类CalculatorContext

    package patterns.behavior;
    
    /**
     * Calculator Context
     *
     * @author Charles Chen
     *
     */
    public class CalculatorContext {
       private Calculator calculator ;
    
       public CalculatorContext(Calculator calculator) {
        this.calculator = calculator;
      }
    
       /**
       * Use the calculator
       *
       * @param operand1
       * @param operand2
       * @return
       */
       public double useCalculator(double operand1, double operand2) {
        return calculator .calculate(operand1, operand2);
      }
    }

客户端测试程序

    package patterns.behavior;
    
    public class ClientTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
         // TODO Auto-generated method stub
         double operand1 = 18;
         double operand2 = 9;
    
        CalculatorContext context = new CalculatorContext(new Plus());
         // Use addition calculator
        System. out.println(context.useCalculator(operand1, operand2));
    
         // Use subtraction calculator
        context.setCalculator( new Minus());
        System. out.println(context.useCalculator(operand1, operand2));
    
         // Use multiply calculator
        context.setCalculator( new Multiply());
        System. out.println(context.useCalculator(operand1, operand2));
    
         // Use division calculator
        context.setCalculator( new Divide());
        System. out.println(context.useCalculator(operand1, operand2));
      }
    
    }

#### **策略模式在Java API中的使用** ####

Java集合框架中，涉及到排序时，通常需要传入一个Comparator比较器实例，作为排序依据的比较策略。例如创建TreeSet或TreeMap时，可以通过构造器传入一个Comparator实例，或者调用Collections.sort方法进行排序时，也可以传入一个Comparator实例作为排序策略。

这里就是实现了策略模式。Comparator作为不同的具体比较器的公共接口，排序方法通过调用该接口，从而使用具体的比较策略进行排序。


[Image 1]: 