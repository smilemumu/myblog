---
title: "Java8计算时间差"
date: 2019-09-23
tags: [Java8]
categories: [Java]
draft: true
featured: true 
---




**计算某个并行任务的总耗时。**

<!--more-->
## 计算时间差的方式
### 1.Period类

主要是Period类方法getYears（），getMonths（）和getDays（）来计算.能够计算出相差的年月日周。


```java
import java.time.*;

public class Test {
    public static void main(String[] args) {
        LocalDate today = LocalDate.now();
        System.out.println("今天 : " + today);
        LocalDate birthDate = LocalDate.of(1994, Month.MAY, 19);
        System.out.println("生日 : " + birthDate);
        Period p = Period.between(birthDate, today);
        System.out.printf("年龄 : %d 年 %d 月 %d 日", p.getYears(), p.getMonths(), p.getDays());
    }
}

 /**
     * 计算两个时间段内的相差时分秒
     * @param createTime
     * @return
     */
    private String getLeftTimeByCreateTime(Date createTime) {
        LocalDateTime deadLine = LocalDateTimeUtils.date2LocalDateTime(createTime).plusDays(3L);
        Duration duration = Duration.between(LocalDateTime.now(),deadLine);
        if(duration.getSeconds()<=0){
            return "00:00:00";
        }else{
            Long hour = duration.getSeconds() / ChronoUnit.HOURS.getDuration().getSeconds();
            Long minute = (duration.getSeconds() - ChronoUnit.HOURS.getDuration().getSeconds() * hour) / ChronoUnit.MINUTES.getDuration().getSeconds();
            Long second = (duration.getSeconds() - ChronoUnit.HOURS.getDuration().getSeconds() * hour) - minute * ChronoUnit.MINUTES.getDuration().getSeconds();
            return hour+":"+minute+":"+second;
        }
    }
```
输出:  
今天 : 2019-09-23  
生日 : 1994-05-19  
年龄 : 25 年 4 月 4 日  
### 2.Duration类

提供了使用基于时间的值（如秒，毫秒秒）测量时间量的方法。

```java
import java.time.*;

public class Test {

    public static void main(String[] args) {
        logger.info("开始时间:{}", LocalDateTime.now().format(LocalDateTimeUtils.YYYY_MM_DD_HH_MM_SS));
        Instant start = Instant.now();
        //具体的耗时处理逻辑，此处用Thread.sleep(1000)来代替
        Thread.sleep(1000);
        Instant end = Instant.now();
        logger.info("结束时间-{}", LocalDateTime.now().format(LocalDateTimeUtils.YYYY_MM_DD_HH_MM_SS));
        logger.info("耗时:{}毫秒", Duration.between(start,end).toMillis());
    }
}
```

输出:  

开始时间:2019-09-23 18:00:00  
结束时间:2019-09-23 18:00:01  
耗时:1000毫秒  

  
### 总结：
Period适合计算日期差，Instant适合计算时间差，尤其是计算耗时模块的耗时时间。如果有需要的话，可以按情况自行选择。