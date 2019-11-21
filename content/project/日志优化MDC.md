---
title: "日志优化MDC"
date: 2019-11-14
tags: [Spring]
categories: [Spring]
draft: true
---
# MDC(Mapped Diagnostic Context)

## 

## 实现
### 步骤1：创建MDCFilter实现Filter
filter（过滤器）作用于在intreceptor(拦截器)之前，不像intreceptor一样依赖于springmvc框架，只需要依赖于serverlet。
在过滤之前在reqId钟放入uuid并在结束之后remove
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;
import org.springframework.stereotype.Component;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;
import java.util.UUID;

/**
 * @Author shizhongcai
 * @Date 2019/11/15 11:15
 */
@Component
public class MDCFilter implements Filter {

    private static final Logger LOG = LoggerFactory.getLogger(MDCFilter.class);

    public static final String MDC_ID = "reqId";

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        boolean mdcFlag = false;
        try {
            MDC.put(MDC_ID, UUID.randomUUID().toString());
            mdcFlag = true;
        } catch (Throwable e) {
            LOG.error("MDC put error", e);
        }
        try {
            String path = ((HttpServletRequest)servletRequest).getRequestURL().toString();
            LOG.info("request path:" + path);
            filterChain.doFilter(servletRequest, servletResponse);
        } finally {
            if (mdcFlag) {
                try {
                    MDC.remove(MDC_ID);
                } catch (Throwable e) {
                    LOG.error("MDC remove error", e);
                }
            }
        }
    }

    @Override
    public void destroy() {
    }
}
```

### 步骤2：配置logback.xml,具体配置如下
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--<include resource="org/springframework/boot/logging/logback/base.xml"/>-->
    <!-- 文件输出格式,reqId就是之前在MDCFilter中放入的reqId -->
    <property name="PATTERN" value="%-12(%d{yyyy-MM-dd HH:mm:ss.SSS}) |-%-5level [%thread] %c [%L] reqId[%X{reqId}]-| %msg%n" />

    <!-- sit文件路径 -->
    <property name="FILE_PATH" value="D:/xxx/xxx/" />
    <!-- 开发环境 -->
    <!-- 文件 -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${FILE_PATH}/dream.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${FILE_PATH}/dream.%d{yyyy-MM-dd}.log</fileNamePattern>
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>${PATTERN}</pattern>
        </layout>
    </appender>

    <!--CONSOLE-->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>${PATTERN}</pattern>
        </layout>
    </appender>

    <root level="info">
        <appender-ref ref="CONSOLE" />
    </root>

</configuration>
```

### 步骤3：添加日志切面WebLogAspect,如下：
```java
import com.alibaba.fastjson.JSON;
import com.shizhongcai.business.common.config.filter.MDCFilter;
import com.shizhongcai.business.common.domain.data.AesBaseParams;
import com.shizhongcai.business.common.domain.enums.ErrorCodesEnum;
import com.shizhongcai.business.common.domain.vo.BaseReqVo;
import com.shizhongcai.business.common.domain.vo.BaseRspVo;
import com.shizhongcai.business.common.exception.BaseException;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import javax.servlet.DispatcherType;
import javax.servlet.http.HttpServletRequest;
import java.time.Duration;
import java.time.Instant;

/**
 * @Author shizhongcai
 * @Date 2019/11/15 11:13
 */
@Component
@Aspect
public class WebLogAspect {

    private static final Logger LOG = LoggerFactory.getLogger(MDCFilter.class);

    // MDC过滤器注册
    @Bean
    public FilterRegistrationBean mdcFilterRegistration() {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        registration.setDispatcherTypes(DispatcherType.REQUEST);
        registration.setFilter(new MDCFilter());
        registration.addUrlPatterns("/*");
        registration.setName("MDCFilter");
        //设置优先级,在web.xml中执行顺序是谁在前面谁先执行，而在spring boot中的FilterRegistrationBean注册过滤器的类中设置order属性（默认值Integer.MAX_VALUE），会按照order值的大小，从小到大的顺序来依次执行过滤器。
        registration.setOrder(Integer.MAX_VALUE);
        return registration;
    }

    //========================↓↓↓ controller AOP切面日志打印 ↓↓↓========================
    @Pointcut("execution(* com.shizhongcai..*Controller.*(..))")
    public void pointService(){
    }

    @Around("pointService()")
    public Object aroundMethod(ProceedingJoinPoint joinPoint) throws Throwable {
        Instant start = Instant.now();
        Instant end;
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
               int index = -1;
        for (int i = 0; i < joinPoint.getArgs().length; i++) {
            Object object = joinPoint.getArgs()[i];
            if (object instanceof AesBaseParams) {
                index = i;
                break;
            }else if(object instanceof BaseReqVo){
                index = i;
                break;
            }else{
                index = i;
                break;
            }
        }
        String method = joinPoint.getSignature().getName();
        //此处index = -1的时候表示无参数，此时调用joinPoint.getArgs[index]会报错空指针异常
        LOG.info("请求类型{}，请求方法{},入参 {}",request.getMethod(),method,index>0?JSON.toJSONString(joinPoint.getArgs()[index]):"为空");
        //执行方法本身
        //此处还可以加锁，防止接口重复调用
        Object obj;
        try {
            obj = joinPoint.proceed();
        } catch (Throwable throwable) {
            LOG.error("执行出错：", throwable);
            BaseRspVo rspVo;
            if (BaseException.class.isAssignableFrom(throwable.getClass())) {
                if (throwable instanceof BaseException) {
                    rspVo = BaseRspVo.fail(((BaseException)throwable).getCode(), throwable.getMessage());
                } else {
                    rspVo = new BaseRspVo(ErrorCodesEnum.SYS_ERROR);
                }
            } else {
                rspVo = new BaseRspVo(ErrorCodesEnum.SYS_ERROR);
            }
            return rspVo;
        }
        end = Instant.now();
        long costTime = Duration.between(start,end).toMillis();
        LOG.info("{}出参:{},耗时{}(mills)",method, JSON.toJSONString(obj),costTime);
        return obj;
    }
}

```

这样就能在控制台看到打印的日志：  
2019-11-19 16: 57:25.154 |-INFO  [http-nio-8084-exec-2] ...filter.MDCFilter [41] <font color = 'red'>reqId[34a4c7c2-4cc7-41b3-b9f9-6b9a67a592f5]</font>-| request path: http://127.0.0.1:8084/dream/demo/testPool  
2019-11-19 16: 57:25.174 |-INFO  [http-nio-8084-exec-2] ...filter.MDCFilter [79] reqId[34a4c7c2-4cc7-41b3-b9f9-6b9a67a592f5]-| 请求类型POST，请求方法testPool,入参 为空  
2019-11-19 16: 57:25.178 |-INFO  [http-nio-8084-exec-2] ...DemoService [23] reqId[34a4c7c2-4cc7-41b3-b9f9-6b9a67a592f5]-| org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor@e69cd6c  
2019-11-19 16: 57:28.275 |-INFO  [myThreadPoolTaskExecutor-1] ...DemoService [25] reqId[]-| 1
2019-11-19 16: 57:28.332 |-INFO  [http-nio-8084-exec-2] ...filter.MDCFilter [101] reqId[34a4c7c2-4cc7-41b3-b9f9-6b9a67a592f5]-| testPool出参:null,耗时7(mills)  

这样我们就能将一整条请求串联起来，只要搜索关键字，就能很方便的找到该请求的所有日志