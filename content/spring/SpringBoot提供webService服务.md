---
title: "WebService"
date: 2021-03-25
tags: [WebService]
categories: [Spring]
draft: true
---
# SpringBoot提供webService服务

## 1.编码
### 1.1第一步：引入maven依赖
```
    <dependency>
        <groupId>org.apache.cxf</groupId>
        <artifactId>cxf-spring-boot-starter-jaxws</artifactId>
        <version>3.2.5</version>
    </dependency>
```
### 1.2第二步：创建CXF配置类
```java

import com.shizhongcai.webservice.server.advertise.*;
import org.apache.cxf.Bus;
import org.apache.cxf.bus.spring.SpringBus;
import org.apache.cxf.jaxws.EndpointImpl;
import org.apache.cxf.transport.servlet.CXFServlet;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.annotation.Resource;
import javax.xml.ws.Endpoint;


@Configuration
public class CXFConfig {


    /**
     *     这里需要注意  由于springmvc 的核心类 为DispatcherServlet
     *     此处若不重命名此bean的话 原本的mvc就被覆盖了。可查看配置类：DispatcherServletAutoConfiguration
     *     一种方法是修改方法名称 或者指定bean名称
     *     这里需要注意 若beanName命名不是 cxfServletRegistration 时，会创建两个CXFServlet的。
     *     具体可查看下自动配置类：Declaration org.apache.cxf.spring.boot.autoconfigure.CxfAutoConfiguration
     *     也可以不设置此bean 直接通过配置项 cxf.path 来修改访问路径的
     * @return
     */
    @Bean("cxfServletRegistration")
    public ServletRegistrationBean dispatcherServlet() {
        //注册servlet 拦截services 开头的请求 不设置 默认为：/services/*
        return new ServletRegistrationBean(new CXFServlet(), "/services/*");
    }

    @Resource
    private AdvertiseService advertiseService;


    @Bean(name = Bus.DEFAULT_BUS_ID)
    public SpringBus springBus() {
        SpringBus springBus = new SpringBus();
        return springBus;
    }

    /**
     * 发布soap直连地址
     * @return
     */
    @Bean
    public Endpoint reportEndpoint() {
        EndpointImpl endpoint = new EndpointImpl(springBus(), advertiseService);
        //发布地址
        endpoint.publish("/report");
        return endpoint;
    }
}

```

### 1.3第三步：创建webservice接口
```java

import com.shizhongcai.webservice.server.advertise.vo.AdvertiseReqVo;
import com.shizhongcai.webservice.server.advertise.vo.AdvertiseRspVo;

import javax.jws.WebMethod;
import javax.jws.WebParam;
import javax.jws.WebResult;
import javax.jws.WebService;


@WebService(name = "AdvertiseService", // 服务名称
        targetNamespace = "http://advertise.server.webservice.shizhongcai.com/"// 一般是接口的包名倒序
)
public interface AdvertiseService {

    /**
     * 广告上报
     * @param reqVo
     * @return
     */
    @WebMethod
    @WebResult(name = "String", targetNamespace = "")
    AdvertiseRspVo report(@WebParam(name = "AdvertiseReqVo") AdvertiseReqVo reqVo);

}

```

### 1.4第四步：创建webservice实现类
```java
package com.shizhongcai.webservice.server.advertise;

import com.shizhongcai.webservice.server.advertise.vo.AdvertiseReqVo;
import com.shizhongcai.webservice.server.advertise.vo.AdvertiseRspVo;
import org.springframework.stereotype.Component;

import javax.jws.WebService;

/**
 * @version 1.0
 * @Author shizhongcai
 * @date 2020-3-20 15:57
 */
@WebService(serviceName = "report", // 服务名称
        targetNamespace = "http://advertise.server.webservice.shizhongcai.com/", // 一般是接口的包名倒序
        endpointInterface = "com.shizhongcai.webservice.server.advertise.AdvertiseService"
)
@Component
public class AdvertiseServiceImpl implements AdvertiseService {

    @Override
    public AdvertiseRspVo report(AdvertiseReqVo reqVo) {
        String phone = reqVo.getPhone();
        return AdvertiseRspVo.success(phone);
    }

}
```

### 1.5请求实体
```java

import lombok.Data;

/**
 * @version 1.0
 * @Author shizhongcai
 * @date 2020-3-20 15:47
 */
@Data
public class AdvertiseReqVo {
    /**
     * 链接地址
     */
    private String url;
    /**
     * 微信广告上报clickId
     */
    private String clickId;
    /**
     * 用户手机号
     */
    private String phone;
    /**
     * 用来区分产品及来源
     * REQ624诚易贷线下版本:CYD_OFFLINE
     */
    private String appId;
    /**
     * API上报类型
     *   微信-WECHAT
     *   腾讯-TECENT
     *   头条-TOUTIAO
     */
    private String APIType;
    /**
     * 头条 7-页面访问
     * 头条 19-有效获客
     * 微信 REGISTER-注册
     * 微信 RESERVATION-预约
     */
    private String actionType;

}




/**
 * @version 1.0
 * @Author shizhongcai
 * @date 2020-3-20 15:44
 */
@Data
public class AdvertiseRspVo {
    /**
     * 手机号
     */
    private String phone;
    /**
     * 上报结果
     * 0-成功
     * 1-失败
     */
    private String tradeResult;

    public AdvertiseRspVo(String phone, String tradeResult) {
        this.phone = phone;
        this.tradeResult = tradeResult;
    }

    public static AdvertiseRspVo success(String phone) {
        return new AdvertiseRspVo(phone,"0");
    }
    public static AdvertiseRspVo fail(String phone) {
        return new AdvertiseRspVo(phone,"1");
    }
}

```

##2.调用

###2.1WSDL获取地址
GET
```
ip:port/服务根路径/services/report?wsdl
```
###2.1请求地址
POST
```
ip:port/服务根路径/services/report
```
###2.2请求参数
POST/rew-XML格式
```
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ser="http://advertise.server.webservice.shizhongcai.com/">
    <soapenv:Header/>
    <soapenv:Body>
        <ser:report>
        	<AdvertiseReqVo>
            	<phone>11246666622</phone>
            	<url>http://www.baidu.com</url>
            	<clickId>22222222</clickId>
            	<appId>CYD_OFFLINE</appId>
            	<APIType>WECHAT</APIType>
            	<actionType>REGISTER</actionType>
        	</AdvertiseReqVo>
        </ser:report>
    </soapenv:Body>
</soapenv:Envelope>
```
###2.3返回参数
```
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <ns2:reportResponse xmlns:ns2="http://advertise.server.webservice.shizhongcai.com/">
            <String>
                <phone>11246666622</phone>
                <tradeResult>0</tradeResult>
            </String>
        </ns2:reportResponse>
    </soap:Body>
</soap:Envelope>
```