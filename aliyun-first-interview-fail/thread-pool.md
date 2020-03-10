## 面试题

线程池的工作原理，几个重要参数，然后给了具体几个参数分析线程池会怎么做，最后问阻塞队列的作用是什么？  

## 解析

####线程池的工作原理

线程池刚启动，当前线程数小于corePoolSize时，尝试向corePool增加一个新线程来执行任务。  

线程池正常运行，线程数大于等于corePoolSize时，向阻塞队列增加任务，阻塞队列默认长度0x7fffffff。  

若阻塞队列满了之后，线程数小于maximumPoolSize时，增加非core线程来执行任务，直到线程数大于maximumPoolSize后，拒绝任务。

线程池SHUTDOWN状态时，只接受增加不带firstTask的线程，拒绝其他任务。  

####几个重要参数

corePoolSize：控制工作线程数量

maximumPoolSize：控制最大工作线程数量

workQueue：阻塞队列

handler：拒绝策略

####阻塞队列作用

存放未执行的任务，提供给工作线程执行。

