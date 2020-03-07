## 面试题

HashMap检测到hash冲突后，将元素插入在链表的末尾还是开头？  

## 解析

根据 [HashMap put方法的执行过程?](./hashmap-put.md) 中put流程可以看出，当在桶中遍历到最后为null时，新插入元素。

因此HashMap检测到hash冲突后，会将元素插入在链表的末尾。