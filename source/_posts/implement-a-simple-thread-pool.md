title: Java实现一个简单的线程池
date: 2014-10-12 17:00:41.0
tags:
- 线程池
categories:
- 并发编程

---

我们知道java.util.concurrent包下有个Executors类，这个类提供了一系列静态方法来创建各种线程池，例如newCachedThreadPool(), newFixedThreadPool()等等。参照ThreadPoolExecutor类的功能，我们知道，一个线程池所需要实现的功能和接口大致是：（1）维护一个任务队列，客户端可以提交Runnable任务到这个队列中（2）初始化...

<!-- more -->

我们知道java.util.concurrent包下有个Executors类，这个类提供了一系列静态方法来创建各种线程池，例如newCachedThreadPool(), newFixedThreadPool()等等。参照ThreadPoolExecutor类的功能，我们知道，一个线程池所需要实现的功能和接口大致是：

（1）维护一个任务队列，客户端可以提交Runnable任务到这个队列中

（2）初始化时启动若干个线程，每个线程运行时都不断从任务队列中取任务执行

（3）对外提供提交Runnable任务的接口，如execute()，submit()

（4）对外提供关闭线程池的接口，用来关闭当前所有的线程

  


根据线程池的这些特性，我们可以实现一个简单的线程池，代码如下：

    package concurrent.threadpool;
    
    import java.util.ArrayList;
    import java.util.List;
    import java.util.concurrent.ArrayBlockingQueue;
    import java.util.concurrent.BlockingQueue;
    
    /**
     * Thread pool
     * 
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class ThreadPool {
    	private List<WorkingThread> workingThreads;
    	private BlockingQueue<Runnable> taskQueue;
    	private boolean isShutdown = false;
    
    	private final int DEFAULT_THREAD_COUNT = 10;
    	private final int DEFAULT_TASK_COUNT = 10;
    
    	/**
    	 * Default constructor, initialized threads and task queue
    	 */
    	public ThreadPool() {
    		// TODO Auto-generated constructor stub
    		workingThreads = new ArrayList<WorkingThread>(DEFAULT_THREAD_COUNT);
    		taskQueue = new ArrayBlockingQueue<Runnable>(DEFAULT_TASK_COUNT);
    		for (int i = 0; i < DEFAULT_THREAD_COUNT; i++) {
    			WorkingThread workingThread = new WorkingThread(taskQueue);
    			workingThreads.add(workingThread);
    			workingThread.start();
    		}
    	}
    
    	/**
    	 * Constructor, initialized threads and task queue
    	 * 
    	 * @param threadCount
    	 * @param maxTaskCount
    	 */
    	public ThreadPool(int threadCount, int maxTaskCount) {
    		// TODO Auto-generated constructor stub
    		workingThreads = new ArrayList<WorkingThread>(threadCount);
    		taskQueue = new ArrayBlockingQueue<Runnable>(maxTaskCount);
    		for (int i = 0; i < DEFAULT_THREAD_COUNT; i++) {
    			WorkingThread workingThread = new WorkingThread(taskQueue);
    			workingThreads.add(workingThread);
    			workingThread.start();
    		}
    	}
    
    	/**
    	 * Submit a task
    	 * 
    	 * @param task
    	 */
    	public void execute(Runnable task) {
    		if (this.isShutdown) {
    			throw new IllegalStateException("Thread pool is shutdown");
    		}
    		try {
    			taskQueue.put(task);
    		} catch (InterruptedException e) {
    			// TODO Auto-generated catch block
    			e.printStackTrace();
    		}
    	}
    
    	/**
    	 * Shutdown the thread pool
    	 */
    	public void shutdown() {
    		if (!this.isShutdown) {
    			for (WorkingThread thread : workingThreads) {
    				thread.stopWorkingThread();
    			}
    			this.isShutdown = true;
    		}
    	}
    
    	/**
    	 * Working thread
    	 * 
    	 * @author Charles Chen
    	 * @date 2014年10月12日
    	 */
    	private class WorkingThread extends Thread {
    		private BlockingQueue<Runnable> taskQueue;
    		private boolean isStop = false;
    
    		public WorkingThread(BlockingQueue<Runnable> taskQueue) {
    			// TODO Auto-generated constructor stub
    			this.taskQueue = taskQueue;
    		}
    
    		@Override
    		public void run() {
    			// TODO Auto-generated method stub
    
    			try {
    				while (!this.isStop) {
    					Runnable task = taskQueue.take();
    					if (task != null) {
    						task.run();
    					}
    				}
    			} catch (InterruptedException e) {
    				// TODO Auto-generated catch block
    				e.printStackTrace();
    			}
    
    		}
    
    		/**
    		 * Stop the working thread
    		 */
    		public void stopWorkingThread() {
    			this.isStop = true;
    		}
    	}
    }

  


客户端测试程序

    package concurrent.threadpool;
    
    import java.util.concurrent.TimeUnit;
    
    /**
     * Client test for simple thread pool
     * 
     * @author Charles Chen
     * @date 2014年10月12日
     */
    public class ClientTest {
    	public static void main(String[] args) {
    		ThreadPool pool = new ThreadPool(10, 5);
    		pool.execute(new PrintTask());
    		pool.execute(new PrintTask());
    		pool.shutdown();
    	}
    }
    
    class PrintTask implements Runnable {
    	@Override
    	public void run() {
    		// TODO Auto-generated method stub
    		for (int i = 0; i < 5; i++) {
    			System.out.println("Printing " + i);
    			try {
    				TimeUnit.MILLISECONDS.sleep(1000);
    			} catch (InterruptedException e) {
    				// TODO Auto-generated catch block
    				e.printStackTrace();
    			}
    		}
    	}
    
    }

  
