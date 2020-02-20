## 面试题

Java容器有哪些？哪些是同步容器,哪些是并发容器？

## 解析

java中常用的容器总体按结构可以分为List、Map、Set三大类，按并发性可以分为同步容器，并发容器，和普通容器，如下图所示：

| |List|Map|Set|  
|:----:|:---:|:---:|:---:|  
|普通容器  |ArrayList/LinkedList|HashMap|HashSet/TreeSet|
|同步容器  |Vector|HashTable|Collections.SynchronizedSet|
|并发容器  |CopyOnWriteArrayList|ConcurrentHashMap  ConcurrentSkipListMap|CopyOnWriteArraySet  ConcurrentSkipListSet|

