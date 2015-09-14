title: 并发编程之任务协作
date: 2014-05-11 19:07:29.0
tags:
- 并发编程
- 线程协作
categories:
- 并发编程

---

前言  当多个线程共享资源时，我们通常利用加锁的方式来避免一个线程对资源的操作影响另一线程对该资源的操作，例如使用synchronized关键字或Lock对象。而如果多个线程之间的关系不是互斥，而是协作完成某种任务，例如煮面，需要先把水烧开，再加面，然后加调料，又例如典型的生产者消费者问题。对于这种协作关系的多个线程，关键在于线程之间的握手或者说是通信，确保一个任务执行完之后能将信号传递...

<!-- more -->

**前言**

  当多个线程共享资源时，我们通常利用加锁的方式来避免一个线程对资源的操作影响另一线程对该资源的操作，例如使用synchronized关键字或Lock对象。而如果多个线程之间的关系不是互斥，而是协作完成某种任务，例如煮面，需要先把水烧开，再加面，然后加调料，又例如典型的生产者消费者问题。对于这种协作关系的多个线程，关键在于线程之间的握手或者说是通信，确保一个任务执行完之后能将信号传递给另一个任务。Java提供一些同步机制来解决线程之间的协作

**wait()与notify()**

  wait(), notify()以及notifyAll()是典型的解决线程间协作的方法。这三个方法并不是Thread对象的方法，而是所有Object类的方法。 线程可以通过调用某个对象的wait方法，将自己阻塞等待在该对象上，并释放对象锁。也可以调用对象的notify或notifyAll方法将阻塞在该对象上的线程唤醒，从而实现线程与线程之间的通信。

  值得注意的是，这三个方法都必须放在synchronized块中执行，因为在调用这三个方法之前，必须确保线程已经获得对象锁。

  下面来看一下如何用这种方式来解决生产者消费者问题：

定义生产的产品类：

    public class Product {
    	private int id;
    
    	public Product(int id) {
    		this.id = id;
    	}
    
    	public int getId() {
    		return id;
    	}
    
    	public void setId(int id) {
    		this.id = id;
    	}
    
    }

定义工厂类，用于存放产品，并作为生产者线程和消费者线程的共享对象： 

    public class Factory {
    	public Product product = null;
    }

定义生产者线程类：

    public class Producer implements Runnable {
    	private int count = 0;
    	private Factory factory;
    
    	public Producer(Factory factory) {
    		this.factory = factory;
    	}
    
    	@Override
    	public void run() {
    		// TODO Auto-generated method stub
    		try {
    			while (!Thread.interrupted()) {
    				if (factory.product != null) {
    					synchronized (factory) {
    						factory.wait();
    					}
    				}
    				System.out.println("Producing product " + count);
    				factory.product = new Product(count++);
    				synchronized (factory) {
    					factory.notify();
    				}
    			}
    		} catch (InterruptedException e) {
    			e.printStackTrace();
    		}
    	}
    
    }

 

消费者线程类：

    public class Consumer implements Runnable {
    	private Factory factory;
    
    	public Consumer(Factory factory) {
    		this.factory = factory;
    	}
    	@Override
    	public void run() {
    		// TODO Auto-generated method stub
    		try {
    			while (!Thread.interrupted()) {
    				if (factory.product == null) {
    					synchronized (factory) {
    						factory.wait();
    					}
    				}
    
    				System.out.println("Consuming product "
    						+ factory.product.getId());
    				factory.product = null;
    				synchronized (factory) {
    					factory.notify();
    				}
    			}
    		} catch (InterruptedException e) {
    			// TODO: handle exception
    			e.printStackTrace();
    		}
    	}
    
    }

测试程序：

    Factory factory = new Factory();
    exec.execute(new Producer1(factory));
    exec.execute(new Consumer1(factory));
    TimeUnit.MICROSECONDS.sleep(5000);
    exec.shutdownNow();

 

**BlockingQueue同步队列**

  使用java.util.concurrent.BlockingQueue是解决多线程协作的一种更高效安全的方案，它提供了一些同步的线程安全的存取元素的接口。我们可以使用BlockingQueue来保存多个线程之间的共享资源，并利用BlockingQueue提供的存取元素的接口来访问资源，同步机制与线程间的通信由BlockingQueue保证和实现，从而简化对多线程协作的控制。

  还是拿生产者消费者的问题来说，我们可以将产品存放在一个BlockingQueue中。对于生产者端的线程，当它们向队列添加产品时，若队列已满，生产者线程将阻塞，直到队列中有空闲位置时被唤醒。而对于消费者端的线程，当它们从队列中获取产品时，若队列中没有可用资源，消费者线程将被阻塞，直到队列中有可用的资源时被唤醒。

  BlockingQueue中提供同步机制的插入元素的方法为put(Object object)，获取元素的方法为take()。

  现在我们使用BlockingQueue重写生产者消费者问题。

定义生产者线程类：

    public class Producer implements Runnable {
    	private int count = 0;
    	private BlockingQueue<Product> queue;
    
    	public Producer(BlockingQueue<Product> queue) {
    		this.queue = queue;
    	}
    
    	@Override
    	public void run() {
    		// TODO Auto-generated method stub
    		while (!Thread.interrupted()) {
    			System.out.println("Produced product " + count);
    			Product product = new Product(count++);
    			try {
    				queue.put(product);
    			} catch (InterruptedException e) {
    				// TODO Auto-generated catch block
    				e.printStackTrace();
    			}
    		}
    	}
    }

 

定义消费者线程类：

    public class Consumer implements Runnable {
    	private BlockingQueue<Product> queue;
    
    	public Consumer(BlockingQueue<Product> queue) {
    		this.queue = queue;
    	}
    
    	@Override
    	public void run() {
    		// TODO Auto-generated method stub
    		while (!Thread.interrupted()) {
    			try {
    				Product product = queue.take();
    				System.out.println("Consuming product " + product.getId());
    			} catch (InterruptedException e) {
    				// TODO Auto-generated catch block
    				e.printStackTrace();
    			}
    		}
    	}
    
    }

测试程序：

    ExecutorService exec = Executors.newCachedThreadPool();
    BlockingQueue<Product> queue = new LinkedBlockingQueue<Product>();
    exec.execute(new Producer(queue));
    exec.execute(new Consumer(queue));
    TimeUnit.MICROSECONDS.sleep(5000);
    exec.shutdownNow();

  常用的BlockingQueue实现类有LinkedBlocingQueue和ArrayBlockingQueue。LinkedBlocingQueue底层用链表方式组织元素，是一种无界的队列，ArrayBlockingQueue底层用顺序表方式组织元素，初始化时接受一个整型参数限制队列大小。

  使用BlockingQueue来实现线程之间的协作大大简化了程序，它自动管理了线程间的同步以及等待唤醒操作，从而使得程序员可以忽略在哪些地方阻塞或唤醒线程等细节，而关注其他更高级的功能。