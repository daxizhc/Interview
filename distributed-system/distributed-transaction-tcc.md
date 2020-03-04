## 分布式事务框架EasyTransaction中TCC原理

TCC分布式事务，顾名思义，三个大写字母分别指代try，confirm，cancel。因此，TCC的被调用方也必须实现这三个方法。

### 调用流程

#### try阶段：
1、CallWrapUtil为调用的接口生成JDK动态代理，调用方使用的对象即为此动态代理对象

2、开启柔性事务，注册TransactionHook，记录日志

3、将调用包装为CompensableCallerWrapper，记录日志后执行调用

try阶段如果报错，本地事务回滚，调用结束

#### confirm阶段:

1、在try正常执行完成后，TransactionHook或者ConsistentGuardianDaemon会调ConsistentGuardian.process()方法

2、TccMethodExecutor执行onDismatch方法，调用远程confirm方法
