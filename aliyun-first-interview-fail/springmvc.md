## 面试题

请详细描述springmvc处理请求全流程？

## 解析

1、判断是不是isMultipart，是的话进行封装。   

2、遍历handlerMappings中的handlerMapping，直到获取到1个Handler（例如controller的某个方法），封装为HandlerExecutionChain后返回。

3、遍历handlerAdapters中的handlerAdapter，直到获取一个适配的HandlerAdapter，例如RequestMappingHandlerAdapter、
HttpRequestHandlerAdapter、SimpleControllerHandlerAdapter等。

4、调用interceptor.preHandle

5、handlerAdapter处理handler，返回ModelAndView或抛出异常

6、未抛出异常的话，调用interceptor.postHandle

7、处理ModelAndView或者异常，如渲染ModelAndView、processHandlerException

