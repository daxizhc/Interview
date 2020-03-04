#SpringSecurity表单登录原理


####1、提出问题
SpringSecurity中配置表单登录的常见代码如下:
```
public void configure(HttpSecurity http) throws Exception {
        http
            .formLogin()
            .loginPage("登录页面地址")
            .loginProcessingUrl("登录表单发送地址")
            .successHandler(authenticationSuccessHandler)
            .failureHandler(authenticationFailureHandler)
            .permitAll();
    }
```
其中http通过重写WebSecurityConfigurerAdapter.configure(HttpSecurity httpSecurity)方法获得。

为什么说这样就已经配置好了表单登录？原理是什么？

####2、源码解析
通过阅读源码，可以发现它经历了以下几个步骤：

a、formLogin()方法给AbstractConfiguredSecurityBuilder对象中的configurers变量增加了一个FormLoginConfigurer对象。

b、configurers变量统一在AbstractConfiguredSecurityBuilder.doBuild()方法中配置生效，该方法中又调用了configure()方法，如下所示:
```
private void configure() throws Exception {
    Collection<SecurityConfigurer<O, B>> configurers = getConfigurers();
    for (SecurityConfigurer<O, B> configurer : configurers) {
        configurer.configure((B) this);
    }
}
```
可见添加了FormLoginConfigurer对象后，目的就是要调用它的configure方法。
c、FormLoginConfigurer中configure在其父类AbstractAuthenticationFilterConfigurer中，方法如下:
```
public void configure(B http) throws Exception {
    PortMapper portMapper = http.getSharedObject(PortMapper.class);
    if (portMapper != null) {
        authenticationEntryPoint.setPortMapper(portMapper);
    }

    RequestCache requestCache = http.getSharedObject(RequestCache.class);
    if (requestCache != null) {
        this.defaultSuccessHandler.setRequestCache(requestCache);
    }

    authFilter.setAuthenticationManager(http
            .getSharedObject(AuthenticationManager.class));
    authFilter.setAuthenticationSuccessHandler(successHandler);
    authFilter.setAuthenticationFailureHandler(failureHandler);
    if (authenticationDetailsSource != null) {
        authFilter.setAuthenticationDetailsSource(authenticationDetailsSource);
    }
    SessionAuthenticationStrategy sessionAuthenticationStrategy = http
            .getSharedObject(SessionAuthenticationStrategy.class);
    if (sessionAuthenticationStrategy != null) {
        authFilter.setSessionAuthenticationStrategy(sessionAuthenticationStrategy);
    }
    RememberMeServices rememberMeServices = http
            .getSharedObject(RememberMeServices.class);
    if (rememberMeServices != null) {
        authFilter.setRememberMeServices(rememberMeServices);
    }
    F filter = postProcess(authFilter);
    http.addFilter(filter);
}
```
此方法中的authFilter正是在FormLoginConfigurer的构造方法中传入的UsernamePasswordAuthenticationFilter。

####3、总结回顾
通过以上分析，我们可以看出，在SpringSecurity中配置表单登录，源码中最终的操作就是增加了一个UsernamePasswordAuthenticationFilter过滤器。
这个过滤器被加入到SpringSecurity的过滤器链当中，对登录表单请求进行校验，登录失败则拒绝请求，成功则调用下一个过滤器处理。
