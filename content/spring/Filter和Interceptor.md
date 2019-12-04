---
title: "Filter和Interceptor"
date: 2019-11-20
tags: [Spring]
categories: [Spring]
draft: true
---

Filter调用链
![Filter调用链](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/project/1704592-93aadaf76ab4746b.webp)


```java
// Interceptor的源码
public interface HandlerInterceptor {

    // 在调用真正的处理请求类之前调用
    boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception;

    // 在调用真正的处理请求类之后调用
    void postHandle(
            HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)
            throws Exception;

 // 在完成渲染或者出错之后调用
    void afterCompletion(
            HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
            throws Exception;

}
```


refer to: https://www.jianshu.com/p/5f5e5211bbdb
refer to: https://www.cnblogs.com/junzi2099/p/8022058.html