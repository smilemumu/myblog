---
title: "JAVA IO流"
date: 2019-11-07
tags: [基础]
categories: [java]
draft: true
---

# Java IO流

## Java 中 IO 流分为几种?

-    按照流的流向分，可以分为输入流和输出流；
	-	输出流：从内存读出到文件。只能进行写操作。
	-	输入流：从文件读入到内存。只能进行读操作。
	-	输入和输出，都是相对于系统内存而言
-    按照操作单元划分，可以划分为字节流和字符流；
	-    字节流：以字节为单位，每次次读入或读出是8位数据。可以读任何类型数据。(1byte（B） = 8bit(位))
	-    字符流：以字符为单位，每次次读入或读出是16位数据。其只能读取字符类型数据。(1字符=2字节 )
-    按照流的角色划分为节点流和处理流。
	-    节点流：直接与数据源相连，读入或读出。
	-    处理流（Filter Stream）：与节点流一块使用，在节点流的基础上，再套接一层，套接在节点流上的就是处理流。（DataInputStream、BufferedInputStream、BufferedReader等）

Java Io流的40多个类都是从如下4个抽象类基类中派生出来的。
-	InputStream/Reader: 所有的输入流的基类，前者是字节输入流，后者是字符输入流。
-	OutputStream/Writer: 所有输出流的基类，前者是字节输出流，后者是字符输出流。



## 不同平台的换行符号：

|符号|linux|windows|Java建议写法|
|-|-|-|-|
|换行符|\n|\r\n|System.getProperty("line.separator")|
|路径分隔符|/|\\\|File.separator|
|多个路径分隔符|:|;|File.pathSeparator|


## 字节流和字符流的区别:
字符流使用了缓存区（内存），执行了write()，文件中不会立刻有内容（除非缓冲区满了或者主动刷新缓冲区），需要等输出流对象关闭了，文件中才会有内容；字节流不使用缓冲区，执行了wirite()，文件中立刻就有内容了。
-	### 1.字符流
```java
//步骤为:程序->字符流->缓存(数据先存放到缓存，再从缓存写入文件【主动刷新or流关闭】)->文件
import java.util.*;
import java.io.*;
public class WriterTest {
    public static void main(String[] args) {
        File file = new File("1.txt");
        Scanner in = null;
        Writer out = null;
        try{
            in = new Scanner(System.in);
            out = new FileWriter(file);
            String data = null;
            while(in.hasNext()){
                data = in.nextLine();
                if(data == null){
                    data = "";
                }
                out.write(data);
                //out.flush();刷新缓冲区，会立刻将缓冲区的内容输出
            }
            
        }catch(Exception e) {
            e.printStackTrace();
        }
        finally{
            try{
				//最后要将流关闭
                in.close();
                out.close();
            }catch(Exception e){
                e.printStackTrace();
            }
        }
    }
}
```


-	### 2.字节流
```java
import java.util.*;
import java.io.*;
public class OutputStreamTest {
    public static void main(String[] args) {
        File file = new File("2.txt");
        Scanner scannerIn = null;
        FileOutputStream out = null;
        try{
            scannerIn = new Scanner(System.in);
            //覆盖式，追加式多一个true参数
            out = new FileOutputStream(file);
            String data = null;
            byte[] bytes = null;
            while(in.hasNext()){
                data = scannerIn.nextLine();
                if(data != null){
                    bytes = data.getBytes();
                }else{
                    bytes = "".getBytes();
                }
                out.write(bytes);
            }
        }catch(Exception e) {
            e.printStackTrace();
        }finally{
            try{
                in.close();
                out.close();
            }catch(Exception e){
                e.printStackTrace();
            }
        }
    }
}
```
字节数组与字符串之间的转换
public String(byte[] bytes, String charsetName)
byte[] getBytes(String charsetName)


## 详细分类

### 按操作方式分类
1.输入字节流InputStream
-	ByteArrayInputStream、StringBufferInputStream、FileInputStream 是三种基本的介质流，它们分别从Byte 数组、StringBuffer、和本地文件中读取数据。
-	PipedInputStream 是从与其它线程共用的管道中读取数据。PipedInputStream的一个实例要和PipedOutputStream的一个实例共同使用，共同完成管道的读取写入操作。主要用于线程操作。
-	DataInputStream： 读取基础数据类型
-	ObjectInputStream 和所有 FilterInputStream 的子类都是装饰流（装饰器模式的主角）。

2.输出字节流OutputStream：
-	ByteArrayOutputStream、FileOutputStream： 是两种基本的介质流，它们分别向- Byte 数组、和本地文件中写入数据。
-	PipedOutputStream 是向与其它线程共用的管道中写入数据。
-	DataOutputStream 将基础数据类型写入到文件中
-	ObjectOutputStream 和所有 FilterOutputStream 的子类都是装饰流。

3.字符输入流Reader：
-	FileReader、CharReader、StringReader 是三种基本的介质流，它们分在本地文件、Char 数组、String中读取数据。
-	PipedReader：是从与其它线程共用的管道中读取数据
-	BufferedReader ：加缓冲功能，避免频繁读写硬盘
-	InputStreamReader： 是一个连接字节流和字符流的桥梁，它将字节流转变为字符流。

4.字符输出流Writer：
-	StringWriter:向String 中写入数据。
-	CharArrayWriter：实现一个可用作字符输入流的字符缓冲区
-	PipedWriter:是向与其它线程共用的管道中写入数据
-	BufferedWriter ： 增加缓冲功能，避免频繁读写硬盘。
-	PrintWriter 和PrintStream 将对象的格式表示打印到文本输出流。 极其类似，功能和使用也非常相似
-	OutputStreamWriter： 是OutputStream 到Writer 转换的桥梁，它的子类FileWriter 其实就是一个实现此功能的具体类（具体可以研究一SourceCode）。功能和使用和OutputStream 极其类似。


### 按操作对象分类
1.对文件进行操作（节点流）
-	FileInputStream（字节输入流）
-	FileOutputStream（字节输出流）
-	FileReader（字符输入流）
-	FileWriter（字符输出流）

2.对管道进行操作（节点流）
-	PipedInputStream（字节输入流）
-	PipedOutStream（字节输出流）
-	PipedReader（字符输入流）
-	PipedWriter（字符输出流）
-	PipedInputStream的一个实例要和PipedOutputStream的一个实例共同使用，共同完成管道的读取写入操作。主要用于线程操作。

3.字节/字符数组流（节点流）：
-	ByteArrayInputStream
-	ByteArrayOutputStream
-	CharArrayReader
-	CharArrayWriter

**上述三种为节点流，其余都为处理流**

4.Buffered缓冲流（处理流）：带缓冲区的处理流，缓冲区的作用的主要目的是：避免每次和硬盘打交道，提高数据访问的效率。
-	BufferedInputStream
-	BufferedOutputStream
-	BufferedReader
-	BufferedWriter

5.转化流（处理流）：
-	InputStreamReader：把字节转化成字符
-	OutputStreamWriter：把字节转化成字符

6.基本类型数据流（处理流）：用于操作基本数据类型值。
因为平时若是我们输出一个8个字节的long类型或4个字节的float类型，那怎么办呢？可以一个字节一个字节输出，也可以把转换成字符串输出，但是这样转换费时间，若是直接输出该多好啊，因此这个数据流就解决了我们输出数据类型的困难。数据流可以直接输出float类型或long类型，提高了数据读写的效率。
-	DataInputStream
-	DataOutputStream

7.其他
DataInputStream 有些特殊的方法如readInt(), readDouble()和readLine() 等可以读取一个 int, double和一个string一次性的
BufferedInputStream 增加性能
RandomAccessFile既可以读文件，也可以写文件
管道流共有四种， PipedInputStream, PipedOutputStream, PipedReader 和 PipedWriter.在多个线程或进程中传递数据的时候管道流非常有用。
File类不属于 IO流，也不是用于文件操作的，它主要用于知道一个文件的属性，读写权限，大小等信息。注意：Java7中文件IO发生了很大的变化，专门引入了很多新的类来取代原来的基于java.io.File的文件IO操作方式。



参考: <https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247484945&amp;idx=1&amp;sn=229be49807e3c2a9621f42c0a6c0aeb6&source=41#wechat_redirect>
参考: <https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247484946&amp;idx=1&amp;sn=043b054de3aef29bf3ff80eea15c16fd&source=41#wechat_redirect>