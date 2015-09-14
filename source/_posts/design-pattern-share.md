title: 结构型设计模式 — 享元模式
date: 2014-06-29 15:48:11.0
tags:
- 设计模式
- 享元模式
categories:
- 设计模式

---

什么是享元模式？享元模式（Flyweight）就是维护一个对象实例的共享池，实现对象实例的共享和回收利用。当系统中需要频繁创建和使用一个类的实例，而该类创建实例时需要较大的开销，使用享元模式去维护一个该类实例的共享池，实现实例的共享，可以很大程度地减少创建实例带来的开销。享元模式下有两个角色，共享池角色和实例角色。共享池其实也充当一个实例工厂的角色，一般需要在初始化时创建一定数量的对象实例，并提供...

<!-- more -->

#### **什么是享元模式？** ####

享元模式（Flyweight）就是维护一个对象实例的共享池，实现对象实例的共享和回收利用。当系统中需要频繁创建和使用一个类的实例，而该类创建实例时需要较大的开销，使用享元模式去维护一个该类实例的共享池，实现实例的共享，可以很大程度地减少创建实例带来的开销。

享元模式下有两个角色，共享池角色和实例角色。共享池其实也充当一个实例工厂的角色，一般需要在初始化时创建一定数量的对象实例，并提供获取实例、回收实例的接口，从而实现对象实例的共享和回收利用。

  


#### **为什么要使用享元模式？** ####

系统中往往需要频繁创建和使用一个类的对象实例，如果这个类的初始化工作需要较大的开销，比如该类结构较为复杂，或者初始化过程涉及对外部资源的I/O操作，比如读写文件或进行数据库连接，那么频繁地创建该类的实例，使用完后立即丢弃，难免会造成较大的开销和浪费。

使用享元模式去维护一个该类实例的共享池，由共享池去负责类实例的创建、获取、回收、生命周期管理等，可以有效地减少创建实例带来的开销，提高系统的性能。

我们所熟知的数据库连接池就是实现享元模式的典型案例，连接池维护了一个数据库连接实例的共享池，实现数据库连接实例的共享和管理，从而较少频繁地数据连接带来的开销，优化系统的性能。

  


#### **如何实现享元模式？** ####

以上面提到的数据库连接池为例，我们需要编一个连接池类，在连接池初始化时创建一定数目的数据库连接，并将所有连接实例保存在一个集合中，对外提供获取连接实例、释放连接实例的接口。UML类图如下：

![Image 1][]  


  


示例程序：

数据库连接池类

    package patterns.structure.flyweight;
    
    import java.util.Vector;
    import java.util.concurrent.TimeUnit;
    import java.sql.Connection;
    import java.sql.DriverManager;
    import java.sql.SQLException;
    
    /**
     * Simple Connection Pool Demo
     * 
     * @author Charles Chen
     * 
     */
    public class ConnectionPool {
    	// Database Driver
    	private final String DRIVER = "com.mysql.jdbc.Driver";
    	// Database connection url
    	private final String URL = "jdbc:mysql://127.0.0.1:3306/test";
    	// Database login user name
    	private final String USER = "root";
    	// Database login password
    	private final String PASSWD = "2426";
    
    	// Initial pool size
    	private final int INIT_SIZE = 100;
    	// Maximum retry times while getting a connection
    	private final int MAX_RETRY = 3;
    
    	private Vector<Connection> pool = null; // For storage connections
    
    	/**
    	 * Constructor. Create connection instance
    	 */
    	private ConnectionPool() {
    		pool = new Vector<Connection>();
    		try {
    			for (int i = 0; i < INIT_SIZE; i++) {
    				Class.forName(DRIVER);
    				Connection connection = DriverManager.getConnection(URL, USER,
    						PASSWD);
    				pool.add(connection);
    			}
    
    		} catch (ClassNotFoundException e) {
    			// TODO Auto-generated catch block
    			e.printStackTrace();
    		} catch (SQLException e) {
    			// TODO: handle exception
    			e.printStackTrace();
    		}
    	}
    
    	/**
    	 * Create connection pool instance
    	 * 
    	 * @author Charles Chen
    	 * 
    	 */
    	private static class ConnectionPoolInstance {
    		private static final ConnectionPool instance = new ConnectionPool();
    	}
    
    	/**
    	 * 获取连接池实例
    	 * 
    	 * @return
    	 */
    	public static ConnectionPool getInstance() {
    		return ConnectionPoolInstance.instance;
    	}
    
    	/**
    	 * Get database connection from pool
    	 * 
    	 * @return
    	 */
    	public Connection getConnection() {
    		int retry = 1;
    		// If there are not connections, sleep and retry
    		while (retry <= MAX_RETRY && pool.size() < 1) {
    			try {
    				System.out.println("Not avaiable connections, retry to get...");
    				TimeUnit.SECONDS.sleep(1);
    				retry++;
    			} catch (InterruptedException e) {
    				// TODO Auto-generated catch block
    				e.printStackTrace();
    			}
    		}
    
    		if (pool.size() > 0) {
    			synchronized (pool) {
    				if (pool.size() > 0) {
    					return pool.remove(0);
    				}
    			}
    		}
    
    		System.out.println("Sorry, there are not available connections");
    		return null;
    	}
    
    	/**
    	 * Release connection
    	 * 
    	 * @param connection
    	 */
    	public void release(Connection connection) {
    		pool.add(connection);
    	}
    }

  


客户端测试程序：

    package patterns.structure.flyweight;
    
    import java.sql.Connection;
    import java.sql.ResultSet;
    import java.sql.SQLException;
    import java.sql.Statement;
    
    public class ClientTest {
    
    	/**
    	 * @param args
    	 */
    	public static void main(String[] args) {
    		// TODO Auto-generated method stub
    		ConnectionPool pool = ConnectionPool.getInstance();
    		Connection conn = pool.getConnection(); // Get a connection from pool
    		try {
    			Statement stm = conn.createStatement();
    			ResultSet rs = stm.executeQuery("select name, passwd from user");
    			while (rs.next()) {
    				System.out.println(rs.getString("name"));
    			}
    		} catch (SQLException e) {
    			// TODO Auto-generated catch block
    			e.printStackTrace();
    		}
    		pool.release(conn); // Release the connection
    	}
    
    }

  



[Image 1]: 