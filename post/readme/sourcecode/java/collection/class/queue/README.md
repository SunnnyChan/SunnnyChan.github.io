# Queue

* [ArrayDeque（数组双端队列）]()

* [ArrayBlockingQueue（基于数组的并发阻塞队列）](JUC.ArrayBlockingQueue.md) 
```md
在构造时需要指定容量， 并可以选择是否需要公平性，
如果公平参数被设置true，等待时间最长的线程会优先得到处理
（其实就是通过将ReentrantLock设置为true来达到这种公平性的：即等待时间最长的线程会先操作）。

通常，公平性会使你在性能上付出代价，只有在的确非常需要的时候再使用它。
它是基于数组的阻塞循环队 列，此队列按 FIFO（先进先出）原则对元素进行排序。
```
* [PriorityQueue（优先级队列）](java.util.PriorityQueue.md)
```md
是一个带优先级的 队列，而不是先进先出队列。
PriorityQueue是没有容量限制的。
```

* [PriorityBlockingQueue](JUC.ArrayBlockingQueue.md)
```md
PriorityBlockingQueue是对 PriorityQueue的再次包装，是基于堆数据结构的。

```
* [DelayQueue（延期阻塞队列）（阻塞队列实现了BlockingQueue接口）] (JUC.DelayQueue.md)
```md
基于PriorityQueue来实现的，
```