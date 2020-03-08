## 面试题

Redis和memcache有什么区别？Redis为什么比memcache有优势？  

## 解析

#### redis 支持复杂的数据结构
redis 相比 memcached 来说，拥有更多的数据结构，能支持更丰富的数据操作。如果需要缓存能够支持更复杂的结构和操作， redis 会是不错的选择。

#### redis 原生支持集群模式
在 redis3.x 版本中，便能支持 cluster 模式，而 memcached 没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据。

#### 性能对比
由于 redis 只使用单核，而 memcached 可以使用多核，所以平均每一个核上 redis 在存储小数据时比 memcached 性能更高。而在 100k 以上的数据中，memcached 性能要高于 redis。虽然 redis 最近也在存储大数据的性能上进行优化，但是比起 memcached，还是稍有逊色。

#### redis相比memcached的优势
1、memcached所有的值都是简单的字符串，redis作为其代替者，支持更为丰富的数据类型。  
2、redis的速度比memcached快很多。  
3、redis可以持久化其数据。