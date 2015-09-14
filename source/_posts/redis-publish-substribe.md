title: Redis实现发布/订阅模型
date: 2014-11-10 10:56:08.0
tags:
- redis
- 发布/订阅
categories:
- 数据库技术

---

Redis提供了publish和subscribe/psubscibe指令来实现发布/订阅模型，发布和订阅的目标称为通道(channel)。subscribe/psubscribe了一个或多个通道的客户端，可以收到其他客户端向这个通道publish的消息。subscribe和psubscribe的区别是，前者指定具体的通道名称，而后者可以指定一个正则表达式，匹配这个表达式的通道都被订阅。例如，re...

<!-- more -->

Redis提供了publish和subscribe/psubscibe指令来实现发布/订阅模型，发布和订阅的目标称为通道(channel)。

subscribe/psubscribe了一个或多个通道的客户端，可以收到其他客户端向这个通道publish的消息。

subscribe和psubscribe的区别是，前者指定具体的通道名称，而后者可以指定一个正则表达式，匹配这个表达式的通道都被订阅。

例如，redis-cli-1执行了**subscribe channel1 channel2**，表示订阅了channel1 和channel2 两个通道，而redis-cli-2执行了psubscribe channel，表示订阅了匹配channel表达式的通道。

那么此时如果redis-cli-0执行了publish channel1 "This is a message from client-0"，那么redis-cli1和redis-cli2都会收到该消息。

  


Jedis实现pub/sub模型

对于发布消息，Jedis提供publish接口来发布消息，可以实现向某个channel发布消息

对于订阅消息，Jedis提供subscribe和psubscribe接口来订阅消息，调用订阅接口时需要传入一个JedisPubSub抽象类的实现类实例，用来监听和处理订阅的事件。我们可以重写JedisPubSub的方法，如onMessage，onPMessage表示收到正常订阅和用表达式方式订阅的消息等等。

示例代码：

    package redis.pubsub;
    
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.TimeUnit;
    
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisPubSub;
    import redis.pool.JedisUtils;
    
    public class PubSubTest {
    
         public static void main(String[] args) {
                // TODO Auto-generated method stub
               ExecutorService exec = Executors.newCachedThreadPool();
               String channel = "c1";
                exec.execute( new Publisher( "p1", channel));
                exec.execute( new Subscriber( "s1", channel, null));
                exec.execute( new Subscriber( "s2", null, "c*"));
         }
    }
    
    class Publisher implements Runnable {
         private String name;
         private String channel;
    
         public Publisher(String name, String channel) {
                // TODO Auto-generated constructor stub
                this. name = name;
                this. channel = channel;
         }
    
         @Override
         public void run() {
                // TODO Auto-generated method stub
               Jedis jedis = JedisUtils. getInstance();
                while ( true) {
                     jedis.publish( channel, "New Message from " + this.name );
                     try {
                         TimeUnit. SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                          // TODO Auto-generated catch block
                          e.printStackTrace();
                    }
               }
         }
    }
    
    /**
     * Subscriber thread
     *
     * @author Charles Chen
     * @date 2014年11月8日
     */
    class Subscriber implements Runnable {
         private String name;
         private String channel;
         private String pattern;
    
         public Subscriber(String name, String channel, String pattern) {
                // TODO Auto-generated constructor stub
                this. name = name;
                this. channel = channel;
                this. pattern = pattern;
         }
    
         @Override
         public void run() {
                // TODO Auto-generated method stub
               Jedis jedis = JedisUtils. getInstance();
                if ( this. pattern != null) {
                     jedis.psubscribe( new SubscribeListener(this.name ), pattern );
               } else {
                     jedis.subscribe( new SubscribeListener(this.name ), channel );
               }
                while ( true)
                    ;
         }
    
    }
    
    /**
     * Subscribe listener
     *
     * @author Charles Chen
     * @date 2014年11月8日
     */
    class SubscribeListener extends JedisPubSub {
         private String subscriber;
    
         public SubscribeListener(String subscriber) {
                // TODO Auto-generated constructor stub
                this. subscriber = subscriber;
         }
    
         @Override
         public void onMessage(String channel, String message) {
                // TODO Auto-generated method stub
               System. out.println( this. subscriber + " received message from channel ["
                         + channel + "]:" + message);
         }
    
         @Override
         public void onPMessage(String pattern, String channel, String message) {
                // TODO Auto-generated method stub
               System. out.println( this. subscriber + " received message from channel ["
                         + channel + "]:" + message);
         }
    
         @Override
         public void onPSubscribe(String pattern, int subscribedChannels) {
                // TODO Auto-generated method stub
               System. out.println( this. subscriber + " subscribe channel in pattern: "
                         + pattern + ", channels count: " + subscribedChannels);
         }
    
         @Override
         public void onPUnsubscribe(String pattern, int subscribedChannels) {
                // TODO Auto-generated method stub
               System. out.println( this. subscriber
                         + " unsubscribe channel in pattern: " + pattern
                         + ", channels count: " + subscribedChannels);
    
         }
    
         @Override
         public void onSubscribe(String pattern, int subscribedChannels) {
                // TODO Auto-generated method stub
               System. out.println( this. subscriber + " subscribe channel: " + pattern
                         + ", channels count: " + subscribedChannels);
    
         }
    
         @Override
         public void onUnsubscribe(String pattern, int subscribedChannels) {
                // TODO Auto-generated method stub
               System. out.println( this. subscriber + " unsubscribe channel: " + pattern
                         + ", channels count: " + subscribedChannels);
         }
    
    }

  
