title: 行为型设计模式 — 迭代器模式
date: 2014-08-23 12:37:36.0
tags:
- 设计模式
- 迭代器模式
categories:
- 设计模式

---

迭代器模式比较容易理解，即创建一个迭代器去顺序访问聚集对象的元素。使用迭代器模式可以实现对具体对象元素的访问，而暴露聚集对象的内部结构。迭代器模式常用于集合元素的访问中，例如Java Collection API中，调用Collection对象的iterator()方法可以返回当前集合的迭代器，从而实现顺序访问。迭代器模式的类图如下：示例程序：Iterator接口package patterns....

<!-- more -->

迭代器模式比较容易理解，即创建一个迭代器去顺序访问聚集对象的元素。使用迭代器模式可以实现对具体对象元素的访问，而暴露聚集对象的内部结构。

迭代器模式常用于集合元素的访问中，例如Java Collection API中，调用Collection对象的iterator()方法可以返回当前集合的迭代器，从而实现顺序访问。

迭代器模式的类图如下：

  
![Image 1][]  


  


示例程序：

Iterator接口

    package patterns.behavior.iterator;
    
    /**
     * Iterator Interface
     *
     * @author Charles Chen
     * @date 2014年8月23日
     */
    public interface Iterator {
         Object first();
    
         boolean hasNext();
    
         boolean hasPrevious();
    
         Object next();
    
         Object previous();
    
         void reset();
    }

  


Aggregate聚集对象接口

    /**
     * Aggregate Interface
     *
     * @author Charles Chen
     * @date 2014年8月23日
     * @param <T>
     */
    public interface Aggregate<T> {
         void add(T elem);
    
         void remove(T elem);
    
         T get(int index);
    
         Iterator iterator();
    
         int size();
    }

  


具体迭代器实现类

    package patterns.behavior.iterator;
    
    /**
     * Implementation of Iterator
     *
     * @author Charles Chen
     * @date 2014年8月23日
     */
    public class ConcreteIterator implements Iterator {
         private Aggregate aggregate ;
         private int pos = -1;
    
         public ConcreteIterator( Aggregate aggregate) {
                // TODO Auto-generated constructor stub
                this. aggregate = aggregate;
         }
    
         @Override
         /**
          * Get first element
          */
         public Object first() {
                // TODO Auto-generated method stub
                return aggregate.get(0);
         }
    
         @Override
         /**
          * If has next element, return true
          */
         public boolean hasNext() {
                // TODO Auto-generated method stub
                return pos < aggregate.size() - 1 ? true : false;
         }
    
         @Override
         /**
          * If has previous element, return true
          */
         public boolean hasPrevious() {
                // TODO Auto-generated method stub
                return pos > 0 ? true : false;
         }
    
         @Override
         /**
          * Get next element
          */
         public Object next() {
                // TODO Auto-generated method stub
                return hasNext() ? aggregate.get(++ pos) : null;
         }
    
         @Override
         /**
          * Get previous element
          */
         public Object previous() {
                // TODO Auto-generated method stub
                return hasPrevious() ? aggregate.get(-- pos) : null;
         }
    
         @Override
         /**
          * Reset the position to 0
          */
         public void reset() {
                // TODO Auto-generated method stub
                pos = -1;
         }
    
    }

  


具体聚集实现类

    package patterns.behavior.iterator;
    
    import java.util.ArrayList;
    import java.util.List;
    
    /**
     * Implementation of Aggregate
     *
     * @author Charles Chen
     * @date 2014年8月23日
     * @param <T>
     */
    public class ConcreteAggregate<T> implements Aggregate<T> {
         private List<T> data;
    
         public ConcreteAggregate() {
                // TODO Auto-generated constructor stub
                data = new ArrayList<T>();
         }
    
         @Override
         /**
          * Add element to list
          */
         public void add(T elem) {
                // TODO Auto-generated method stub
                data.add( elem);
         }
    
         @Override
         /**
          * Remove element from list
          */
         public void remove(T elem) {
                // TODO Auto-generated method stub
                data.remove( elem);
         }
    
         @Override
         /**
          * Get element by index
          */
         public T get( int index) {
                // TODO Auto-generated method stub
                return data.get( index);
         }
    
         @Override
         /**
          * Get iterator of current collection
          */
         public Iterator iterator() {
                // TODO Auto-generated method stub
                return new ConcreteIterator( this);
         }
    
         @Override
         /**
          * Get the size of current collection
          */
         public int size() {
                // TODO Auto-generated method stub
                return data.size();
         }
    
    }

  


客户端测试程序

    package patterns.behavior.iterator;
    
    /**
     * Client test sample for iterator design pattern
     *
     * @author Charles Chen
     * @date 2014年8月23日
     */
    public class ClientTest {
    
         public static void main(String[] args) {
                // TODO Auto-generated method stub
               ConcreteAggregate<String> aggregate = new ConcreteAggregate<String>();
                aggregate.add( "hello");
                aggregate.add( "iterator");
                aggregate.add( "design");
                aggregate.add( "pattern");
               Iterator it = aggregate.iterator();
    
                // Traverse the elements
                while ( it.hasNext()) {
                    System. out.println((String) it.next());
               }
                while ( it.hasPrevious()) {
                    System. out.println((String) it.previous());
               }
         }
    
    }

  



[Image 1]: 