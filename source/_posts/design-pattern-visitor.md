title: 行为型设计模式 — 访问者模式
date: 2014-10-19 20:38:05.0
tags:
- 设计模式
- 访问者模式
categories:
- 设计模式

---

什么是访问者模式访问者模式的定义是：封装某些作用于某种数据结构中各元素的访问，使得在不改变该数据结构的前提下修改或新增作用于这些元素的操作。简单来说，访问者模式就是引入一个访问者角色统一负责对目标对象的所有具体实现的访问。访问者模式的类图如下：访问者模式下有四类角色：（1）Element：被访问者抽象角色。声明接受访问者实例的accept接口，这个接口接收一个访问者实例，并声明实际的操作接口（2）...

<!-- more -->

#### **什么是访问者模式** ####

访问者模式的定义是：封装某些作用于某种数据结构中各元素的访问，使得在不改变该数据结构的前提下修改或新增作用于这些元素的操作。简单来说，访问者模式就是引入一个访问者角色统一负责对目标对象的所有具体实现的访问。

访问者模式的类图如下：

![Image 1][]  


  


访问者模式下有四类角色：

（1）**Element**：被访问者抽象角色。声明接受访问者实例的accept接口，这个接口接收一个访问者实例，并声明实际的操作接口

（2）**ConcreteElement**：被访者具体角色。实现Element，实现accept方法，在该方法中调用访问者的visit方法并将自身作为参数传入，供访问者访问。实现具体的操作方法

（3）**Visitor**：访问者抽象角色。声明针对所有具体被访问者操作的接口，一般有多少个具体的被访问者，就声明多少个方法

（4）**ConcreteVisitor**：访问者具体角色。实现抽象访问者接口，实现具的操作

  


#### **为什么要使用访问者模式** ####

（1）访问者模式可以实现对一类对象所有具体元素的统一操作

（2）访问者模式实现对象与作用于对象的操作的解耦，操作的修改不会影响被访问的对象

（3）访问者模式便于操作的扩展，如果对被访问对象有新的操作，直接实现一个新访问者即可

访问者模式的主要缺点是，当被访问者有新的具体实现时，需要调整所有的访问者逻辑，添加对新实例的操作

####     ####

#### **如何使用访问者模式** ####

在财务应用中，单据Transaction有很多具体的单据类型，例如发票Invoice，贷项通知单CreditMemo，借项通知单DebitMemo等等，对单据的操作有做账Accounting，有校验Validate，而针对不同的单据，具体的操作是不同的。我们可以将作用于单据的操作抽象为访问者，例如AccountingVisitor，ValidationVisitor，具体的单据就是被访问的具体元素。

类图如下：

![Image 1][]  


  


示例代码：

单据接口Transaction

    package patterns.behavior.visitor;
    /**
     * Transaction interface
     * @author Charles Chen
     * @date 2014年10月19日
     */
    public interface Transaction {
    	void accept(Visitor visitor);
    
    	void accounting();
    
    	void validate();
    }

  


访问者接口Visitor

    package patterns.behavior.visitor;
    
    /**
     * Visitor interface
     * 
     * @author Charles Chen
     * @date 2014年10月19日
     */
    public interface Visitor {
    	void visit(Invoice invoice);
    
    	void visit(CreditMemo creditMemo);
    
    	void visit(DebitMemo debitMemo);
    }

  


三类单据Invoice、CreditMemo和DebitMemo的具体实现

    package patterns.behavior.visitor;
    
    /**
     * Invoice implementation
     *
     * @author Charles Chen
     * @date 2014年10月19日
     */
    public class Invoice implements Transaction {
          @Override
          public void accept(Visitor visitor) {
                // TODO Auto-generated method stub
                visitor.visit( this);
         }
    
          @Override
          public void accounting() {
                // TODO Auto-generated method stub
               System. out.println( "Do accounting for Invoice" );
         }
    
          @Override
          public void validate() {
                // TODO Auto-generated method stub
               System. out.println( "Validate Invoice" );
         }
    }
    
    package patterns.behavior.visitor;
    
    /**
     * Credit Memo implementation
     *
     * @author Charles Chen
     * @date 2014年10月19日
     */
    public class CreditMemo implements Transaction {
          @Override
          public void accept(Visitor visitor) {
                // TODO Auto-generated method stub
                visitor.visit( this);
         }
         
          @Override
          public void accounting() {
                // TODO Auto-generated method stub
               System. out.println( "Do accounting for Credit Memo" );
         }
    
          @Override
          public void validate() {
                // TODO Auto-generated method stub
               System. out.println( "Validate Credit Memo" );
         }
    }
    
    package patterns.behavior.visitor;
    
    /**
     * Debit Memo implementation
     *
     * @author Charles Chen
     * @date 2014年10月19日
     */
    public class DebitMemo implements Transaction {
          @Override
          public void accept(Visitor visitor) {
                // TODO Auto-generated method stub
                visitor.visit( this);
         }
    
          @Override
          public void accounting() {
                // TODO Auto-generated method stub
               System. out.println( "Do accounting for Debit Memo");
         }
    
          @Override
          public void validate() {
                // TODO Auto-generated method stub
               System. out.println( "Validate Debit Memo");
         }
    
    }

  


做账操作访问者AccountingVisitor

    package patterns.behavior.visitor;
    
    /**
     * Accounting visitor
     * 
     * @author Charles Chen
     * @date 2014年10月19日
     */
    public class AccountingVisitor implements Visitor {
    
    	@Override
    	public void visit(Invoice invoice) {
    		// TODO Auto-generated method stub
    		invoice.accounting();
    	}
    
    	@Override
    	public void visit(CreditMemo creditMemo) {
    		// TODO Auto-generated method stub
    		creditMemo.accounting();
    	}
    
    	@Override
    	public void visit(DebitMemo debitMemo) {
    		// TODO Auto-generated method stub
    		debitMemo.accounting();
    	}
    
    }

  


校验操作访问者ValidateVisitor

    package patterns.behavior.visitor;
    
    /**
     * Validation visitor
     * 
     * @author Charles Chen
     * @date 2014年10月19日
     */
    public class ValidateVisitor implements Visitor {
    
    	@Override
    	public void visit(Invoice invoice) {
    		// TODO Auto-generated method stub
    		invoice.validate();
    	}
    
    	@Override
    	public void visit(CreditMemo creditMemo) {
    		// TODO Auto-generated method stub
    		creditMemo.validate();
    	}
    
    	@Override
    	public void visit(DebitMemo debitMemo) {
    		// TODO Auto-generated method stub
    		debitMemo.validate();
    	}
    
    }

  


客户端测试程序

    package patterns.behavior.visitor;
    
    /**
     * Client test for visitor design pattern sample
     * 
     * @author Charles Chen
     * @date 2014年10月19日
     */
    public class ClientTest {
    
    	public static void main(String[] args) {
    		// TODO Auto-generated method stub
    		Transaction invoice = new Invoice();
    		Transaction creditMemo = new CreditMemo();
    		Transaction debitMemo = new DebitMemo();
    
    		Visitor accVisitor = new AccountingVisitor();
    		Visitor valVisitor = new ValidateVisitor();
    		// Visit invoice
    		invoice.accept(accVisitor);
    		invoice.accept(valVisitor);
    		// Visit credit memo
    		creditMemo.accept(accVisitor);
    		creditMemo.accept(valVisitor);
    		// Visit debit memo
    		debitMemo.accept(accVisitor);
    		debitMemo.accept(valVisitor);
    	}
    
    }

  



[Image 1]: 