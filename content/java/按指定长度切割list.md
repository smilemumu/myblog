---
title: "按指定长度切割list"
date: 2019-09-11
tags: [Java,guava]
categories: [Java]
draft: true
featured: true 
---

**某一次需求开发中，遇到这样的场景，需要对名单进行分组然后再对这批名单处理。**

<!--more-->
##计算总页数共有2种方法  

总记录数：  totalRecord  
每页记录数：pageSize  
**方法1**
```
totalPage = totalRecord % pageSize== 0 ? totalRecord / pageSize: totalRecord / pageSize+ 1; 
``` 
**方法2**(推荐)
```
totalPage = (totalRecord + pageSize-1) / pageSize;
```

## 1.使用Guava的Lists.partition方法  
```java
import com.google.common.collect.Lists;
private List<List<String>> splitList(List<String> list , int groupSize){
return  Lists.partition(list, groupSize);
}
```

## 2.手动实现  
```java
private List<List<String>> splitList(List<String> list , int groupSize){
    int length = list.size();
    // 计算可以分成多少组
    int num = ( length + groupSize - 1 )/groupSize ; // TODO 
    List<List<String>> newList = new ArrayList<>(num);
    for (int i = 0; i < num; i++) {
        // 开始位置
        int fromIndex = i * groupSize;
        // 结束位置
        int toIndex = (i+1) * groupSize < length ? ( i+1 ) * groupSize : length ;
        newList.add(list.subList(fromIndex,toIndex)) ;
    }
    return  newList ;
}
```
## 3.使用Java8的流来实现  
```java
private static List<List<String>> splitList(List<String> list , int groupSize){
    int length = list.size();
    // 计算可以分成多少组
    int num = ( length + groupSize - 1 )/groupSize ;
    List<List<String>> newList = Stream.iterate(0, n->n+1).limit(num).parallel().map(e->{
        return list.stream().skip(e*groupSize)
							.limit(groupSize)
							.collect(Collectors.toList());
    }).collect(Collectors.toList());
    return  newList ;
}
```

