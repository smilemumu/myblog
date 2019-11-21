---
title: "JAVA基础"
date: 2019-11-05
tags: [基础]
categories: [java]
draft: true
---

# Java基础知识

### 1. String 、StringBuilder 、StringBuffer
String 类中使用 final 关键字修饰字符数组来保存字符串，private　final　char　value[]，所以 String 对象是不可变的。
每次对 String 类型进行改变的时候，都会生成一个新的 String 对象，然后将指针指向新的 String 对象。StringBuffer 每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。
对于三者使用的总结：

    操作少量的数据: 适用String
    单线程操作字符串缓冲区下操作大量数据: 适用StringBuilder
    多线程操作字符串缓冲区下操作大量数据: 适用StringBuffer

### 2.== 和 equals

== : 它的作用是判断两个对象的地址是不是相等。即，判断两个对象是不是同一个对象(基本数据类型==比较的是值，引用数据类型==比较的是内存地址)。

equals() : 它的作用也是判断两个对象是否相等。但它一般有两种使用情况：

-	情况1：类没有覆盖 equals() 方法。则通过 equals() 比较该类的两个对象时，等价于通过“==”比较这两个对象。
-	情况2：类覆盖了 equals() 方法。一般，我们都覆盖 equals() 方法来比较两个对象的内容是否相等；若它们的内容相等，则返回 true (即，认为这两个对象相等)。

说明：
-	String 中的 equals 方法是被重写过的，因为 object 的 equals 方法是比较的对象的内存地址，而 String 的 equals 方法比较的是对象的值。
-	当创建 String 类型的对象时，虚拟机会在常量池中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个 String 对象。

### 3.hashCode 和 equals
？？为什么重写equals时必须重写hashCode方法？？
hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode() 函数。

散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以快速找到所需要的对象）

我们先以“HashSet 如何检查重复”为例子来说明为什么要有 hashCode： 当你把对象加入 HashSet 时，HashSet 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashcode 值作比较，如果没有相符的hashcode，HashSet会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用 equals（）方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。（摘自我的Java启蒙书《Head first java》第二版）。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。

hashCode() 的作用就是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。**hashCode() 在散列表中才有用，在其它情况下没用。**在散列表中hashCode() 的作用是获取对象的散列码，进而确定该对象在散列表中的位置。


-  如果两个对象相等，则hashcode一定也是相同的
-  两个对象相等,对两个对象分别调用equals方法都返回true
-  两个对象有相同的hashcode值，它们也不一定是相等的
-  因此，equals 方法被覆盖过，则 hashCode 方法也必须被覆盖
-  hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

### 4.线程、程序、进程
线程与进程相似，但线程是一个比进程更小的执行单位。一个进程在其执行的过程中可以产生多个线程。
进程是程序的一次执行过程。
程序是含有指令和数据的文件，被存储在磁盘或其他的数据存储设备中，也就是说程序是静态的代码。

### 5.线程有哪些基本状态？

线程创建之后它将处于 NEW（新建） 状态，调用 start() 方法后开始运行，线程这时候处于 READY（可运行） 状态。可运行状态的线程获得了 cpu 时间片（timeslice）后就处于 RUNNING（运行） 状态。

当线程执行 wait()方法之后，线程进入 **WAITING（等待）**状态。进入等待状态的线程需要依靠其他线程的通知才能够返回到运行状态，而 TIME_WAITING(超时等待) 状态相当于在等待状态的基础上增加了超时限制，比如通过 sleep（long millis）方法或 wait（long millis）方法可以将 Java 线程置于 TIMED WAITING 状态。当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态。当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到 BLOCKED（阻塞） 状态。线程在执行 Runnable 的run()方法之后将会进入到 TERMINATED（终止） 状态。

### 6.final 关键字
final关键字主要用在3个地方：变量、方法、类。
-	1.final修饰基础类型变量，其数值一旦在初始化之后便不能更改。如果修饰的是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。
-	2.final修饰类，表明这个类不能被继承。
-	3.final修饰方法时将方法锁定，以防任何继承类修改它的含义；

### 7.static关键字
-	1.修饰成员变量和成员方法: 被 static 修饰的成员属于类，不属于单个这个类的某个对象，被类中所有对象共享。
-	2.静态代码块:  静态代码块定义在类中方法外, 静态代码块在非静态代码块之前执行(静态代码块—>非静态代码块—>构造方法)。 该类不管创建多少对象，静态代码块只执行一次.
-	3.静态内部类（static修饰类的话只能修饰内部类）： 静态内部类与非静态内部类之间存在一个最大的区别: 非静态内部类在编译完成之后会隐含地保存着一个引用，该引用是指向创建它的外围类，但是静态内部类却没有。没有这个引用就意味着：1. 它的创建是不需要依赖外围类的创建。2. 它不能使用任何外围类的非static成员变量和方法。(应用---静态内部类单例模式,参考单例模式 [ ])
-	4.静态导包(用来导入类中的静态资源，1.5之后的新特性): 格式为：import static 这两个关键字连用可以指定导入某个类中的指定静态资源，并且不需要使用类名调用类中静态成员，可以直接使用类中静态成员变量和成员方法。

### 8.this关键字
-	1.代表当前对象
```java
         public int getAge(）{
            return age;   
         }
         public void show(){
            System.out.println(this.getAge()); //这里this可以省略不写
         }

```
-	2.区分局部变量和成员变量
```java
当局部变量和成员变量在同一个作用域使用并且同名时
例如构造器中的赋值：this.name = name;

```
-	3.调用本类构造器

```java
	Student（String name,String age）{
		this.name = name;
		this.age = age;
	}
	Student（String name,String age,String score）{
		this(name,age);
		this.score = score;
	}
	
```

### 9.super关键字
super 关键字与 this 类似，this 用来表示当前类的实例，super 用来表示父类
-	1.super可用于访问父类被子类隐藏或着覆盖的方法和属性，使用形式为super.方法（属性）
-	2.在类的继承中，子类的构造方法中默认会有super()语句存在(默认隐藏)，相当于执行父类的相应构造方法中的语句，若显式使用则必须位于类的第一行 (super和this不能同时出现在同一个构造函数中调用其他的构造函数，因为两个语句都要是第一个语句)
-	3.对于父类有参的构造方法，super不能省略，否则无法访问父类的有参构造方法，使用形式为super（xx,xx...）

### 10.Collections 工具类和 Arrays 工具类常见方法
-	10.1排序操作
```java
void reverse(List list)//反转
void shuffle(List list)//随机排序
void sort(List list)//按自然排序的升序排序
void sort(List list, Comparator c)//定制排序，由Comparator控制排序逻辑
void swap(List list, int i , int j)//交换两个索引位置的元素
void rotate(List list, int distance)//旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面。
```
-	10.2查找、替换操作
```java
int binarySearch(List list, Object key)//对List进行二分查找，返回索引，注意List必须是有序的
int max(Collection coll)//根据元素的自然顺序，返回最大的元素。 类比int min(Collection coll)
int max(Collection coll, Comparator c)//根据定制排序，返回最大元素，排序规则由Comparatator类控制。类比int min(Collection coll, Comparator c)
void fill(List list, Object obj)//用指定的元素代替指定list中的所有元素。
int frequency(Collection c, Object o)//统计元素出现次数
int indexOfSubList(List list, List target)//统计target在list中第一次出现的索引，找不到则返回-1，类比int lastIndexOfSubList(List source, list target).
boolean replaceAll(List list, Object oldVal, Object newVal), 用新元素替换旧元素
```
-	10.3不可变集合
```java
emptyXxx(): 返回一个空的、不可变的集合对象，此处的集合既可以是List，也可以是Set，还可以是Map。
singletonXxx(): 返回一个只包含指定对象（只有一个或一个元素）的不可变的集合对象，此处的集合可以是：List，Set，Map。
unmodifiableXxx(): 返回指定集合对象的不可变视图，此处的集合可以是：List，Set，Map。
上面三类方法的参数是原有的集合对象，返回值是该集合的”只读“版本。
```

### 11.Arrays类的常见操作

-    排序 : sort()
-    查找 : binarySearch()
-    比较: equals()
-    填充 : fill()
-    转列表: asList()
-    转字符串 : toString()
-    复制: copyOf()
