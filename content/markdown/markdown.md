---
title: "markdown模板"
date: 2019-09-11
tags: [markdown]
categories: [markdown]
draft: true
---

**markdown模板**
<!--more-->
#markdown demo
# 标题DEMO
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题

---
# 段落DEMO
这是段落的DEMO  
这是段落的DEMO
  
---
# 区块引用DEMO
> 区块引用
> > 嵌套引用
> > >三嵌套引用
> > > > 四嵌套引用

---
# 代码块DEMO
```java
    public static void main(){
	    system.out.println(123);
    }
```

```javascript
$(document).ready(function () {
    alert('RUNOOB');
});
```

## 四个空格或者TAB
<font color="red">

    import com.sinorock.common.argument.AesArgumentResolver;
    import org.springframework.context.annotation.Bean;
	import org.springframework.context.annotation.Configuration;
	import org.springframework.web.method.support.HandlerMethodArgumentResolver;
	import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

	import java.util.List;

	/**
 	* 自定义注解 AesJson配置类
 	*/
	@Configuration
	public class ArgumentResolverConfig implements WebMvcConfigurer {
    	@Override
    	public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers)	{
        	argumentResolvers.add(aesArgumentResolver());
    	}
    	@Bean
    	public AesArgumentResolver aesArgumentResolver(){
    	    return new AesArgumentResolver();
    	}
	}
</font>
---

# 斜体 加粗DEMO
*斜体* ，_斜体_  
**加粗**，__粗体__  

----
# 无序列表DEMO
- 第一项
 - fd
     - dd
+   第二项
-   第三项
+   第四项
-   第五项
-   第六项

---
# 有序列表DEMO
点之后有空格

1. 第一项
2. 第二项
3. 第三项
4. 第四项
5. 第五项
6. 第六项

---
# 链接DEMO
## 1. 行内式
[GitHub](http://github.com)

自动生成连接  <http://www.github.com/>
## 2.参考式
[GitHub][1]
[1]:http://github.com
自动生成连接  <http://www.github.com/>

---
#图片DEMO
![皇室战争 龙宝宝 图标](https://cdn.statsroyale.com/images/cards/full/baby_dragon.png "皇室战争 龙宝宝")

---

# 反斜杠 ''DEMO
相当于转义字符 \{\}

---
# 表格DEMO
**-:**  设置内容和标题栏居右对齐。  
**:-**  设置内容和标题栏居左对齐。  
**:-:** 设置内容和标题栏居中对齐。

 
|name |价格|数量|
|-|-|-|
|香蕉 | $1 | 5 |
|苹果 | $1 | 6 |
|草莓 | $1 | 7 |

---

| 标题1 | 标题2   | 长长的标题3 | title 4 |
| ----- | --------- | ----------- | ------- |
| 内容1 | content 2 | 1           |         |
| 行3  | line3     | column 3    |         |

---
# 待办事项
- [ ] 未完成事项

- [x] 已完成事项