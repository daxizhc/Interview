#Spring循环依赖解决

spring中能够解决循环依赖的是单例模式，构造器的循环依赖或者prototype的循环依赖都无法解决。

单例模式解决这个问题采用的手段是三级缓存，在DefaultSingletonBeanRegistry这个类中：
```
//一级缓存
/** Cache of singleton objects: bean name --> bean instance */
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
//二级缓存
/** Cache of early singleton objects: bean name --> bean instance */
private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);
//三级缓存
/** Cache of singleton factories: bean name --> ObjectFactory */
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);
```
假设有两个单例bean，beanA和beanB，beanA依赖beanB，beanB依赖beanA。

在spring容器运行到尾声，执行AbstractApplicationContext.refresh()方法时，会初始化所有非懒加载的bean。

beanA和beanB也是在这个时候被初始化的。

初始化bean，只需要调用AbstractBeanFactory.getBean()方法。

首先初始化beanA，调用链如下：getBean -> doGetBean -> getSingleton(String beanName, ObjectFactory<?> singletonFactory) 

注意，在doGetBean时，会先调用一下getSingleton(String beanName)方法，此方法继续调用getSingleton(String beanName, boolean allowEarlyReference)

getSingleton(String beanName, boolean allowEarlyReference)方法会判断一级缓存不存在目标bean，并且目标bean正在单例创建过程中，
如果是，锁定一级缓存，搜索二级缓存，若二级缓存没有，将目标bean从三级缓存放入二级缓存，若二级缓存有，则直接返回。

调用到getSingleton(String beanName, ObjectFactory<?> singletonFactory)时，先判断一级缓存singletonObjects中是否包含目标bean，有则直接返回，没有则创建bean。

创建beanA，调用链如下：createBean -> doCreateBean -> populateBean

在doCreateBean方法中，如果开启earlySingletonExposure，则会将目标bean放入三级缓存，二级缓存earlySingletonObjects同时remove一下目标bean。

populateBean方法是为bean填充各种属性值的，理所当然，beanA依赖的beanB也是在这个时候被填充的。

populateBean方法中，AutowiredAnnotationBeanPostProcessor.postProcessPropertyValues方法为beanA找出需要注入的项进行注入。

beanA通过setter方法注入的beanB，因此AutowiredAnnotationBeanPostProcessor通过方法级别的注入来填充beanA，
通过beanFactory.resolveDependency来找出方法的参数（需要注入的beanB），而为了获取beanB，再次调用了beanFactory.getBean()方法。





