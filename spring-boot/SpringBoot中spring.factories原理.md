#SpringBoot中spring.factories是如何被加载的

在SpringApplication的构造方法中，调用了getSpringFactoriesInstances(ApplicationContextInitializer.class)方法

随后一路debug，就能运行到了这行代码：SpringFactoriesLoader.loadFactoryNames(type, classLoader)

loadFactoryNames调用了loadSpringFactories方法，代码如下:
```$xslt
private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
    MultiValueMap<String, String> result = cache.get(classLoader);
    if (result != null) {
        return result;
    }

    try {
        Enumeration<URL> urls = (classLoader != null ?
        // 读取所有jar包下，/META-INF/spring.factories文件中的内容，放入一个MultiValueMap中
                classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
                ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
        result = new LinkedMultiValueMap<>();
        while (urls.hasMoreElements()) {
            URL url = urls.nextElement();
            UrlResource resource = new UrlResource(url);
            Properties properties = PropertiesLoaderUtils.loadProperties(resource);
            for (Map.Entry<?, ?> entry : properties.entrySet()) {
                String factoryClassName = ((String) entry.getKey()).trim();
                for (String factoryName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
                    result.add(factoryClassName, factoryName.trim());
                }
            }
        }
        cache.put(classLoader, result);
        return result;
    }
    catch (IOException ex) {
        throw new IllegalArgumentException("Unable to load factories from location [" +
                FACTORIES_RESOURCE_LOCATION + "]", ex);
    }
}
```

其中，当key为EnableAutoConfiguration时，value就是一长串的**AutoConfiguration了，这些在后面的AutoConfigurationImportSelector类中就会用到。

下面是AutoConfigurationImportSelector.getCandidateConfigurations方法:
```$xslt
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
            getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
            + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

这样，就为springboot的自动装配完成了很重要的一步工作。