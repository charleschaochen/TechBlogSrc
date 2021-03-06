title: Redis持久化策略
date: 2014-11-10 10:04:53.0
tags:
- redis
- 缓存
categories:
- 数据库技术

---

Redis是一个支持持久化的内存数据库，所谓持久化就是将内存中的数据同步到磁盘上。Redis支持两种持久化方式，第一种是Snapshot快照方式，这也是默认的持久化方式，另一种是Append-only File（AOF）的方式。Snapshotting快照方式快照持久化方式是将数据以快照的方式存入到一个二进制文件中，快照文件默认名称为dump.rdb，默认是保存在启动redis-server的路径...

<!-- more -->

Redis是一个支持持久化的内存数据库，所谓持久化就是将内存中的数据同步到磁盘上。Redis支持两种持久化方式，第一种是Snapshot快照方式，这也是默认的持久化方式，另一种是Append-only File（AOF）的方式。

  


#### **Snapshotting快照方式** ####

快照持久化方式是将数据以快照的方式存入到一个二进制文件中，快照文件默认名称为dump.rdb，默认是保存在启动redis-server的路径下。

（1）如何配置自动快照持久化

在redis.conf的SNAPSHOTTING配置段，可以配置自动快照持久化的规则。默认的配置是：

save 900 1\# 900秒（15分钟）内至少有1个key被修改，则发起快照保存，相当于每900秒检查一次

save 300 10 \# 300秒（5分钟）内至少有10个key被修改，则发起快照保存，相当于每300秒检查一次

save 60 10000 \# 60秒（1分钟）内至少有10000个key被修改，则发起快照保存，相当于每60秒检查一次

  


（2）快照持久化的原理

当redis发起一次快照保存时，首先会调用fork，启动一个子进程将当前数据的快照写入到一个临时文件中（这是为了避免dump失败而影响了原来的快照文件），而与此同时，父进程仍然正常处理客户端的请求，而父进程对数据的修改是在一个副本中进行，不会影响子进程的保存工作。当子进程将快照写入到临时文件完毕后，用临时文件替换原来的快照文件，然后子进程退出，父进程才将对数据的修改从副本中同步到内存。

**所以redis的工作路径和配置的dump文件的路径必须在同一个分区上，否则快照持久化会失败**。这是因为快照持久化时，redis会将数据写入到一个临时文件，再从临时文件建立一个硬链接到dump文件的保存路径，而硬链接不支持跨分区。

  


（3）客户端主动发起快照持久化

redis客户端使用save或bgsave命令主动发起快照持久化动作。**save和bgsave的区别在于前者是在主进程中进行持久化动作，这也就意味着主进程会阻塞当前所有客户端的请求，直到持久化动作完成，所以不推荐使用，而后者是启动一个子进程去完成持久化。**

  


（4）Snapshot方式的缺点

Snapshot快照方式的持久化策略有两个明显的劣势：

首先，每次持久化动作都是将所有数据写入到磁盘，如果数据量太大，持久化的IO操作会严重影响性能

另外，由于持久化动作是每隔一段时间执行一次，所以如果redis服务器down掉的话，最后一次持久化之后的所有修改就会丢失

####     ####

#### **Append-only File方式** ####

Append-only方式比snapshot方式更具有持久性，AOF方式会将从客户端收到的每一个写指令都通过write函数追加到文件中，默认的文件名为appendonly.aof，重启redis时，会根据文件中保存的写指令重建数据库内容。相比snapshot方式，AOF方式可以保证每次写操作都能被保存下来，且写指令是以追加的方式写入磁盘，并不会引起太大的IO消耗。

  


（1） 如何配置使用append-only file模式进行持久化

在redis.conf的”APPEND ONLY MODE“配置段，可以配置AOF持久化的规则：

appendonly yes \# 使用AOF持久化策略

  


由于操作系统默认会缓存write函数执行的修改，所以客户端的写指令并不是马上追加到文件中，所以也有可能导致修改丢失。但我们可以配置**appendfsync**来强制操作系统将写指令追加到文件中：

\# appendfsync always \# 每收到一个写指令就马上强制写入磁盘，可以保证持久化的可靠性，但速度慢

appendfsync everysec \# 每隔一秒强制写入次磁盘，最常用

\# appendfsync no \# 不适用强制策略，完全依靠操作系统

  


（2）AOF方式的缺点

使用AOF方式最直接导致的是AOF文件会越来越大，假设客户端执行了1000个incr num操作，AOF文件中将保存1000个指令，且redis重启时将执行1000次incr指令，事实上只需要将set num 1000指令追加到文件中即可。

幸运的是，redis提供了**bgrewriteaof**指令来重写aof文件，原理如下：

a） redis调用fork，启动一个子进程根据当前数据库内容，将重建数据库的指令写入到一个临时文件中

b） 与此同时，父进程仍然正常接收客户端的请求，每收到一个写指令，就将其追加到aof文件中，并缓存起来

c） 当子进程将指令写入到临时文件完毕后，父进程会将缓存的写指令追加到临时文件中

d） 最后，父进程用临时文件替换掉原来的aof文件