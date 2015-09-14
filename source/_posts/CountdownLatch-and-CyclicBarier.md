title: 同步辅助类CountdownLatch与CyclicBarrier
date: 2014-05-15 22:58:53.0
tags:
- 同步
- 并发编程
categories:
- 并发编程

---

 JDK5以后java.util.concurrent包下引入了大量解决并发问题的新类，其中比较常用的两个并发辅助类就是CountDownLatch和CyclicBarrierCountdownLatch当一个或多个线程需要等待其他线程完成后才执行，这时候可以用CountdownLatch来实现。CountdownLatch初始化时可以指定一个计数，代表执行的线程个数。Countdown...

<!-- more -->

 JDK5以后java.util.concurrent包下引入了大量解决并发问题的新类，其中比较常用的两个并发辅助类就是CountDownLatch和CyclicBarrier

  


**CountdownLatch**

  


当一个或多个线程需要等待其他线程完成后才执行，这时候可以用CountdownLatch来实现。

CountdownLatch初始化时可以指定一个计数，代表执行的线程个数。

  


CountdownLatch提供两个常用方法：

（1） countdown()方法，减少计数

（3） await()方法，如果当前计数不为0，将阻塞当前线程，直至计数为0被唤醒

  


值得注意的是：CountdownLatch的计数达到0之后不能再复位，因此CountdownLatch只能被触发一次

  


举个实际的应用例子：例如一个产品由7个零部件构成，那么可以设计7个相互独立的线程，每个线程完成一个零部件的生产。当零部件都生产完毕后，需要有一个线程负责组装。此时我们可以使用CountdownLatch来实现，初始化计数为7，生产线程中执行完生产任务就调用countdown()减少计数，而组装线程中调用await()，直到计数减少为0，即所有零件生产完毕后，开始组装任务。

  


生产者线程：

    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    
    public class Worker implements Runnable {
           private CountDownLatch latch ;
           private int partId ;
    
           public Worker(CountDownLatch latch, int partId) {
                 this.latch = latch;
                 this.partId = partId;
          }
    
           @Override
           public void run() {
                 // TODO Auto-generated method stub
                 if (!Thread.interrupted()) {
                      System. out.println( "Manufacturing part " + partId );
                       latch.countDown();
                }
          }
    
    }

  


加工者线程：

    import java.util.concurrent.CountDownLatch;
    
    public class Processor implements Runnable {
           private CountDownLatch latch ;
    
           public Processor(CountDownLatch latch) {
                 this.latch = latch;
          }
    
           @Override
           public void run() {
                 // TODO Auto-generated method stub
                 try {
                       if (!Thread.interrupted()) {
                             latch.await();
                            System. out.println("Manufacturing done!" );
                            System. out.println("Processing the product." );
                      }
                } catch (InterruptedException e) {
                       // TODO Auto-generated catch block
                      e.printStackTrace();
                }
    
          }
    
    }

  


测试程序：

    public static void main(String[] args) {
    	 // TODO Auto-generated method stub
    	ExecutorService exec = Executors. newCachedThreadPool();
    	 int partCount = 7;
    	CountDownLatch latch = new CountDownLatch(partCount);
    	 for (int i = 1; i <= partCount; i++) {
    		  exec.execute( new Worker(latch, i));
    	}
    	exec.execute( new Processor(latch));
    }

  


该程序初始化了一个计数为7的CountDownLatch，并创建了7个工人线程，每个线程负责生产零部件，然后调用latch.countDown()减少计数。而加工者线程Processor中，由于调用了latch.await()而处于等待状态，直到CountDownLatch的计数减为0才被唤醒，执行包装任务。程序输出如下：

  


Manufacturing part 1

Manufacturing part 7

Manufacturing part 6

Manufacturing part 2

Manufacturing part 4

Manufacturing part 5

Manufacturing part 3

Manufacturing done!

Processing the product.

  


  


**CyclicBarrier**

  


当一组线程在执行过程中需要不时地相互等待，这时候可以用CyclicBarrier来实现。它允许多个线程相互等待，直到等待的线程数达到一个公共屏障点(common barrier point)时被唤醒。

CyclicBarrier初始化时可以指定一个计数以及一个Runnable，当在CyclicBarrier对象上调用await()方法的线程数达到指定的计数，将会触发指定的Runnable线程，并唤醒所有等待的线程继续执行。

  


举一个实际的应用例子。有一支5人自行车队要完成一次50公里的骑行，车队决定每骑行10公里，就停下来等待其他成员到齐后，休息一会儿再出发，直到到达目的地。此时我们可以使用CyclicBarrier来实现，初始化计数为5，如果等待的线程数达到5，就判断当前距离是否达到50公里，如果还没达到，就休眠一段时间表示休息，如果达到，就终止所有线程，表示已经到达目的地。

  


值得注意的是：CyclicBarrier的计数达到指定数目后，可以复位，继续循环重用。

  


骑行者线程：

    import java.util.concurrent.BrokenBarrierException;
    import java.util.concurrent.CyclicBarrier;
    
    public class Rider implements Runnable {
           private CyclicBarrier barrier ;
           private int riderId ;
           private int distance = 0;
    
           public Rider(CyclicBarrier barrier, int riderId) {
                 this.barrier = barrier;
                 this.riderId = riderId;
          }
    
           @Override
           public void run() {
                 // TODO Auto-generated method stub
                 try {
                       while (!Thread.interrupted()) {
                             distance += 10;
                            System. out.println("Rider " + riderId + " distance: "
                                        + distance);
                             barrier.await();
                      }
                } catch (InterruptedException e) {
                       // TODO Auto-generated catch block
                      e.printStackTrace();
                } catch (BrokenBarrierException e) {
                       // TODO Auto-generated catch block
                      e.printStackTrace();
                }
          }
    
           public int getDistance () {
                 return distance ;
          }
    }

测试程序：

    public static void main(String[] args) {
    	 // TODO Auto-generated method stub
    	 final ExecutorService exec = Executors.newCachedThreadPool();
    	 final List<Rider> riders = new ArrayList<Rider>();
    	 final int TOTAL_DIST = 50;
    	 int riderCount = 5;
    	CyclicBarrier barrier = new CyclicBarrier(riderCount, new Runnable() {
    		   @Override
    		   public void run() {
    				 // TODO Auto-generated method stub
    				 int distance = riders.get(0).getDistance();
    				 if (distance >= TOTAL_DIST) {
    					  System. out.println("Reach the end!" );
    					  exec.shutdownNow();
    					   return;
    				}
    				System. out.println(TOTAL_DIST - distance
    							+ "km left. Take a break...");
    				 try {
    					  TimeUnit. MILLISECONDS .sleep(1000);
    				} catch (InterruptedException e) {
    					   // TODO Auto-generated catch block
    					  e.printStackTrace();
    				}
    		  }
    	});
    	 for (int i = 1; i <= riderCount; i++) {
    		  Rider rider = new Rider(barrier, i);
    		  riders.add(rider);
    		  exec.execute(rider);
    	}
    }

  


  


程序中，Rider线程表示一个骑行者，骑行者先是骑行了10km，然后调用barrier.await()进入等待状态。CyclicBarrier的初始化计数为5，也就是说当5个骑行者都骑行了10km进入等待状态之后，CyclicBarrier中的Runnable任务将被执行，Runnable任务中先是判断某一个骑行者的骑行距离是否达到总距离，如果达到表示到达了终点，否则，休眠1s后唤醒5个处于等待状态的Rider线程，继续骑行。程序输出如下：

  


Rider 1 distance: 10

Rider 3 distance: 10

Rider 2 distance: 10

Rider 5 distance: 10

Rider 4 distance: 10

40km left. Take a break...

Rider 4 distance: 20

Rider 5 distance: 20

Rider 2 distance: 20

Rider 3 distance: 20

Rider 1 distance: 20

30km left. Take a break...

Rider 1 distance: 30

Rider 5 distance: 30

Rider 3 distance: 30

Rider 2 distance: 30

Rider 4 distance: 30

20km left. Take a break...

Rider 4 distance: 40

Rider 2 distance: 40

Rider 5 distance: 40

Rider 3 distance: 40

Rider 1 distance: 40

10km left. Take a break...

Rider 4 distance: 50

Rider 1 distance: 50

Rider 5 distance: 50

Rider 2 distance: 50

Rider 3 distance: 50

Reach the end!

  


善于应用这些同步辅助类可以减少编程的工作量，使得我们可以将更多精力放在其他功能的实现上。