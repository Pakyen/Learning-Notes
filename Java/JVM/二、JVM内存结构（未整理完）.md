# 二、JVM内存结构（未整理完）

## 整体架构
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/3FCD2BF9-45F5-484A-A69F-8A91BB2F00BB%202.png)
	1. 程序计数器
	2. 虚拟机栈
	3. 本地方法栈
	4. 堆
	5. 方法区
- - - -

## 1、程序计数器
### 作用
::用于保存JVM中下一条所要执行的指令的地址::

### 特点
* **线程私有**
	* CPU会为每个线程分配时间片，当当前线程的时间片使用完以后，CPU就会去执行另一个线程中的代码
	* 程序计数器是**每个线程**所**私有**的，当另一个线程的时间片用完，又返回来执行当前线程的代码时，通过程序计数器可以知道应该执行哪一句指令

* **不会存在内存溢出**

![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/FA30CB5E-DEE4-4F2F-8D92-B0BA23B222DA%202.png)



## 2、虚拟机栈
### 定义
* 每个**线程**运行需要的内存空间，称为**虚拟机栈**
* 每个栈由多个**栈帧**组成，对应着每次调用方法时所占用的内存
* 每个线程只能有**一个活动栈帧**，对应着**当前正在执行的方法**

![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-15%2011.36.24%202.png)

### 演示
```
public class Main {
	public static void main(String[] args) {
		method1();
	}

	private static void method1() {
		method2(1, 2);
	}

	private static int method2(int a, int b) {
		int c = a + b;
		return c;
	}
}
```
在控制台中可以看到，主类中的方法在进入虚拟机栈的时候，符合栈的特点
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/C344A630-8E17-40AD-A4D9-A3CAEBFFAE82%202.png)

### 问题辨析

#### 垃圾回收是否涉及栈内存？
	* **不需要**。因为虚拟机栈中是由一个个栈帧组成的，在方法执行完毕后，对应的栈帧就会被弹出栈。所以无需通过垃圾回收机制去回收内存

#### 栈内存的分配越大越好吗？
	* 不是。因为**物理内存是一定的**，栈内存越大，可以支持更多的递归调用，但是可执行的线程数就会越少

#### 方法内的局部变量是否是线程安全的？
	* 如果方法内**局部变量没有逃离方法的作用范围**，则是**线程安全**的
	* 如果如果**局部变量引用了对象**，并**逃离了方法的作用范围**，则需要考虑线程安全问题

问题辨析3:
	多个线程同时执行这个方法，会不会造成x值的混乱？
	不会，因为x值是方法内的布局变量，一个线程对应一个栈，每一次调用方法都会产生一个新的栈帧，所以多个线程同时执行这个方法不会造成x值的混乱。
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-15%2011.50.35%202.png)
	但如果x是static的，那么这个x相当于多个线程的共享变量，那么多个线程同时执行的时候，就会共同作用于同一个x
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-15%2011.55.01%202.png)
	所以如果是共享的，就要考虑线程安全；如果是私有的就不用考虑线程安全



### 栈内存溢出
**Java.lang.stackOverflowError** 栈内存溢出

#### 发生原因
	* 虚拟机栈中，**栈帧过多**（无限递归）
	* 每个栈帧**所占用过大**

### 线程运行诊断

#### CPU占用过多
* Linux环境下运行某些程序的时候，可能导致CPU的占用过高，这时需要定位占用CPU过高的线程

	1. **top**命令， ~查看是哪个**进程**占用CPU过高~
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-15%2012.59.58%202.png)
> top命命令能定位到进程，不过定位不到线程  


	2. **ps H -eo pid, tid（线程id）, %cpu | grep 刚才通过top查到的进程号** 通过ps命令进一步 ~查看是哪个线程占用CPU过高~


	3. **jstack 进程id** 通过查看进程中的线程的nid，刚才通过ps命令看到的tid来**对比定位**，注意jstack查找出的线程id是**16进制的**，**需要转换**

![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-15%2013.06.43%202.png)

#### 程序运行很长时间没有结果
* 死锁
	* 使用**jstack 进程id**来查看死锁

![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-15%2013.37.02%202.png)
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-15%2013.33.42%202.png)
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/A548DA3E-6672-4160-B93C-F05CF351D372%202.png)



## 3. 本地方法栈
一些带有**native关键字**的方法就是需要JAVA去调用本地的C或者C++方法，因为JAVA有时候没法直接和操作系统底层交互，所以需要用到本地方法


## 4、堆
### 定义
	通过new关键字**创建的对象**都会被放在堆内存
### 特点
	* **所有线程共享**，堆内存中的对象都需要**考虑线程安全问题**
	* 有垃圾回收机制

### 堆内存溢出
**java.lang.OutofMemoryError** ：java heap space. 堆内存溢出

### 堆内存诊断
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-15%2014.42.30%202.png)
	* jps
	* jmap
	* jconsole
	* jvirsalvm



## 5.1 方法区
* 首先，方法区是JVM规范的一个概念定义，并不是一个具体的实现，每一个JVM的实现都可以有各自的实现；

* 在JVM规范中，方法区被定义为一种逻辑区域（逻辑上是堆的一部分，不过具体实现不一定在堆内）。　

* 方法区与Java堆一样，是**各个线程共享的内存区域**，它用于**存储**跟类的结构相关的信息：类的成员变量，方法数据，成员方法/构造器方法的代码

* 方法区在虚拟机启动时被创建

### 结构
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/20200608150547%203.png)

### 方法区内存溢出
* 1.8以前会导致**永久代**内存溢出
* 1.8以后会导致**元空间**内存溢出
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-15%2015.19.07%202.png)

使用spring、mybatis时会产生大量的运行期间生成的类，在1.8以前，容易造成永久代内存溢出；但是在jdk1.8之后，元空间由系统本地内存提供，相对充裕，并且垃圾回收机制也是由元空间自行管理的，所以不会像永久代一样经常由于垃圾回收效率低而造成内存溢出


## 5.2 方法区的组成
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/20200608150547%204.png)


### 5.2.1 常量池
不管是jdk1.7还是1.8，方法区都包含了一个叫做运行时常量池的东西，运行时常量池还包含了StringTable。不过在运行时常量池之前，要先了解什么是常量池

> 二进制字节码包含了：类基本信息、常量池、类方法定义、包含了虚拟机指令  

> 我们可以使用jdk提供的javap对class文件(class文件就是字节码)进行反编译，-v参数显示详细信息  
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-16%2010.39.39%202.png)
反编译后可以得到：
	* 类的基本信息
	* 常量池
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/F3CE0F01-D1DF-4BE6-819D-4BAB75B00EA2%202.png)
	* 虚拟机中执行编译的方法（框内的是真正编译执行的内容，**#号的内容需要在常量池中查找**）
> 	框内三行对应代码的虚拟机指令，getstatic代表获取一个静态变量(system.out)，#2代表要获取的静态变量，具体是哪个静态变量就得在常量池的表中查  
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-16%2011.01.19%202.png)
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/4A94AC2A-604B-4F56-B7B6-4B76296BA0F7%202.png)

所以，**常量池就是**
	* 就是一张表（如上图中的constant pool），虚拟机指令根据这张常量表找到要执行的类名、方法名、参数类型、字面量信息

### 5.2.2 运行时常量池
* 运行时常量池
	* 常量池是.class文件中的，当该**类被加载以后**，它的常量池信息就会**放入运行时常量池**，并把里面的**符号地址变为真实地址**



## 5.3 StringTable面试题
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-16%2011.08.56%202.png)
* q1：**s3==s4 ?**，
	* 返回 **false**
	* 因为s3是常量字符串拼接，编译期优化，常量池中没有”ab”，”ab”入池
	* s4是是变量字符串拼接，在运行期间通过StringBuilder作拼接，在堆内存中
	* 所以s3和s4不是一个对象

* q2：**s3==s5 ?**
	* 返回 **true**
	* s5是常量池中已有的对象，和s3是同一个对象

* q3： **s3==s6 ?**
	* 返回**true**
	* s6调用了s4.intern()，虽然串池中已经有”ab”，没能入池成功，但是s4.intern()还会返回串池中的对象，所以s3和s6是同一个对象

* q4
```
String x2 = new String("c") + new String("d");
String x1 = "cd";
x2.intern();
System.out.println(x1==x2);
```
	* 返回false
	* 因为x1在串池中，x2会入池失败，x2还在堆中

* q5
```
String x2 = new String("c") + new String("d");
x2.intern();
String x1 = "cd";
System.out.println(x1==x2);
```
	* 返回true
	* x2先调用了intern()方法，会先入池
	* 所以x2和x1都在串池中，是同一个对象

* q6：如果是jdk1.6呢？
```
String x2 = new String("c") + new String("d");
x2.intern();
String x1 = "cd";
System.out.println(x1==x2);
```
	* 返回false
	* 因为jdk1.6是复制了对象，将副本放入池中，所以无论成功与否，x2和x1都不是一个对象


### 常量池与串池的关系
**串池StringTable**
	* hashtable结构 不能扩容

**特征**
	* 常量池中的字符串仅是符号，只有**在被用到时才会转化为对象**
	* 利用串池的机制，来避免重复创建字符串对象
	* 字符串**变量**拼接的原理是**StringBuilder**
	* 字符串**常量**拼接的原理是**编译器优化**
	* 可以使用**intern方法**，主动将串池中还没有的字符串对象放入串池中
	* **注意**：无论是串池还是堆里面的字符串，都是对象

```
public class StringTableStudy {
	public static void main(String[] args) {
		String s1 = "a"; 
		String s2 = "b";
		String s3 = "ab";	//在串池中
		// 拼接字符串对象来创建新的字符串
		// String s4 = s1+s2; 
 		//s4: new StringBuilder().append("a").append("b").toString() 
		//s4: 是StringBuilder的toString返回的对象，存在于堆内存中
	}
}
```
常量池中的信息，都会被加载到运行时常量池中，但这是a b ab 仅是常量池中的符号，**还没有成为java字符串**
```
0: ldc           #2                  // String a
2: astore_1
3: ldc           #3                  // String b
5: astore_2
6: ldc           #4                  // String ab
8: astore_3
9: return
```
当执行到 ldc #2 时，会把符号 a 变为 “a” 字符串对象，**并放入串池中**（hashtable结构 不可扩容）
当执行到 ldc #3 时，会把符号 b 变为 “b” 字符串对象，并放入串池中
当执行到 ldc #4 时，会把符号 ab 变为 “ab” 字符串对象，并放入串池中
最终**StringTable [“a”, “b”, “ab”]**

**注意**：字符串对象的创建都是**懒惰的**，只有当运行到那一行字符串且在串池中不存在的时候（如 ldc #2 ）时，该字符串才会被创建并放入串池中。

加上`String s4 = s1+s2;`后运行，反编译的结果
```
	 Code:
      stack=2, locals=5, args_size=1
         0: ldc           #2                  // String a
         2: astore_1
         3: ldc           #3                  // String b
         5: astore_2
         6: ldc           #4                  // String ab
         8: astore_3
         9: new           #5                  // class java/lang/StringBuilder
        12: dup
        13: invokespecial #6                  // Method java/lang/StringBuilder."<init>":()V
        16: aload_1
        17: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String
;)Ljava/lang/StringBuilder;
        20: aload_2
        21: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String
;)Ljava/lang/StringBuilder;
        24: invokevirtual #8                  // Method java/lang/StringBuilder.toString:()Ljava/lang/Str
ing;
        27: astore        4
        29: return
```

#### 通过拼接(变量)的方式来创建字符串的**过程**是：
StringBuilder().append(“a”).append(“b”).toString()
最后的toString方法的返回值是一个**新的字符串**，但字符串的**值**和拼接的字符串一致，但是两个不同的字符串，**一个存在于串池之中，一个存在于堆内存之中**
```
String ab = "ab";
String ab2 = a+b;
//结果为false,因为ab是存在于串池之中，ab2是由StringBuffer的toString方法所返回的一个对象，存在于堆内存之中
System.out.println(ab == ab2);
```

#### 通过拼接(常量)的方式来创建字符串的**过程**是：
```
public class StringTableStudy {
	public static void main(String[] args) {
		String a = "a";
		String b = "b";
		String ab = "ab";
		String ab2 = a+b;
		//使用拼接字符串的方法创建字符串
		String ab3 = "a" + "b";
	}
}
```
反编译后的结果
```
 	  Code:
      stack=2, locals=6, args_size=1
         0: ldc           #2                  // String a
         2: astore_1
         3: ldc           #3                  // String b
         5: astore_2
         6: ldc           #4                  // String ab
         8: astore_3
         9: new           #5                  // class java/lang/StringBuilder
        12: dup
        13: invokespecial #6                  // Method java/lang/StringBuilder."<init>":()V
        16: aload_1
        17: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String
;)Ljava/lang/StringBuilder;
        20: aload_2
        21: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String
;)Ljava/lang/StringBuilder;
        24: invokevirtual #8                  // Method java/lang/StringBuilder.toString:()Ljava/lang/Str
ing;
        27: astore        4
        //ab3初始化时直接从串池中获取字符串
        29: ldc           #4                  // String ab
        31: astore        5
        33: return
```
	* 使用**拼接字符串常量**的方法来创建新的字符串时，因为**内容是常量，javac在编译期会进行优化，结果已在编译期确定为ab**，而创建ab的时候已经在串池中放入了“ab”，所以ab3直接从串池中获取值，所以进行的操作和 ab = “ab” 一致。
	* 使用**拼接字符串变量**的方法来创建新的字符串时，因为内容是变量，只能**在运行期确定它的值，所以需要使用StringBuilder来创建**


#### intern方法 1.8 
调用字符串对象的intern方法，会将该字符串对象尝试放入到串池中
	* 如果串池中没有该字符串对象，则放入成功
	* 如果有该字符串对象，则放入失败

无论放入是否成功，都会返回**串池中**的字符串对象

**注意**：此时如果调用intern方法成功，堆内存与串池中的字符串对象**是**同一个对象；如果失败，则不是同一个对象
（这与1.6的intern方法不一样）

##### 例1
```
public class Main {
	public static void main(String[] args) {
		//"a" "b" 被放入串池中，str则存在于堆内存之中
		String str = new String("a") + new String("b");
		//调用str的intern方法，这时串池中没有"ab"，则会将该字符串对象放入到串池中，此时堆内存与串池中的"ab"是同一个对象
		String st2 = str.intern();
		//给str3赋值，因为此时串池中已有"ab"，则直接将串池中的内容返回
		String str3 = "ab";
		//因为堆内存与串池中的"ab"是同一个对象，所以以下两条语句打印的都为true
		System.out.println(str == st2);
		System.out.println(str == str3);
	}
}
```

##### 例2
```
public class Main {
	public static void main(String[] args) {
        //此处创建字符串对象"ab"，因为串池中还没有"ab"，所以将其放入串池中
		String str3 = "ab";
        //"a" "b" 被放入串池中，str则存在于堆内存之中
		String str = new String("a") + new String("b");
        //此时因为在创建str3时，"ab"已存在与串池中，所以放入失败，但是会返回串池中的"ab"
		String str2 = str.intern();
        //false 因为str在堆内存中
		System.out.println(str == str2);
        //false
		System.out.println(str == str3);
        //true
		System.out.println(str2 == str3);
	}
}
```

#### intern方法 1.6
调用字符串对象的intern方法，会将该字符串对象尝试放入到串池中
	* 如果串池中没有该字符串对象，会将该字符串对象**复制一份**，再放入到串池中
	* 如果有该字符串对象，则放入失败
无论放入是否成功，都会返回**串池中**的字符串对象

**注意**：此时无论调用intern方法成功与否，串池中的字符串对象和堆内存中的字符串对象**都不是同一个对象**

### 5.4 StringTable位置
* jdk1.6中，StringTable在方法区的常量池中，永久代
* jdk1.7、1.8中，StriingTable被放到了堆里
![](%E4%BA%8C%E3%80%81JVM%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%EF%BC%88%E6%9C%AA%E6%95%B4%E7%90%86%E5%AE%8C%EF%BC%89/%E6%88%AA%E5%B1%8F2021-04-16%2013.31.57%202.png)

### 5.5 StringTable垃圾回收

StringTable在内存紧张时，会发生垃圾回收

### 5.6 StringTable调优
	* 因为StringTable是由HashTable实现的，所以可以**适当增加HashTable桶的个数**，来减少字符串放入串池所需要的时间
	`-XX:StringTableSize=xxxx`
> 	桶个数多，数据分散，哈希碰撞的几率小，查找速度更快  

	* 考虑是否需要将字符串对象入池可以通过**intern方法减少重复入池**
> 如果使用intern，那么在串池中，相同的地址只会存储一份  


## 6 直接内存
	* 属于操作系统，常见于NIO操作时，**用于数据缓冲区**
	* 分配回收成本较高，但读写性能高
	* 不受JVM内存回收管理