## Java基础
### 1. JDK和JRE的区别
* JDK: Java Development Kit的简称，Java开发工具包，提供Java的开发环境和运行环境。
* JRE: Java Runtime Environment的简称，Java运行环境，为Java的运行提供所需环境。
  具体来说JDK其实包含了JRE，同时还包含了编译Java源码的编译器Javac，还包含了很多Java程序调试和分析的工具。简单来说：如果你需要运行Java程序，只需要安装JRE就可以了，如果你需要编写Java程序，需要安装JDK。

### 2. ==和equals区别
`==`比较两个对象在内存里是不是同一个对象，就是说在内存里面的存储位置一致。两个String对象存储的值是一样的，但是有可能在内存里存储在不同的地方。   
`==`比较的是引用而`equals()`方法比较的是内容。`equals()`方法是由Object对象提供的，可以由子类进行重写，默认的实现只有当对象和自身进行比较时才会返回true，这时和`==`是等价的。`String、BitSet、Date、File`等都对`equals`方法进行了重写。对两个String对象，值相等意味着它们包含相同的字符序列。
对于基本类型的包装类，值相等意味着对应的基本类型的值一样。

### 3. hashCode()和equals()的区别
>**`equals` 保证可靠性，`hashCode` 保证性能**   
>`equals` 和 `hashCode` 都可用来判断两个对象是否相等，但是二者有区别：
- `equals` 可以保证比较对象是否是绝对相等，即「`equals` 保证可靠性」
- `hashCode` 用来在最快的时间内判断两个对象是否相等，可能有「误判」，即「`hashCode` 保证性能」
- 两个对象 `equals` 为 true 时，要求 `hashCode` 也必须相等
- 两个对象 `hashCode` 为 true 时，`equals` 可以不等（如发生哈希碰撞时）
  hashCode误判是指不同对象的 `hashCode` 也可能相等，这是因为 `hashCode` 是根据地址 `hash` 出来的一个 `int 32` 位的整型数字，相等是在所难免。   
  根据阿里《Java开发手册》，对 Java 对象的 `hashCode` 和 `equals` 方法，有如下强制约定：
- 只要覆写 equals，就必须覆写 hashCode
- 因为 Set 存储的是不重复的对象，依据 hashCode 和 equals 进行判断，所以 Set 存储的对象必须覆写这两个方法。
- 如果自定义对象作为 Map 的键，那么必须覆写 hashCode 和 equals。
- String 已经覆写 hashCode 和 equals 方法，所以我们可以愉快地使用 String 对象作为 key 来使用

### 4. float f = 3.4 是否正确？
**不正确**   
3.4 默认是双精度，将双精度型(double)赋值给浮点型(float)属于下转型(down-casting,也称为窄化)会造成精度损失，因此需要强制类型转换`float f = (float)3.4`或者`float f = 3.4F`。

### 5. Java中的操作字符串都有哪些类？它们之间有什么区别？

操作字符串的类：`String`,`StringBuffer`,`StringBuilder`

String声明的是不可变的对象，每次操作都会生成一个新的String对象，然后将指针指向新的String对象。

StringBuffer和StringBuilder可以在原有的对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用String。

StringBuffer是线程安全的，StringBuilder是非线程安全的，但StringBuilder的性能却高于StringBuffer，所以在单线程环境下推荐使用StringBuilder，多线程环境下推荐使用StringBuffer。

### 6. `String str="i"` 与 `String str = new String("i")`一样吗？

不一样，因为内存的分配方式不一样。

`String str="i"`:分配到常量池中

`String str = new String("i")`:分配的堆内存

### 7. Java的四种引用
- **强引用**
  这种引用属于最普通最强硬的一种存在，如`String s = "abc"`,变量`s`就是字符串`"abc"`的强引用，只要强引用存在，则垃圾回收器就不会回收这个对象,只有在和`GCRoots`断绝关系时，才会被消灭掉。
- **软引用**
  软引用用于维护一些可有可无的对象。在内存足够的时候，软引用对象不会被回收，只有在内存不足时，系统则会回收软引用对象，如果回收了软引用对象之后仍然没有足够的内存，才会抛出内存溢出异常。   
  可以看到，这种特性非常适合用在缓存技术上。比如网页缓存、图片缓存等。 软引用可以和一个引用队列`ReferenceQueue`联合使用，如果软引用所引用的对象被垃圾回收， Java 虚拟机就会把这个软引用加入到与之关联的引用队列中
- **弱引用**
  弱引用对象相比较软引用，要更加无用一些，它拥有更短的生命周期。当JVM进行垃圾回收时，无论内存是否充足，都会回收被弱引用关联的对象。弱引用拥有更短的生命周期，在 Java 中，用` java.lang.ref.WeakReference `类来表示。它的应用场景和软引用类似，可以在一些对内存更加敏感的系 统里采用
- **虚引用**
  这是一种形同虚设的引用，在现实场景中用的不是很多。虚引用必须和引用队列`ReferenceQueue` 联合使用。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回 收。实际上，虚引用的 get，总是返回 null。

### 8. 静态代理和动态代理的区别？
静态代理中代理类在编译期就已经确定，而动态代理则是JVM运行时动态生成，静态代理的效率比动态代理高一些，但是静态代理代码冗余大，一旦需要修改接口，代理类和委托类都需要修改。

### 9. JDK动态代理和CGLIB动态代理的区别？
**JDK动态代理**：只能对实现了接口的类生成代理，而不能针对类。    
**CGLIB动态代理**：是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法。因为是继承，所以该类或方法不要声明成final。

### 10. Java中的异常分类?

> * 编译时异常(也叫受控异常)`CheckedException`
> * 运行时异常(也叫非受控异常)` UnCheckedException`

Java认为`CheckedException`都是可以被处理的异常，所以Java程序必须显式处理`CheckedException`。如果程序没有处理Checked 异常，该程序在编译时就会发生错误无法编译。这体现了Java 的设计哲学：没有完善错误处理的代码根本没有机会被执行。对Checked异常处理方法有 两种：

- 当前方法知道如何处理该异常，则用try...catch块来处理该异常。
- 当前方法不知道如何处理，则在定义该方法时声明抛出该异常。

运行时异常只有当代码在运行时才发行的异常，编译的时候不需要try…catch。

**常见异常:**

* `NullPointerException`: 空指针异常
* `NoSuchMethodException`：找不到方法
* `IllegalArgumentException`：不合法的参数异常
* `IndexOutOfBoundException`: 数组下标越界异常
* `IOException`：由于文件未找到、未打开或者I/O操作不能进行而引起异常
* `ClassNotFoundException` ：找不到文件所抛出的异常
* `NumberFormatException`： 字符的UTF代码数据格式有错引起异常；
* `InterruptedException`： 线程中断抛出的异常



### 11. 什么是值传递和引用传递？

**值传递**：是指在调用函数时将实际参数复制一份到函数中，这样的话如果函数对其传递过来的形式参数进行修改，将不会影响到实际参数。

**引用传递**： 是指在调用函数时将对象的地址直接传递到函数中，如果在对形式参数进行修改，将影响到实际参数的值



## JVM
### 1. 哪些情况下的对象会被垃圾回收机制处理掉？
利用可达性分析算法，虚拟机会将一些对象定义为`GCRoots`，从`GCRoots`出发沿着引用链向下寻找，如果某个对象不能通过`GCRoots`寻找到，虚拟机就认为该对象可以被回收掉。
* 哪些对象可以被看做是`GCRoots`呢？
  - 虚拟机栈（栈帧中的本地变量表）中引用的对象
  - 方法区中的类静态属性引用的对象，常量引用的对象
  - 本地方法栈中JNI(Native方法)引用的对象
* 对象不可达，一定会被垃圾收集器回收么？ **不一定**
  - 先判断对象是否有必要执行`finalize()`方法，对象必须重写`finalize()`方法且没有被运行过。
  - 若有必要执行，会把对象放到一个队列中，JVM 会开一个线程去回收它们，这是对象最后一次可以逃逸清理的机会。