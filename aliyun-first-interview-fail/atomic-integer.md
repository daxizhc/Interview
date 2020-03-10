## 面试题

讲一讲AtomicInteger，为什么要用CAS而不是synchronized？

## 解析

synchronized是悲观锁，会导致其他线程阻塞，cpu切换线程又会耗费资源。

CAS不会引起其他线程阻塞，只会使其重试。

对于改变一个int变量值这种操作，显然CAS性能更好。