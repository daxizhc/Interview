## 面试题

考虑redis的时候，有没有考虑容量？大概数据量会有多少？  

## 解析

根据实际需要设置redis的最大容量，参数是配置文件中的maxmemory，如
```
maxmemory 1024mb
```