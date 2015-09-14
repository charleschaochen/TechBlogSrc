title: ReadWriteLock读写锁
date: 2014-08-13 19:07:33.0
tags:
- 并发编程
- 锁
- ReadWriteLock
categories:
- 并发编程

---

 我们知道，通过Java的synchronized关键字获得的锁是排他锁，当一个线程获得对象的排他锁，不管该线程执行的是读操作还是写操作，其他线程都只能等待直到对象锁被释放。然而，考虑这样一种情况，当有多个线程读取一个共享资源而只有一个线程对共享资源发生修改操作，对于读操作我们就没必要使用排他锁，我们可以使用共享锁，使得执行读操作的线程可以共享对象锁，并发地进行读取。只有写操作发生时，线...

<!-- more -->

 我们知道，通过Java的synchronized关键字获得的锁是排他锁，当一个线程获得对象的排他锁，不管该线程执行的是读操作还是写操作，其他线程都只能等待直到对象锁被释放。  
  
然而，考虑这样一种情况，当有多个线程读取一个共享资源而只有一个线程对共享资源发生修改操作，对于读操作我们就没必要使用排他锁，我们可以使用共享锁，使得执行读操作的线程可以共享对象锁，并发地进行读取。只有写操作发生时，线程才获取排他锁。  
  
JDK 1.5引入了ReadWriteLock接口，提供了创建读锁和写锁的接口来实现共享锁和排他锁。官方的API文档是这样描述的：  
  
A ReadWriteLock maintains a pair of associated locks,  
one for read-only operations and one for writing.  
The read lock may be held simultaneously by multiple reader threads,  
so long as there are no writers. The write lock is exclusive.  
  
ReadWriteLock维护了一对相关联的锁，一个是为只读操作服务，另一个是为写操作服务。读锁可以被多个执行读操作的线程持有，只要当前没有写操作的线程。而写锁是排他锁。  
  
当发生读操作时，我们可以利用ReadWriteLock对象获取一个读锁，用该读锁来加锁，那么多个线程可以共享该对象锁，并发地访问资源。当发生写操作时，我们可以利用ReadWriteLock对象获取一个写锁，用写锁来加锁，那么只有在当前线程释放锁后，其他线程才可以修改资源。  
  
ReadWriteLock的应用示例：  
  
这里定义了Dictionary类，封装了一个HashMap作为其数据，提供读取、修改和获取所有键的接口。其中get和keySet方法中使用了ReadWriteLock的读锁，而set方法使用了写锁。  
  
Dictionary类  


    package concurrent.lock;
    
    import java.util.HashMap;
    import java.util.Map;
    import java.util.Set;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.locks.Lock;
    import java.util.concurrent.locks.ReadWriteLock;
    import java.util.concurrent.locks.ReentrantReadWriteLock;
    
    /**
     * Shared data dictionary
     * 
     * @author Charles Chen
     * 
     */
    public class Dictionary {
    	// Storage data
    	private Map<String, String> data = new HashMap<String, String>();
    	private ReadWriteLock lock;
    	private Lock readLock;
    	private Lock writeLock;
    
    	/**
    	 * Constructor. Initialise locks
    	 */
    	public Dictionary() {
    		lock = new ReentrantReadWriteLock();
    		readLock = lock.readLock();
    		writeLock = lock.writeLock();
    	}
    
    	/**
    	 * Get value by key, using read lock
    	 * 
    	 * @param key
    	 * @return
    	 */
    	public String get(String key) {
    		this.readLock.lock();
    		try {
    			System.out.println("Getting value of key " + key + "...");
    			TimeUnit.MILLISECONDS.sleep(1000);
    			return this.data.get(key);
    		} catch (InterruptedException e) {
    			// TODO: handle exception
    			return null;
    		} finally {
    			this.readLock.unlock();
    		}
    	}
    
    	/**
    	 * Modify or add key-value, using write lock
    	 * 
    	 * @param key
    	 * @param value
    	 */
    	public void set(String key, String value) {
    		this.writeLock.lock();
    		try {
    			System.out.println("Modifing value of key " + key + "...");
    			TimeUnit.MILLISECONDS.sleep(1000);
    			this.data.put(key, value);
    		} catch (InterruptedException e) {
    			// TODO: handle exception
    		} finally {
    			this.writeLock.unlock();
    		}
    	}
    
    	/**
    	 * Get keys set, using read lock
    	 * 
    	 * @return
    	 */
    	public Set<String> keySet() {
    		this.readLock.lock();
    		try {
    			return this.data.keySet();
    		} finally {
    			this.readLock.unlock();
    		}
    	}
    }

  
  
读操作任务类  


    package concurrent.lock;
    
    /**
     * Reader task to read data from Dictionary
     * 
     * @author Charles Chen
     * 
     */
    public class ReaderTask implements Runnable {
    	private int taskId;
    	private Dictionary dict;
    
    	public ReaderTask(int taskId, Dictionary dict) {
    		this.taskId = taskId;
    		this.dict = dict;
    	}
    
    	@Override
    	public void run() {
    		// TODO Auto-generated method stub
    		for (String key : dict.keySet()) {
    			System.out.println("Reader Task#" + taskId + ": " + key + "["
    					+ dict.get(key) + "]");
    		}
    	}
    
    }

  
  


写操作任务类

    package concurrent.lock;
    
    import java.util.Random;
    
    public class WriterTask implements Runnable {
    	private int taskId;
    	private Dictionary dict;
    
    	public WriterTask(int taskId, Dictionary dict) {
    		this.taskId = taskId;
    		this.dict = dict;
    	}
    
    	@Override
    	public void run() {
    		// TODO Auto-generated method stub
    		for (String key : dict.keySet()) {
    			System.out.println("Writer Task#" + taskId + " is modifing key "
    					+ key + "...");
    			dict.set(key, "Modified by Writer Task#" + taskId);
    		}
    	}
    
    }

  
