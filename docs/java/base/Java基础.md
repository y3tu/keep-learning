[TOC]

#### 面向对象的特征

* **封装**：封装是面向对象的特征之一，是对象和类概念的主要特性。把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的信息进行隐藏。

* **继承**：指使用现有类的所有功能，并在无需重新编写原来的类的情况下对功能进行扩展。

* **多态**：允许将子类类型的指针赋值给父类类型的指针。

  

#### Java语言的特点

* 简单易学（Java语言的语法与C语言和C++语言很接近）

* 面向对象（封装，继承，多态）

* 平台无关性（Java虚拟机实现平台无关性）

* 支持网络编程并且很方便（Java语言诞生本身就是为简化网络编程设计的）

* 支持多线程（多线程机制使应用程序在同一时间并行执行多项任）

* 健壮性（Java语言的强类型机制、异常处理、垃圾的自动收集等）

* 安全性



#### `JDK`、`JRE`、`JVM`

**JRE**：Java Runtime Environment, Java运行时环境。包含两部分内容：Jvm和标准实现和Java的一些基本类库。它相对于Jvm来说，多出来的是一部分Java类库。

**JDK**:  Java Development Kit, Java开发工具包。Jdk是整个Java开发的核心，它集成了Jre和一些其他工具，比如：`javac.exe`,`java.exe`,`jar.exe`等

**JVM**: Java Virtual Machine,Java虚拟机。虚拟机是Java实现跨平台的核心。



#### 值传递和引用传递

**值传递**：是指在调用函数时将实际参数复制一份到函数中，这样的话如果函数对其传递过来的形式参数进行修改，将不会影响到实际参数。

**引用传递**： 是指在调用函数时将对象的地址直接传递到函数中，如果在对形式参数进行修改，将影响到实际参数的值



#### Java 中的数据类型

**基本数据类型**

* 整数型：byte、short、int、long

   byte 也就是字节，1 byte = 8 bits，byte 的默认值是 0 

  short 占用两个字节，也就是 16 位，1 short = 16 bits，它的默认值也是 0 

  int 占用四个字节，也就是 32 位，1 int = 32 bits，默认值是 0

  long 占用八个字节，也就是 64 位，1 long = 64 bits，默认值是 0L

  所以整数型的占用字节大小空间为 long > int > short > byte

* 浮点型：float 、double

  float 是单精度浮点型，占用 4 位，1 float = 32 bits，默认值是 0.0f

  double 是双精度浮点型，占用 8 位，1 double = 64 bits，默认值是 0.0d

  浮点型：float 、double

- 字符型

  字符型就是 char，char 类型是一个单一的 16 位 Unicode 字符，最小值是 `\u0000 (也就是 0 )`，最大值是 `\uffff (即为 65535)`，char 数据类型可以存储任何字符，例如 char a = 'A'。

* 布尔型

  布尔型指的就是 boolean，boolean 只有两种值，true 或者是 false，只表示 1 位，默认值是 false。


**引用数据类型**

* 类(class)
* 接口(interface)
* 数组([])

- 

#### Java中的克隆

1.为什么使用clone？

![image-20211120165649720](C:\Users\y3tu\AppData\Roaming\Typora\typora-user-images\image-20211120165649720.png)
