---
title: "Java8相关"
date: 2019-09-11
tags: [Java8]
categories: [Java]
draft: true
---
**阅读Java8实战后的一些笔记**
<!--more-->
- [1. 行为参数化22](#1-行为参数化)
- [2. lamba表达式](#2-lamba表达式)
- [3. 构造函数引用](#3-构造函数引用)
- [4. 使用流](#4-使用流)
- [5. 设计模式使用](#5-设计模式使用)
- [6. 默认方法](#6-默认方法)
- [7. Optional](#7-Optional)
- [8. CompletableFuture:组合式异步编程](#8-CompletableFuture:组合式异步编程)
- [9. 新的日期和时间API](#9-新的日期和时间API)


## 1. 行为参数化
+ **定义一个接口，对选择建模**  
><font color="green">  
>
    public interface ApplePredicate{  
		boolean test(Apple apple);  
	}
</font>  

+ **定义一个类，实现该接口**
>
    public class AppleRedAndHeavyPredicate implements ApplePredicate{  
		public boolean test(Apple apple){  
			return "red".equals(apple.getColor())   
					&& apple.getWeight() > 150;   
		}  
	}  

+ **使用匿名内部类简化代码**
>    
    List<Apple> redApples = filterApples(apples,new ApplePredicate(){
		public boolean test(Apple a){
			return "red".equals(a.getColor());
		}
	});

+ **使用Lamba表达式简化**
>  
	List<Apple> redApples = filterApples(apples,a->"red".equals(a.getColor()));  

+ **使用泛型，抽象化代码**
>  
	public interface Predicate<T>{  
		boolean test(T t);  
	}
>
	public static <T> List<T> filter(List<T> list,Predicate<T> p){
		List<T> result = new ArrayList<>();
		for(T e:list){
			if(p.test(e)){
				result.add(e);
			}
		}
		return result;
	}

---
## 2. lamba表达式
+ **函数式接口**  

<table><thead><tr><th>函数式接口</th><th>函数描述</th><th>原始类型特化</th></tr></thead><tbody><tr><td>Predicate< T></t></td><td>T->boolean</td><td>IntPredicate,<br>LongPredicate,<br>DoublePredicate</td></tr><tr><td>Consumer< T></t></td><td>T->void</td><td>IntConsumer,<br>LongConsumer,<br>DoubleConsumer</td></tr><tr><td>Function< T,R></td><td>T->R</td><td>IntFunction<  R>,<br>IntToDoubleFunction,<br>IntToLongFunction,<br>LongFunction< R>,<br>LongToDoubleFunction,<br>LongToIntFunction,<br>DoubleFunction< R>,<br>ToIntFunction< T>,<br>ToDoubleFunction< T>,<br>ToLongFunction< T></t></t></t></r></r></r></td></tr><tr><td>Supplier<t></t></td><td>()->T</td><td>BooleanSupplier,<br>IntSupplier,<br>LongSupplier,<br>DoubleSupplier</td></tr><tr><td>UnaryOperator<t></t></td><td>T->T</td><td>IntUnaryOperator,<br>LongUnaryOperator,<br>DoubleUnaryOperator</td></tr><tr><td>BinaryOperator<t></t></td><td>(T,T)->T</td><td>IntBinaryOperator,<br>LongBinaryOperator,<br>DoubleBinaryOperator</td></tr><tr><td>BiPredicate< L,R></td><td>(L,R)->boolean</td><td></td></tr><tr><td>BiConsumer< T,U></td><td>(T,U)->void</td><td>ObjIntConsumer< T>,<br>ObjLongConsumer< T>,<br>ObjDoubleConsumer< T></t></t></t></td></tr><tr><td>BiFunction< T,U,R></td><td>(T,U)->R</td><td>ToIntBiFunction< T,U>,<br>ToLongBiFunction< T,U>,<br>ToDoubleBiFunction< T,U></td></tr></tbody></table>

+ **Lambdas 及函数式接口的例子**

<table>
<thead>
<tr><th>使用案例</th><th>Lambda的例子</th><th>对应的函数式接口</th></tr>
</thead>
<tbody>
<tr><td>布尔表达式</td><td>(List<  string>list)->list.isEmpty()</string><td>Predicate<List<  string>></string></td></tr>

<tr><td>创建对象</td><td>()->new Apple(10)</td><td>Supplier<  apple></apple></td></tr>
<tr><td>消费一个对象</td><td>(Apple a)->System.out.println(a.getWeight())</td><td>Consumer<  apple></apple></td></tr>

<tr><td>从一个对象中选择/提取</td><td>(String s)->s.length()</td><td>Function< String,Integer>,<br>ToIntFunction<string></string></td></tr>

<tr><td>合并两个值</td><td>(int a,int b)->a*b</td><td>IntBinaryOperator</td></tr>

<tr><td>比较两个对象</td><td>(Apple a1,Apple a2)-><br>a1.getWeight().compareTo(a2.getWeight())</td><td>Comparator< apple>,<br>BiFunction< Apple,Apple,Integer>,<br>ToIntBiFunction< Apple,Apple></apple></td></tr>

</tbody>
</table>

+ **Lambda及其等效的方法引用**
<table>
<thead><tr><th>Lambda</th><th>等效的引用方法</th></thead>
<tbody>
<tr><td>(Apple a) -> a.getWeight()</td><td>Apple::getWeight</td></tr>
<tr><td>()->Thread.currentThread().dumpStack()</td><td>Thread.currentThread()::dumpStack</td></tr>
<tr><td>(str,i) -> str.substring(i)</td><td>String::substring</td></tr>
<tr><td>(String s)->System.out.println(s)</td><td>System.out::println</td></tr>
</tbody>
</table>
---

## 3.构造函数引用

+ **默认方法的构造函数引用**
>
	Supplier<Apple> appleSupplier = Apple::new;
	Supplier<Apple> appleSupplier = ()->new Apple();
	Apple a = appleSupplier.get();


+ **1个参数的构造函数引用**  
如果函数签名类似于Apple(Integer weight)   
>
	Function<Integer,Apple> appleFunction = Apple::new;  
	Function<Apple> appleFunction = (weight)->new Apple(weight);  
	Apple a = appleFunction.apply(10); 


+ **2个参数的构造函数引用**  
如果函数签名类似于Apple(Integer weight,String color)    
>
	BiFunction<Integer,String,Apple> appleBiFunction = Apple::new;  
	BiFunction<Integer,String,Apple> appleBiFunction = (weight,color)->new Apple  (weight,color);  
	Apple a = appleFunction.apply(10."red"); 
<font color = "red">  

+ **比较器复合**</font>
>
	Comparator<Apple> c = Comparator.comparing(Apple::getWeighjt);

	apples.sort((a,b)->a.getWeight().compareTo(b.getWeight()));
	可有化成（idea会自动帮我们优化）
    apples.sort(Comparator.comparing(Apple::getWeight));
<font color = "red">

+ **逆序**</font>
>
	Comparator<Apple> c = Comparator.comparing(Apple::getWeighjt)
									.reversed();
<font color = "red">

+ **比较链接**</font>  先按照重量排序，然后按照颜色排序
> 
	Comparator<Apple> c = Comparator.comparing(Apple::getWeighjt)
									.reversed()
									.thenComparing(Apple::getColor);
<font color = "red">

+ **谓词复合**</font> 

    谓词接口包含3个方法：negate,and,or
> 
	Predicate<Apple> redApple = (Apple a)->"red".equals(a.getColor());
	Predicate<Apple> heaveyApple = a->a.getWeight()>150;
	Predicate<Apple> notRedApple = redApple.negate();
	Predicate<Apple> redAndHeavyApple = redApple.and(heavyApple);
	
<font color = "red">

+ **函数复合**</font> 

    谓词接口包含3个方法：negate,and,or
> 
	Function<Integer,Integer> f = (a)->a*2;
	Function<Integer,Integer> g = (b)->b+2;
	f.andThen(g) == g(f(x))
	f.compose(g) == f(g(x))
用此种方法可以组合成流水线工作，最后得出结果

---

## 4. 使用流

+ **distinct()**  
返回不重复的元素
+ **limit(3)**  
截断流，只选择前3个
+ **skip(3)**  
跳过流，跳过前3个 
+ **map()** 映射
+ **flatMap()** 流扁平化，将各个生成的流扁平化为单个流  
例如存在  
List<String> a = ["1","2","3"];  
List<String> b = ["a","b"];
如何生成["1a","1b","2a","2b","3a","3b"]?
>	
+ A.  
	Stream<Stream<String>> result = a.stream().map(i->b.stream(j->return (i+j)));  
	Stream<String> s1 = ["1a","1b"]转换成的流;  
	Stream<String> s2 = ["2a","2b"]转换成的流;  
	Stream<String> s3 = ["3a","3b"]转换成的流;  
	对result进行收集会得到[["1a","1b"],["2a","2b"],["3a","3b"]]不符合预期  
>    
+ B.
	Stream<String> result = a.stream().flatMap(i->b.stream(j->return (i+j)));  
	对result进行收集会得到["1a","1b","2a","2b","3a","3b"]符合预期

+ **anyMatch()**  
流中是否存在一个元素能够匹配  返回一个boolean值
+ **allMatch()**  
流中是否所有元素都能够匹配  返回一个boolean值
+ **nonMatch()**  
流中是否所有元素都不能匹配  返回一个boolean值
+ **findAny()**  
流中只要找到一个匹配的元素就短路并返回一个Optional<T>值,如果不关心顺序，请用findAny
+ **findFirst()**  
顺序流中只要找到一个匹配的元素就短路并返回一个Optional<T>值，关心顺序时使用
+ **isPresent()**  
Optional<T>中包含值返回true,否则返回false
+ **ifPresent(Consumer<T> block)**  
会在值存在时执行消费操作代码块
+ **T orElse(T other)**  
会在值存在时返回值，否则返回默认值
+ **reduce()**
int sum = list.stream().reduce(0,(a,b)->(a+b));初始为0的元素求和  
int sum = list.stream().reduce(0,Integer::sum);  
int multi = list.stream().reduce(1,(a,b)->a*b);初始为1的元素求积  
Optional<Integer> multi = list.stream().reduce((a,b)->a*b);没有初始值的的元素求积将返回optional  
+ **数值范围 range(),rangeClosed()**  
IntStream.range(1,100) = [1,100}  
IntStream.rangeClosed(1,100) = [1,100]  
+ **用Iterate创建无限流** 
例如：Stream.iterate(0,n->n+2).limit(5)  
+ **用generate创建无限流** 
generate接受一个Supplier<T> 提供新的值  
例如：Stream.generate(Math::random).limit(5);  
### 3.1 流收集数据
+ **总量**
>
	Stream.of("1"."2").count();  
	或者
	Stream.of("1"."2").collect(Collectors.counting()); 

+ **MAX 和MIN**
最大的苹果： 
>
	Comparator<Apple> appleComparator = Comparator.comparingInt(Apple::getWeight);
    Optional<Apple> maxWeightApple = apples.stream().collect(Collectors.maxBy(appleComparator));
最小值用**Collectors.minBy()**  
Int求和用**Collectors.summarizingInt()**  
Double求和用**Collectors.summarizingDouble()**  
Long求和用**Collectors.summarizingLong()**

+ **规约reduce()**  
>
	apples.stream().collect(Collectors.reducing(0,Apple::getWeight,Integer::sum));  
	apples.stream().coll ect(Collectors.reducing(0,Apple::getWeight,(i,j)->(i+j)));  
	这两条代码是等价的
	0 <--初始值   
	Apple::getWeight <--转换函数  
	(i,j)->(i+j) <--BinaryOperator  

+ **分组groupingBy()**  
  	**多级分组**  
>
	apples.stream().collect(Collectors.groupingBy(Apple::getWeight, Collectors.groupingBy(Apple::getColor)));

+ **分区partitioningBy()**    
分区是分组的特殊情况,会返回Map<Boolean,List<T>>类型，true是以组，false是一组  

+ **Collectors类的静态方法**  
  <table> 
   <thead> 
    <tr> 
     <th>工厂方法</th> 
     <th>返回类型</th> 
     <th>用于</th> 
     <th>示例</th> 
    </tr> 
   </thead> 
   <tbody> 
    <tr> 
     <td>toList</td> 
     <td><code>List&lt;T&gt;</code></td> 
     <td>把流中所有项目收集到一个List</td> 
     <td><code>List&lt;Dish&gt; dishes = menuStream. collect(toList());</code></td> 
    </tr> 
    <tr> 
     <td>toSet</td> 
     <td><code>Set&lt;T&gt;</code></td> 
     <td>把流中所有项目收集到一个Set,删除重复项</td> 
     <td><code>Set&lt;Dish&gt; dishes = menuStream. collect (toSet());</code></td> 
    </tr> 
    <tr> 
     <td>toCollection</td> 
     <td><code>Collection&lt;T&gt;</code></td> 
     <td>把流中所有项目收集到给定的供应源创建的集合</td> 
     <td><code>Collection&lt;Dish&gt; dishes = menuStream.collect (toCollection() ,ArrayList::new);</code></td> 
    </tr> 
    <tr> 
     <td>counting</td> 
     <td><code>Long</code></td> 
     <td>计算流中元素的个数</td> 
     <td><code>long howManyDishes = menuStream.collect (counting());</code></td> 
    </tr> 
    <tr> 
     <td>summingInt</td> 
     <td><code>Integer</code></td> 
     <td>对流中项目的一个整数属性求和</td> 
     <td><code>int totalCalories =menuStream.collect(summingInt (Dish::getCalories) );</code></td> 
    </tr> 
    <tr> 
     <td>averagingInt</td> 
     <td><code>Double</code></td> 
     <td>计算流中项目Integer属性的平均值</td> 
     <td><code>double avgCalories =menuStream.collect(averagingInt(Dish::getCalories));</code></td> 
    </tr> 
    <tr> 
     <td>summarizingInt</td> 
     <td><code>IntSummaryStatistics</code></td> 
     <td>收集美于流中項目Integer 属性的統竍値，例如最大、最小、总和与平均値</td> 
     <td><code>IntSummaryStatistics menuStatistics =menuStream.collect(summarizingInt(Dish::getCalories));</code></td> 
    </tr> 
    <tr> 
     <td>joining</td> 
     <td><code>String</code></td> 
     <td>连接对流中毎个項目调用toString方法所生成的字符串</td> 
     <td><code>String shortMenu =menuStream.map(Dish::getName).collect (joining(&quot;, &quot;));</code></td> 
    </tr> 
    <tr> 
     <td>maxBy</td> 
     <td><code>Optional&lt;T&gt;</code></td> 
     <td>一个包裹了流中按照给定比较器选出的最大元素的Optional,或如果流为空则为optional.empty()</td> 
     <td><code>Optional&lt;Dish&gt; fattest = menuStream.collect(maxBy(comparingInt(Dish::getCalories)));</code></td> 
    </tr> 
    <tr> 
     <td>minBy</td> 
     <td><code>Optional&lt;T&gt;</code></td> 
     <td>一个包裹了流中按照给定比较器选出的最小元素的Optional,或如果流为空则为optional.empty()</td> 
     <td><code>Optional&lt;Dish&gt; lightest = menuStream.collect(minBy(comparingInt(Dish::getCalories)));</code></td> 
    </tr> 
    <tr> 
     <td>reducing</td> 
     <td>归约操作产生的类型</td> 
     <td>从一个作为累加器的初始値幵始,利用BinaryOperator与流中的元素逐个结合，从而将流归约为单个值</td> 
     <td><code>int totalCalories =menuStream.collect(reducing(0,Dish::getCalories, Integer::sum));</code></td> 
    </tr> 
    <tr> 
     <td>collectingAndThen</td> 
     <td>转换函数的类型</td> 
     <td>包裹另一个收集器，对其结果应用转换函数</td> 
     <td><code>int howManyDishes =menuStream.collect(collectingAndThen(toList(), List::size));</code></td> 
    </tr> 
    <tr> 
     <td>groupingBy</td> 
     <td><code>Map&lt;K, List&lt;T&gt;&gt;</code></td> 
     <td>根据项目的一个属性的值对流中的项目作问组，并将属性值作为结果Map的键</td> 
     <td><code>Map&lt;Dish.Type,List&lt;Dish&gt;&gt; dishesByType = menuStream.collect(groupingBy(Dish::getType));</code></td> 
    </tr> 
    <tr> 
     <td>mapping</td> 
     <td></td> 
     <td>是一个收集器，可以传入两个函数， 一个函数对流中的元素做变换，另一个则将变换的结果对象收集起来 ，目的是在累加之前对每个元素应用一个映射函数</td> 
     <td><code>Map&lt;Dish.Type, Set&lt;CaloricLevel&gt;&gt; caloricLevelsByType =menu.stream().collect(groupingBy(Dish::getType, mapping(dish -&gt; { if (dish.getCalories() &lt;=400) return CaloricLevel.DIET;else if (dish.getCalories() &lt;= 700) return CaloricLevel.NORMAL;else return CaloricLevel.FAT; },toSet() )));</code></td> 
    </tr> 
    <tr> 
     <td>partitioningBy</td> 
     <td><code>Map&lt;Boolean, List&lt;T&gt; &gt;</code></td> 
     <td>相据对流由每个项目应田谓词的结里求对项目经行分区</td> 
     <td><code>Map&lt;Boolean, List&lt;Dish&gt;&gt; vegetarianDishes =menuStream. collect (partitioningBy (Dish::isVegetarian) ) ;</code></td> 
    </tr> 
   </tbody> 
  </table>

# 待办事项
- [x] 自定义收集器


### 4.3 并行数据处理与性能
prallelStream()   
parallel()将顺序流变为并行流  
sequential()将并行流变为顺序流  
自动装箱拆箱会大大降低并行流性能，此时应使用IntStream等原始类型流来避免这种操作。

## 5. 设计模式使用
### 使用lambda重构设计模式使用
#### 4.1策略模式
#### 待办事项
- [x] 各种模式

---

## 6. 默认方法

---

## 6. Optional
---
### optional方法


| 序号 | 方法 & 描述                                            |
|----|----------------------------------------------------|
| 1  | **static <T> Optional<T> empty();**    返回空的 Optional 实例。 |
| 2  | **boolean equals(Object obj)**      判断其他对象是否等于 Optional。|
| 3  | **Optional<T> filter(Predicate<? super <T> predicate)**  如果值存在，并且这个值匹配给定的 predicate，返回一个Optional用以描述这个值，否则返回一个空的Optional。|
| 4  |**<U> Optional<U> flatMap(Function<? super T,Optional<U>> mapper)**如果值存在，返回基于Optional包含的映射方法的值，否则返回一个空的Optional|
| 5  | **T get()**  如果在这个Optional中包含这个值，返回值，否则抛出异常：NoSuchElementException|
| 6  | **int hashCode()**  返回存在值的哈希码，如果值不存在 返回 0。|
| 7  | **void ifPresent(Consumer<? super T> consumer)**  如果值存在则使用该值调用 consumer , 否则不做任何事情。|
| 8  | **boolean isPresent()**  如果值存在则方法会返回true，否则返回 false。|
| 9  | **<U>Optional<U> map(Function<? super T,? extends U> mapper)**  如果有值，则对其执行调用映射函数得到返回值。如果返回值不为 null，则创建包含映射返回值的Optional作为map方法返回值，否则返回空Optional。|
| 19  | **static <T> Optional<T> of(T value)**  返回一个指定非null值的Optional。|
| 11 | **static <T> Optional<T> ofNullable(T value)**  如果为非空，返回 Optional 描述的指定值，否则返回空的 Optional。|
| 12 | **T orElse(T other)**  如果存在该值，返回值， 否则返回 other。|
| 13 | **T orElseGet(Supplier<? extends T> other)**  如果存在该值，返回值， 否则触发 other，并返回 other 调用的结果。|
| 14 | **<X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier)**  如果存在该值，返回包含的值，否则抛出由 Supplier 继承的异常|
| 15 | **String toString()**  返回一个Optional的非空字符串，用来调试|

---

## 8. CompletableFuture:组合式异步编程

---

## 9. 新的日期和时间API
### 9.1 LocalDate
### 9.2 LocalDateTime
### 9.3 Instant
### 9.4 Duration
### 9.5 Period
---