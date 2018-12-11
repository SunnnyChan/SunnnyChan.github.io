# Java 并发编程的艺术
```md
By 方腾飞 魏鹏 程晓明
Reading Note Start : 2018-12-06
```

## Chapters

### [01 - 并发编程的挑战]
* [上下文切换]
* [死锁]
* [资源限制的挑战]

### [02 - Java 并发机制实现原理]
* [volatile]
* [synchronized]
* [原子操作]

### [03 - Java 内存模型]
* [内存模型基础](chapter/03-1-JMM.md)
* [重排序](chapter/03-2-reorder.md)
* [顺序一致性](chapter/03-3-sequential-consistency.md)
* [volatile 的内存语义](chapter/03-04-volatile-memory-semantics.md)
* [锁 的内存语义](chapter/03-05-lock-memory-semantics.md)
* [final 域的内存语义](chapter/03-06-final-memory-semantics.md)
* [happens-before](chapter/03-07-happens-before.md)
* [双重检查锁定和延迟初始化](chapter/)03-08-recheck-lock-and-latency-init.md
* [内存模型综述](chapter/03-09-summary.md)

### [04 - Java 并发编程基础]
* [线程]
* [启动和终止线程]
* [线程间通信]
* [线程应用实例]

### [05 - Java 中的锁]
* [Lock 接口]
* [队列同步器]
* [重入锁]
* [读写锁]
* [LockSupport 工具]
* [Condition 接口]

### [06 - Java 并发容器和框架]
* [ConcurrentHashMap]
* [ConcurrentLinkedQueue]
* [Java 中的阻塞队列]
* [Fork/Join 框架]

### [07 - Java 中的13个原子操作类]

### [08 - Java 中的并发工具类]
* [等待多线程完成的 CountDownLatch]
* [同步屏障 CyclicBarrier]
* [控制并发线程数 Semaphore]
* [线程间交换数据 Exchanger]

### [09 - Java 中的线程池]

### [10 - Executor 框架]
* [Executor]
* [ThreadPoolExecutor]
* [ScheduledThreadPoolExecutor]
* [FutureTask]

### [11 - Java 并发编程实践]
* [生产者消费者模式]
* [线上问题定位]
* [性能测试]
* [异步任务池]