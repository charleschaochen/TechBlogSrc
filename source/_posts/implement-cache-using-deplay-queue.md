title: 使用DelayQueue实现缓存
date: 2014-10-09 10:35:15.0
tags:
- DelayQueue
- 缓存
categories:
- 并发编程

---

实现一个缓存其实就是需要实现一个能够存储键值对，并自动清理超时缓存项的数据结构。java.util.concurrent包下有个DelayQueue，它是一个无界的BlockingQueue，存入DelayQueue的对象必须实现Delayed接口。DelayQueue提供一个阻塞的take方法，只有过期的Delayed对象才能被take出队列。利用DelayQueue的这个特性，我们可以用Del...

<!-- more -->

实现一个缓存其实就是需要实现一个能够存储键值对，并自动清理超时缓存项的数据结构。

java.util.concurrent包下有个DelayQueue，它是一个无界的BlockingQueue，存入DelayQueue的对象必须实现Delayed接口。DelayQueue提供一个阻塞的take方法，只有过期的Delayed对象才能被take出队列。

利用DelayQueue的这个特性，我们可以用DelayQueue实现一个简单的缓存。我们要做的就是将缓存的键值对封装在一个实现Delayed接口的类中，然后加入DelayQueue，并启动一条线程不断从DelayQueue中take东西，如果DelayQueue中没有过期对象，线程将被阻塞，一旦DelayQueue中有过期对象，将直接被移除。

首先，我们实现一个键值对数据结构Pair：

    package concurrent.delay.cache;
    
    /**
     * Key-value pair for cached item
     * 
     * @author Charles Chen
     * @date 2014年10月8日
     * @param <K>
     * @param <V>
     */
    public class Pair<K, V> {
    	private K key;
    	private V value;
    
    	public Pair(K key, V value) {
    		// TODO Auto-generated constructor stub
    		this.key = key;
    		this.value = value;
    	}
    
    	public K getKey() {
    		return key;
    	}
    
    	public void setKey(K key) {
    		this.key = key;
    	}
    
    	public V getValue() {
    		return value;
    	}
    
    	public void setValue(V value) {
    		this.value = value;
    	}
    
    }

  


接着，我们需要编写一个类实现Delayed接口，用来存放Pair实例。Delayed接口中有两个方法需要重写：

**compareTo()**： DelayQueue根据conpareTo方法的返回值决定Deplayed对象在队列中的顺序，一般是最先过期的元素在队头。

**getDelay()**： 返回Delayed对象剩余的存活时间。DelayQueue根据getDelay方法的返回值判断队头元素是否过期，当getDelay()返回值小于0时，表示这个对象已经过期，此时队列将唤醒调用了take()方法的线程，并移除队头元素。

实现代码：

    package concurrent.delay.cache;
    
    
    import java.util.concurrent.Delayed;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.atomic.AtomicLong;
    
    
    /**
     * Cached item
     * 
     * @author Charles Chen
     * @date 2014年10月9日
     * @param <T>
     */
    public class DelayItem<T> implements Delayed {
    	private T item; // Cached item
    	private long expire; // Expire time
    	private long sequence; // Sequence
    
    
    	// Sequencer for generating sequence
    	private static AtomicLong sequencer = new AtomicLong(0);
    
    
    	/**
    	 * Constructor
    	 * 
    	 * @param item
    	 * @param timeout
    	 */
    	public DelayItem(T item, long timeout) {
    		// TODO Auto-generated constructor stub
    		this.item = item;
    		this.expire = System.nanoTime() + timeout;
    		this.sequence = sequencer.getAndIncrement();
    	}
    
    
    	@Override
    	/**
    	 * Compare current object with the specified object.
    	 * Delay queue uses this method to determine the orders of items
    	 */
    	public int compareTo(Delayed o) {
    		// TODO Auto-generated method stub
    		if (this == o)
    			return 0;
    		if (o instanceof DelayItem) {
    			DelayItem other = (DelayItem) o;
    			long dist = this.expire - other.expire;
    			if (dist > 0)
    				return 1;
    			if (dist < 0)
    				return -1;
    			// If the expire time is same, compare the sequence
    			return this.sequence < other.sequence ? -1 : 1;
    		}
    		// If the specified object is not a instance of DelayItem, compare
    		// the return value of getDelay()
    		long dist = this.getDelay(TimeUnit.NANOSECONDS)
    				- o.getDelay(TimeUnit.NANOSECONDS);
    		return dist > 0 ? 1 : (dist < 0 ? -1 : 0);
    	}
    
    
    	@Override
    	/**
    	 * Get the distance between expire time and current time.
    	 * Delay queue uses this method to check if the item is expired
    	 */
    	public long getDelay(TimeUnit unit) {
    		// TODO Auto-generated method stub
    		return unit.convert(this.expire - System.nanoTime(),
    				TimeUnit.NANOSECONDS);
    	}
    
    
    	/**
    	 * Getter for the item
    	 * 
    	 * @return
    	 */
    	public T getItem() {
    		return item;
    	}
    }

  


最后，我们要实现缓存的数据结构，在这个类中，维护一个DelayQueue存放DelayItem，并维护一个HashMap存储缓存的数据用于存取。

实现代码：

    package concurrent.delay.cache;
    
    import java.util.concurrent.ConcurrentHashMap;
    import java.util.concurrent.DelayQueue;
    import java.util.concurrent.TimeUnit;
    
    /**
     * Cache data structure
     * 
     * @author Charles Chen
     * @date 2014年10月9日
     * @param <K>
     * @param <V>
     */
    public class Cache<K, V> {
    	private DelayQueue<DelayItem<Pair<K, V>>> queue = new DelayQueue<DelayItem<Pair<K, V>>>();
    	private ConcurrentHashMap<K, DelayItem<Pair<K, V>>> data = new ConcurrentHashMap<K, DelayItem<Pair<K, V>>>();
    
    	/**
    	 * Constructor
    	 */
    	public Cache() {
    		// TODO Auto-generated constructor stub
    		// Start a timeout checker thread to remove expired items
    		Thread t = new Thread(new Runnable() {
    
    			@Override
    			public void run() {
    				// TODO Auto-generated method stub
    				while (!Thread.interrupted()) {
    					try {
    						timeoutCheck();
    					} catch (InterruptedException e) {
    						// TODO Auto-generated catch block
    						e.printStackTrace();
    					}
    				}
    			}
    		}, "TimeoutChecker");
    		t.setDaemon(true);
    		t.start();
    	}
    
    	/**
    	 * Remove one expired item
    	 * 
    	 * @throws InterruptedException
    	 */
    	private void timeoutCheck() throws InterruptedException {
    		DelayItem<Pair<K, V>> item = queue.take();
    		if (item != null) {
    			data.remove(item.getItem().getKey());
    		}
    	}
    
    	/**
    	 * Put key-value pair into cache
    	 * 
    	 * @param key
    	 * @param value
    	 */
    	public void put(K key, V value, long timeout, TimeUnit unit) {
    		// If key already exists, remove from queue
    		if (data.containsKey(key)) {
    			queue.remove(data.get(key));
    		}
    		Pair<K, V> pair = new Pair<K, V>(key, value);
    		DelayItem<Pair<K, V>> item = new DelayItem<Pair<K, V>>(pair,
    				TimeUnit.NANOSECONDS.convert(timeout, unit));
    		data.put(key, item);
    		queue.put(item);
    	}
    
    	/**
    	 * Remove item by key
    	 * 
    	 * @param key
    	 */
    	public void remove(K key) {
    		queue.remove(data.remove(key));
    	}
    
    	/**
    	 * Get item by key
    	 * 
    	 * @param key
    	 * @return
    	 */
    	public V get(K key) {
    		if (data.get(key) != null) {
    			return data.get(key).getItem().getValue();
    		}
    		return null;
    	}
    }

  


客户端测试程序：

    package concurrent.delay.cache;
    
    import java.util.concurrent.TimeUnit;
    
    public class ClientTest {
    
    	public static void main(String[] args) {
    		// TODO Auto-generated method stub
    		Cache<String, String> cache = new Cache<String, String>();
    		cache.put("No1", "Value of No1", 1000, TimeUnit.MILLISECONDS);
    		System.out.println(cache.get("No1"));
    		// Wait 1001 milliseconds
    		try {
    			TimeUnit.MILLISECONDS.sleep(1001);
    		} catch (InterruptedException e) {
    			// TODO Auto-generated catch block
    			e.printStackTrace();
    		}
    		// Get the value, it will be null
    		System.out.println(cache.get("No1"));
    	}
    
    }

  
