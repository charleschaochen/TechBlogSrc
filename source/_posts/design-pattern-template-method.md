title: 行为型设计模式 — 模板方法模式
date: 2014-08-02 23:36:44.0
tags:
- 设计模式
- 模板方法模式
categories:
- 设计模式

---

什么是模板方法模式模板方法模式就是定义一个抽象类，声明并实现一个模板方法作为所有子类的主要算法的模板，并声明一些基本方法作为算法的实现步骤，这些基本方法中，包含一些抽象方法作为算法实现的可变部分，让子类去实现具体的逻辑，那么不同子类可以用不同方式去实现这些抽象方法，从而使得它们从抽象类中继承而来的模板方法表现出不同的行为。基本方法中也可以包含一些具体方法作为算法实现的公共部分，子类可以选择直接重用...

<!-- more -->

#### **什么是模板方法模式** ####

模板方法模式就是定义一个抽象类，声明并实现一个模板方法作为所有子类的主要算法的模板，并声明一些基本方法作为算法的实现步骤，这些基本方法中，包含一些抽象方法作为算法实现的可变部分，让子类去实现具体的逻辑，那么不同子类可以用不同方式去实现这些抽象方法，从而使得它们从抽象类中继承而来的模板方法表现出不同的行为。基本方法中也可以包含一些具体方法作为算法实现的公共部分，子类可以选择直接重用它们，也可以选择重写覆盖它们。

  


模板方法模式下有两类角色：

**抽象模板角色**

抽象模板角色定义了一个模板方法作为主要算法的模板，并定义了一些抽象方法作为主要算法的实现步骤，让子类去实现具体的逻辑。抽象模板角色中还可以定义一些具体的方法作为主要算法的实现步骤，子类可以选择重用它们也可以选择重写覆盖它们。

**具体模板角色**

继承抽象模板角色，实现所有抽象方法，从而使得从抽象模板角色中继承而来的模板方法表现出不同的行为。

  


模板方法模式中的方法有两大类：

（1） 模板方法：将基本方法组合在一起形成主要算法的总框架。模板方法一般不允许子类去重写，所以一般声明为final

（2） 基本方法：模板方法中算法的实现步骤

  


基本方法有三类：

a） 抽象方法（abstract method）：算法的抽象部分，让子类去实现具体的逻辑

b） 具体方法（concrete method）：算法的某个必要步骤，在抽象类中给出具体的实现，一般不需要子类去重写，所以一般声明为private

c） 钩子方法（hook method）：算法的某个步骤，在抽象类中可以给出默认实现，子类也可以根据需要重写该类方法

  


模板方法模式的类图如下：

![Image 1][]  


  


  


#### **为什么要使用模板方法模式** ####

  


1. 模板方法模式很好地实现了代码复用，子类复用抽象类中定义的模板方法以及一些具体的基本方法

2. 模板方法模式实现了算法设计与具体实现的分离，算法设计者只需要定义抽象类并给出算法的调用步骤，步骤的具体实现由子类去负责。这种模式有利于程序设计的分工，例如在实现某个需求上，高级工程师只需要设计模板方法以及一些调用的基本方法，抽象部分可以由初级程序猿去实现

3. 模板方法模式比较灵活，易于扩展。模板方法模式将算法的可变部分定义为抽象方法，子类可以通过实现这些抽象方法来完成扩展，不需要修改模板方法，符合开闭原则

  


当一个问题的解决方案具有一个大体的框架，仅有一些解决步骤会针对不同情况有不同的实现时，我们可以考虑使用模板方法模式。

  


  


#### **模板方法模式的实现** ####

 

  模板方法模式下，需要实现两类角色：**抽象模板角色**和**具体模板角色**。抽象模板角色中，我们需要分别一些基本方法对应算法实现的各个步骤，对于算法的抽象部分，我们要将方法声明为**abstract**，对于算法的公共部分，我们给出方法的具体实现。最重要的，我们需要定义模板方法来组合这些基本方法，从而形成算法的大体框架。模板方法一般声明为**final**。

  举个实际的例子，现在有一个需求，需要根据用户输入的二元运算表达式得到计算结果。不管是什么运算，运算的大体步骤都是需要先从表达式中获取操作数，再进行运算，最后输出结果，所以这个算法具有一个大体的模板，可变部分就是具体的运算类型。因此，我们可以使用模板方法模式来解决这个问题。

  首先定义一个抽象类AbstractCalculator作为抽象模板角色，其中，模板方法是calculate(String exp)，接受一个表达式作为参数。在该方法中，首先调用抽象方法getOperator()获取具体的操作符，然后调用rerieveOperands(String exp, String operator)来根据操作符分割表达式获取操作数列表，接着调用抽象方法calculate(long num1, long num2)来执行具体的运算，最后调用postCalculation(long result)输出结果。

不难看出，getOperator()和calculate(long num1, long num2)是抽象方法，retrieveOperands(String exp, String operator)是具体方法，postCalculation(long result)是钩子方法。

于是，我们可以实现一个PlusCalculator来实现加法运算，实现getOperator()和calculate(long num1, long num2)方法，选择性重写postCalculation(long result)方法

类图如下所示：

![Image 1][]  


  


示例程序：

AbstractCalculator抽象类

    package patterns.behavior.templateMethod;
    
    import java.util.ArrayList;
    import java.util.List;
    
    /**
     * Abstract Template for Calculators
     * 
     * @author Charles Chen
     * 
     */
    public abstract class AbstractCalculator {
    	/**
    	 * Template method
    	 * 
    	 * First, call retrieveOperands() to retrieve operands from the input
    	 * expression with the operator
    	 * 
    	 * Then, call calculate() to execute calculation
    	 * 
    	 * Last, call postCalculate() to output the result
    	 * 
    	 * @param exp
    	 */
    	public final void calculate(String exp) {
    		List<Long> operands = this.retrieveOperands(exp, this.getOperator());
    		if (operands == null || operands.size() != 2) {
    			System.out.println("Expression is not valid");
    			return;
    		}
    		long result = this.calculate(operands.get(0), operands.get(1));
    		this.postCalculate(result);
    	}
    
    	/* BEGIN: Abstract methods that sub-classes should implement */
    	protected abstract String getOperator();
    
    	protected abstract long calculate(long num1, long num2);
    
    	/* END: Abstract methods that sub-classes should implement */
    
    	/**
    	 * Concrete method, retrieve the operands list from expression with the
    	 * operator
    	 * 
    	 * @param exp
    	 * @param operator
    	 * @return Operands list
    	 */
    	private List<Long> retrieveOperands(String exp, String operator) {
    		if (exp == null || operator == null) {
    			return null;
    		}
    		List<Long> operands = new ArrayList<Long>();
    		String[] nums = exp.split(operator);
    
    		try {
    			for (String num : nums) {
    				operands.add(Long.parseLong(num));
    			}
    		} catch (NumberFormatException e) {
    			// TODO: handle exception
    			return null;
    		}
    		return operands;
    	}
    
    	/**
    	 * Hook method, to output the result after calculation. Sub-classes can use
    	 * the default implement or override it
    	 * 
    	 * @param result
    	 */
    	protected void postCalculate(long result) {
    		System.out
    				.println("Abstract Calculator: Calculation is done, result is "
    						+ result);
    	}
    }

  


PlusCalculator加法运算实现类

    package patterns.behavior.templateMethod;
    
    /**
     * Plus Calculator, extends the Calculator and implements the abstract methods
     * 
     * @author Charles Chen
     * 
     */
    public class PlusCalculator extends AbstractCalculator {
    
    	@Override
    	protected long calculate(long num1, long num2) {
    		// TODO Auto-generated method stub
    		return num1 + num2;
    	}
    
    	@Override
    	protected String getOperator() {
    		// TODO Auto-generated method stub
    		return "\\+";
    	}
    
    	public void postCalculate(long result) {
    		System.out.println("Plus Calculator: Calculation is done, result is "
    				+ result);
    	}
    }

  


  


客户端测试程序

    package patterns.behavior.templateMethod;
    
    public class ClientTest {
    
    	/**
    	 * @param args
    	 */
    	public static void main(String[] args) {
    		// TODO Auto-generated method stub
    		AbstractCalculator calculator = new PlusCalculator();
    		calculator.calculate("13+85");
    	}
    
    }

  


  


#### **模板方法模式在servlet中的应用** ####

 

我们再编写servlet时需要继承HttpServlet抽象类，这里就是应用了模板方法模式，其中service()方法就是一个模板方法。service()方法可以根据request的method来调用不同的do方法，例如doGet()，doPost()等等，这些do方法在HttpServlet中被声明为抽象方法，具体的实现由我们编写的具体servlet去负责。


[Image 1]: 