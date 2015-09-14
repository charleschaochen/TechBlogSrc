title: Java实现读写锁
date: 2014-10-03 22:36:57.0
tags:
- 读写锁
categories:
- 并发编程

---

实现一个简单的读写锁我们知道，使用读写锁的目的是使读操作共享一个锁，写操作独占一个锁，从而使得多个读操作可以同时进行，而进行写操作时才实现互斥，当读操作比较频繁时，可以减少加锁释放锁带来的消耗。我之前的博文有介绍到ReentrantReadWriteLock的使用，其实我们完全可以自己实现一个读写锁。首先我们必须理解读写锁的一些规则：（1） 加读锁之前，必须检查当前是否已经有写操作，或者有写操作的...

<!-- more -->

#### **实现一个简单的读写锁** ####

我们知道，使用读写锁的目的是使读操作共享一个锁，写操作独占一个锁，从而使得多个读操作可以同时进行，而进行写操作时才实现互斥，当读操作比较频繁时，可以减少加锁释放锁带来的消耗。

我之前的博文有介绍到[ReentrantReadWriteLock][]的使用，其实我们完全可以自己实现一个读写锁。首先我们必须理解读写锁的一些规则：

（1） 加读锁之前，必须检查当前是否已经有写操作，或者有写操作的线程在请求获得锁，若有，便阻塞等待。

（2） 释放读锁时，需要唤醒所有等待的线程

（3） 加写锁之前，必须检查当前是否读操作或其他写操作在进行，若有，便阻塞等待。

（4） 释放写锁时，需要唤醒所有等待的线程

知道这些规则后，我们便可以开始设计一个简单的读写锁。我们可以维护几个变量来表示当前是否有读操作或写操作。例如，使用readers统计写操作线程数，writers统计读操作线程数，waitingWriters统计正在请求写操作的线程数。当加锁或释放锁时，对这些变量进行判断并修改这些变量。

以下是一个简单的读写锁的代码：

    package concurrent.readwritelock;
    
    import java.util.concurrent.TimeUnit;
    
    public class SimpleReadWriteLock {
    	private int readers;
    	private int writers;
    	private int waitingWriters;
    
    	/**
    	 * Get a read lock
    	 * 
    	 * @throws InterruptedException
    	 */
    	public synchronized void lockRead() throws InterruptedException {
    		while (writers > 0 || waitingWriters > 0) {
    			this.wait();
    		}
    		readers++;
    	}
    
    	/**
    	 * Release a read lock
    	 */
    	public synchronized void unLockRead() {
    		readers--;
    		notifyAll();
    	}
    
    	/**
    	 * Get a write lock
    	 * 
    	 * @throws InterruptedException
    	 */
    	public synchronized void lockWrite() throws InterruptedException {
    		waitingWriters++;
    		while (readers > 0 || writers > 0) {
    			this.wait();
    		}
    		waitingWriters--;
    		writers++;
    	}
    
    	/**
    	 * Release a write lock
    	 */
    	public synchronized void unLockWrite() {
    		writers--;
    		notifyAll();
    	}
    }

  


#### **支持重进入的读写锁** ####

上面实现的简单的读写锁，在一些情况下容易导致死锁。例如：

（1）读的重进入。若一个线程获得了读锁，在释放该锁之前再一次申请读锁，而此时已经有其他线程正在申请写锁，此时第一个线程由于有写请求而阻塞，而第二个线程由于第一个线程还未释放读锁也进入阻塞状态，形成死锁。正常的情况应该是，如果一个线程已经获取了读锁，再次申请读锁时可以直接获取读锁，而不会因为有其他写请求而阻塞。

（2）写的重进入。若一个线程获得了写锁，在释放该锁之前再一次申请写锁，此时会因为第一个写锁还未释放而进入阻塞状态。正常的情况应该是，如果一个线程已经获取了写锁，再次请求写锁时应该可以直接获取写锁。

（3）从读到写的重进入。若一个线程获得了读锁，在释放该锁之前又申请了写锁，此时会因为第一个读锁还未释放而进入阻塞状态。正常的情况应该是，如果一个线程获取了读锁，当再次申请写锁时，如果当前除了自身外没有其他获取了读锁的线程，那么应当直接获得写锁。

（4）从写到读的重进入。若一个线程获得了写锁，在释放该锁之前又申请了读锁，此时会因为第一个写锁还未释放而进入阻塞状态。正常的情况应该是，如果一个线程获得了写锁，再次申请读锁时可以直接获得锁。

针对以上四种重进入的情况，我们应该对原先的读写锁进行一些优化：

（1）针对读的重进入，我们可以在读写锁中维护一个map，key为线程对象，value为该线程获取的读锁数目。当申请获取读锁时，首先判断当前线程是否存在于map中且读锁数目是否大于0，若是，表示该线程已经获得过了读锁，应该允许其再次获得读锁。获取读锁后，将map中当前线程的读锁数目加1

（2）针对写的重进入，我们可以在读写锁中维护一个线程对象writingThread保存当前获取写锁的线程，同时用一个整型变量writeAccesses记录当前获取写锁的线程的写锁数目。申请获取写锁时，首先判断当前线程是否等于writingThread，若是表示当前线程已经获得了写锁，允许其再次获得写锁。获取写锁后，将writeAccesses加1，并把writingThread指向当前线程对象

（3）针对读到写的重进入，首先需要判断当前线程是否是唯一一个获得读锁的线程，这可以通过检查map来判断，若是，则允许该线程获取写锁。

（4）针对写到读的重进入，首先需要判断当前线程是否已经获得了写锁，这可以通过writingThread是否等于当前线程对象来判断，若是，则允许该线程获得读锁。

根据上面提到的问题和解决方案，改进后的读写锁代码如下：

    package concurrent.readwritelock;
    
    import java.util.HashMap;
    import java.util.Map;
    
    /**
     * Read write lock
     * 
     * @author Charles Chen
     * @date 2014年10月2日
     */
    public class ReadWriteLock {
    	private Map<Thread, Integer> readingThreads;
    	private Thread writingThread = null;
    	private int writingAccesses = 0;
    	private int waitingWriters = 0;
    
    	public ReadWriteLock() {
    		// TODO Auto-generated constructor stub
    		this.readingThreads = new HashMap<Thread, Integer>();
    	}
    
    	/**
    	 * Get a read lock
    	 * 
    	 * @throws InterruptedException
    	 */
    	public synchronized void lockRead() throws InterruptedException {
    		Thread thread = Thread.currentThread();
    		while (!this.canGrantReadAccess(thread)) {
    			this.wait();
    		}
    		// Put current thread into list and count the read accesses
    		this.readingThreads.put(thread, this.getReadAccessors(thread) + 1);
    	}
    
    	/**
    	 * Release read lock
    	 */
    	public synchronized void unLockRead() {
    		Thread thread = Thread.currentThread();
    		// If current thread has not a read lock, throw exception
    		if (!this.isReader(thread)) {
    			throw new IllegalMonitorStateException("Calling Thread does not"
    					+ " hold a read lock on this ReadWriteLock");
    		}
    		int accessCount = this.getReadAccessors(thread) - 1;
    		if (accessCount <= 0) {
    			this.readingThreads.remove(thread);
    		} else {
    			this.readingThreads.put(thread, accessCount);
    		}
    		this.notifyAll();
    	}
    
    	/**
    	 * Get a write lock
    	 * 
    	 * @throws InterruptedException
    	 */
    	public synchronized void lockWrite() throws InterruptedException {
    		this.waitingWriters++;
    		Thread thread = Thread.currentThread();
    		while (!this.canGrantWriteAccess(thread)) {
    			this.wait();
    		}
    		this.waitingWriters--;
    		this.writingThread = thread;
    		this.writingAccesses++;
    	}
    
    	/**
    	 * Release write lock
    	 */
    	public synchronized void unLockWrite() {
    		Thread thread = Thread.currentThread();
    		// If current thread has not a write lock, throw exception
    		if (!this.isWriter(thread)) {
    			throw new IllegalMonitorStateException("Calling Thread does not"
    					+ " hold the write lock on this ReadWriteLock");
    		}
    		this.writingAccesses--;
    		if (this.writingAccesses <= 0) {
    			this.writingThread = null;
    		}
    		this.notifyAll();
    	}
    
    	/**
    	 * Check if the thread can be granted read access
    	 * 
    	 * @param thread
    	 * @return
    	 */
    	private boolean canGrantReadAccess(Thread thread) {
    		// If the thread is already a reader or a writer, return true
    		if (this.isReader(thread) || this.isWriter(thread))
    			return true;
    		// If there are writers or waiting writers, return falses
    		if (this.writingThread != null || this.waitingWriters > 0)
    			return false;
    		return true;
    	}
    
    	/**
    	 * Check if the thread can be granted write access
    	 * 
    	 * @param thread
    	 * @return
    	 */
    	private boolean canGrantWriteAccess(Thread thread) {
    		// If the thread is the only reader or already a writer, return true
    		if (this.isOnlyReader(thread) || this.isWriter(thread))
    			return true;
    		// If there are readers or writers, return false
    		if (this.readingThreads.size() > 0 || this.writingThread != null)
    			return false;
    		return true;
    	}
    
    	/**
    	 * Get the count of read accesses of the thread
    	 * 
    	 * @param thread
    	 * @return
    	 */
    	private int getReadAccessors(Thread thread) {
    		Integer accessCount = this.readingThreads.get(thread);
    		if (accessCount == null) {
    			return 0;
    		}
    		return accessCount.intValue();
    	}
    
    	/**
    	 * Check if the thread has got read access
    	 * 
    	 * @param thread
    	 * @return
    	 */
    	private boolean isReader(Thread thread) {
    		if (this.readingThreads.get(thread) != null
    				&& this.readingThreads.get(thread).intValue() > 0)
    			return true;
    		return false;
    	}
    
    	/**
    	 * Check if the thread is the only one that has got read access
    	 * 
    	 * @param thread
    	 * @return
    	 */
    	private boolean isOnlyReader(Thread thread) {
    		if (this.readingThreads.size() == 1
    				&& this.readingThreads.get(thread) != null)
    			return true;
    		return false;
    	}
    
    	/**
    	 * Check if the thread has got write access
    	 * 
    	 * @param thread
    	 * @return
    	 */
    	private boolean isWriter(Thread thread) {
    		return this.writingThread != null && this.writingThread == thread ? true
    				: false;
    	}
    }

  


对以上编写的读写锁的简单测试程序，有时间的话可以设计更复杂的用例去测试

    package concurrent.readwritelock;
    
    import java.util.concurrent.TimeUnit;
    
    /**
     * Client test for read write lock
     * 
     * @author Charles Chen
     * @date 2014年10月3日
     */
    public class ClientTest {
    
    	public static void main(String[] args) {
    		// TODO Auto-generated method stub
    		ReadWriteLock lock = new ReadWriteLock();
    		Resource res = new Resource(lock);
    
    		// testReaderWriter(res);
    		// testMultiReaders(res);
    		testMultiWriters(res);
    	}
    
    	/**
    	 * Test case of a read thread and write thread
    	 * 
    	 * @param res
    	 */
    	public static void testReaderWriter(Resource res) {
    		new Thread(new ReadTask(res), "reader").start();
    		new Thread(new WriteTask(res), "writer").start();
    	}
    
    	/**
    	 * Test case of three read threads
    	 * 
    	 * @param res
    	 */
    	public static void testMultiReaders(Resource res) {
    		new Thread(new ReadTask(res), "reader-01").start();
    		new Thread(new ReadTask(res), "reader-02").start();
    		new Thread(new ReadTask(res), "reader-03").start();
    	}
    
    	/**
    	 * Test case of three write threads
    	 * 
    	 * @param res
    	 */
    	public static void testMultiWriters(Resource res) {
    		new Thread(new WriteTask(res), "writer-01").start();
    		new Thread(new WriteTask(res), "writer-02").start();
    		new Thread(new WriteTask(res), "writer-03").start();
    	}
    }
    
    /**
     * Resource POJO
     * 
     * @author Charles Chen
     * @date 2014年10月2日
     */
    class Resource {
    	private ReadWriteLock lock;
    
    	public Resource(ReadWriteLock lock) {
    		// TODO Auto-generated constructor stub
    		this.lock = lock;
    	}
    
    	public void read() {
    		try {
    			lock.lockRead();
    			for (int i = 0; i < 5; i++) {
    				TimeUnit.MILLISECONDS.sleep(1000);
    				System.out.println("Read " + i);
    			}
    		} catch (Exception e) {
    		} finally {
    			lock.unLockRead();
    		}
    	}
    
    	public void write() {
    		try {
    			lock.lockWrite();
    			for (int i = 0; i < 5; i++) {
    				TimeUnit.MILLISECONDS.sleep(1000);
    				System.out.println("Write " + i);
    			}
    		} catch (Exception e) {
    		} finally {
    			try {
    				lock.unLockWrite();
    			} catch (Exception e) {
    				// TODO Auto-generated catch block
    				e.printStackTrace();
    			}
    		}
    	}
    }
    
    /**
     * Reader
     * 
     * @author Charles Chen
     * @date 2014年10月2日
     */
    class ReadTask implements Runnable {
    	private Resource res;
    
    	public ReadTask(Resource res) {
    		// TODO Auto-generated constructor stub
    		this.res = res;
    	}
    
    	@Override
    	public void run() {
    		// TODO Auto-generated method stub
    		res.read();
    	}
    }
    
    /**
     * Writer
     * 
     * @author Charles Chen
     * @date 2014年10月2日
     */
    class WriteTask implements Runnable {
    	private Resource res;
    
    	public WriteTask(Resource res) {
    		// TODO Auto-generated constructor stub
    		this.res = res;
    	}
    
    	@Override
    	public void run() {
    		// TODO Auto-generated method stub
    		res.write();
    	}
    }

  



[ReentrantReadWriteLock]: http://www.charlestech.org/view_article.shtml?uid=ZSXCRDQEWA26