# Java异常详解

## 异常的概念
异常，就是不正常的意思
* **异常** ：指的是程序在执行过程中，出现的非正常的情况，最终会导致JVM的非正常停止。

在Java等面向对象的编程语言中，异常本身是一个类，产生异常就是创建异常对象并抛出了一个异常对象。然后把异常对象传递给虚拟机处理（虚拟机就会中断程序)。Java处理异常的方式是中断处理
> 异常指的并不是语法错误,语法错了,编译不通过,不会产生字节码文件,根本不能运行. 异常是运行的时候，才发现不正常  

## 异常的体系
异常机制其实是帮助我们找到程序中的问题，异常的根类（最顶层的父类）是`java.lang.Throwable` ，其下有两个子类：`java.lang.Error`与`java.lang.Exception`，平常所说的异常指`java.lang.Exception`
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2011.05.58.png)
* java.lang.Throwable:类是 Java 语言中所有错误或异常的超类
	* ::Exception:编译期异常::,进行编译(写代码)java程序出现的问题
> Exception表示异常，异常产生后程序员可以通过代码的方式纠正，使程序继续运行，是必须要处理的  
	* ::RuntimeException:运行期异常::,java程序运行过程中出现的问题
          
	* ::Error:错误::
            错误就相当于程序得了一个无法治愈的毛病(非典,艾滋).必须修改源代码,程序才能继续执行

### Throwable中的常用方法
* `public void printStackTrace()`：打印异常的详细信息
> *包含了异常的类型,异常的原因,还包括异常出现的位置,在开发和调试阶段,都得使用printStackTrace*  

* `public String getMessage()`：获取发生异常的原因
> *提示给用户的时候,就提示错误原因*  

* `public String toString()`：获取异常的类型和异常描述信息(不用)

**出现异常,不要紧张,把异常的简单类名,拷贝到API中去查。**
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2011.21.59.png)


### 异常的分类
我们平常说的异常就是指Exception，因为这类异常一旦出现，我们就要对代码进行更正，修复程序
**异常(Exception)的分类**:根据在编译时期还是运行时期去检查异常

* **编译时期异常**：checked异常。在编译时期,就会检查,如果没有处理异常,则编译失败。(如日期格式化异常)

* **运行时期异常** ：runtime异常。在运行时期,检查异常.在编译时期,运行异常不会编译器检测(不报错)。(如数学异常)* 
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E5%BC%82%E5%B8%B8%E7%9A%84%E5%88%86%E7%B1%BB.png)

## 举例
* 编译期异常，写代码的时候就会提示异常
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/C49501B5-D947-4D45-9825-66799BE3F4D7.png)
1. 可以使用throws，抛出异常给虚拟机处理
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/8930F59F-8A61-4F86-8A03-D4376A7CFB76.png)
这样正常情况下是能运行的，但是如果程序有异常，如这里的字符串格式不对，或者不符合pattern，虚拟机就会终止程序，并把异常打印在控制台
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2011.33.09.png)

2. try … catch方式处理异常（编译异常）；
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/ADF6F283-812F-411F-89C1-515C16E59180.png)
不需要在方法名后写throws，用Try catch自己处理异常。这样做的好处是如果try{}内的code snippet有异常时，可以用catch捕捉，然后做出处理。这里的处理是打印出异常信息，但是程序不会被虚拟机终止，还可以运行后续代码
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2011.36.03.png)


* 运行期异常
如索引越界异常
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/3B145890-7F7A-4646-AC95-7E40417F7D9D.png)
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2011.40.50.png)


* Error 错误


## 异常产生的过程解析
1. ::程序发生异常时（如索引越界异常），这时候jvm会检测出程序出现异常::
2. JVM会做两件事：
	1. ::根据异常产生的原因，创建 new 一个异常对象::（包括异常的内容，原因，位置）
		new ArrayIndexOutOfBoundsException(4);
	2. 在出现异常的方法getElement中，::没有处理异常的逻辑（try…catch），那么JVM就会把异常抛出给方法的调用者（main方法）来处理这个异常::
3. main方法接受到了异常对象，::但main方法也没有异常的处理逻辑，那么main方法就会继续把异常传递给JVM::
4. JVM接受到异常对象，又作两件事情：
	1. ::把异常对象中的名称、内容、位置，都以红色显示在控制台::
	2. ::中断处理：终止正在执行的java程序::

![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2011.46.14.png)

## 异常的处理
> java处理异常的5个关键字：try、catch、throw、throws、finally  

### 抛出异常throw
* `throw关键字`
	* 作用：
		* 可以使用throw关键字在指定的方法中抛出指定的异常
	* 使用格式：
		* `throw new xxxException("异常产生的原因")`
	* 注意事项：
		1. throw关键字必须写在方法的内部
		2. throw关键字后面new的对象必须是Exception或者Exception的子类对象
		3. throw关键字抛出指定的异常对象，我们就必须处理这个异常对象：
			1. 如果throw关键字后面创建的是RuntimeException或者是RuntimeException的子类对象，我们可以不处理，默认交给JVM处理（打印对象、中断）
			2. 如果throw关键字后面创建的是编译异常，我们就必须处理这个异常（throws/ try…catch）

> tips: 学习或工作中，首先必须对方法传递过来的参数进行合法性校验  
> 如果参数不合法，那么就必须使用抛出异常的方式，告知方法的调用者，传递的参数有问题  

#### throw的使用
```
public class ThrowDemo {
    public static void main(String[] args) {
        //创建一个数组 
        int[] arr = {2,4,52,2};
        //根据索引找对应的元素 
        int index = 4;
        int element = getElement(arr, index);

        System.out.println(element);
        System.out.println("over");
    }
    /*
     * 根据 索引找到数组中对应的元素
     */
    public static int getElement(int[] arr,int index){ 
       	//判断  索引是否越界
        if(index<0 || index>arr.length-1){
             /*
             判断条件如果满足，当执行完throw抛出异常对象后，方法已经无法继续运算。
             这时就会结束当前方法的执行，并将异常告知给调用者。这时就需要通过异常来解决。 
              */
             throw new ArrayIndexOutOfBoundsException("数组索引越界");
        }
        int element = arr[index];
        return element;
    }
}
```
> 注意：如果产生了问题，我们就会throw将问题描述类即异常进行抛出，也就是将问题返回给该方法的调用者。  
> 那么对于调用者来说，该怎么处理呢？一种是进行捕获处理，另一种就是继续讲问题声明出去，使用throws声明处理。  


### Objects非空判断
还记得我们学习过一个类Objects，曾经提到过它由一些**静态**的实用方法组成，这些方法是null-save（**空指针安全的**）或null-tolerant（**容忍空指针的**），那么在它的源码中，对对象为null的值进行了抛出异常操作

* `public static <T> T requireNonNull(T obj)`：对对象进行判断，判断是否为null，如果为null就抛出空指针异常，非空就返回对象本身
source code:
```
public static <T> T requireNonNull(T obj) {
    if (obj == null)
      	throw new NullPointerException();
    return obj;
}
```

### 声明异常 throws
* ::throws关键字：异常处理的第一种方式::，交给别人处理

* 作用：
	* 当方法内部抛出异常对象的时候,那么我们就必须处理这个异常对象
        * 可以使用throws关键字处理异常对象,会把异常对象声明抛出给方法的调用者处理(自己不处理,给别人处理),最终交给JVM处理-->中断处理

* 使用格式:在方法声明时使用
```
修饰符 返回值类型 方法名(参数列表) throws AAAExcepiton,BBBExcepiton...{
	throw new AAAExcepiton("产生原因");
	throw new BBBExcepiton("产生原因");
   ...
}
```
* 注意:
				1. throws关键字必须写在方法声明处
				2. throws关键字后边声明的异常必须是Exception或者是Exception的子类
				3. 方法内部如果抛出了多个异常对象,那么throws后边必须也声明多个异常
		* 如果抛出的多个异常对象有子父类关系,那么直接声明父类异常即可

				4. 调用了一个声明抛出异常的方法,我们就必须的处理声明的异常
		* 要么继续使用throws声明抛出,交给方法的调用者处理,最终交给JVM
		* 要么try...catch自己处理异常
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2013.51.29.png)
> tips：FileNotFoundException是IOException的子类，所以根据注意事项3，我们可以不用声明它了，直接声明 throws IOException即可  
> 又因为所有异常都是Exception的子类，所以我们这里声明 throws Exception也可以  

### 捕获异常 try…catch
* ::try…catch：异常处理的第二种方式::，自己处理异常
* 如果异常出现的话,会立刻终止程序,所以我们得处理异常:
	1. 该方法不处理,而是声明抛出,由该方法的调用者来处理(throws)。
	2. 在方法中使用try-catch的语句块来处理异常

* **捕获异常**：Java中对异常有针对性的语句进行捕获，可以对出现的异常进行指定方式的处理
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2013.58.58.png)
::try中可能抛出什么异常，catch中就定义什么异常变量，用来接受这个异常::

* 注意:
	1. try中可能会抛出多个异常对象,那么就可以使用多个catch来处理这些异常对象

	2. 如果try中产生了异常,那么就会执行catch中的异常处理逻辑,执行完毕catch中的处理逻辑,继续执行try...catch之后的代码
	3. 如果try中没有产生异常,那么就不会执行catch中异常的处理逻辑,执行完try中的代码,继续执行try...catch之后的代码

![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2014.02.53.png)


## Throwable类中的三个异常处理方法
Throwable类中定义了3个异常处理的方法
	* String `getMessage()` 返回此 throwable 的简短描述。
	* String `toString()` 返回此 throwable 的详细消息字符串。
	* void `printStackTrace()`  JVM打印异常对象,默认此方法,打印的异常信息是最全面的

## finally 代码块
* 注意
1. finally不能单独使用,必须和try…catch一起使用
2. finally一般用于资源释放(资源回收),无论程序是否出现异常,最后都要资源释放(IO)
finally代码块
* 格式:
```
        try{
            可能产生异常的代码
        }catch(定义一个异常的变量,用来接收try中抛出的异常对象){
            异常的处理逻辑,异常异常对象之后,怎么处理异常对象
            一般在工作中,会把异常的信息记录到一个日志中
        }
        ...
        catch(异常类名 变量名){

        }finally{
            无论是否出现异常都会执行
        }
```

## 异常注意事项
* 多个异常使用捕获又该如何处理呢？
	1. 多个异常分别处理 ：*一个异常一对try…catch*
	2. 多个异常一次捕获，多次处理：*一个try对应多个catch*
	3. 多个异常一次捕获一次处理

一般我们是使用::一次捕获多次处理::方式，格式如下：
```
try{
     编写可能会出现异常的代码
}catch(异常类型A  e){  当try中出现A类型异常,就用该catch来捕获.
     处理异常的代码
     //记录日志/打印异常信息/继续抛出异常
}catch(异常类型B  e){  当try中出现B类型异常,就用该catch来捕获.
     处理异常的代码
     //记录日志/打印异常信息/继续抛出异常
}
```

2. 一次捕获多次处理
一个try多个catch的注意事项：
	
	1. 多个catch中的异常不能相同	

	2. catch里边定义的异常变量,::如果有子父类关系,那么子类的异常变量必须写在上面::, 否则就会报错
                ArrayIndexOutOfBoundsException extends IndexOutOfBoundsException
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/9FDE5EA8-5DD4-465F-9F69-AAD43F880388.png)
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/C80CA385-659E-43E7-960B-0A52A99D55A4.png)

3. 一次捕获一次处理
try里有多个异常可能发生，但是只catch处理一次（catch里的变量既能接收第一个异常对象，又能接收第二个异常对象）
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/B60599E6-D9AF-40BF-B457-BD192B90E9E0.png)

* 运行时异常被抛出可以不处理。即不捕获也不声明抛出。
* 如果finally有return语句,**永远返回finally中的结果**（避免在finally中写return）
						* 不管try中return了什么结果
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/%E6%88%AA%E5%B1%8F2021-02-26%2014.25.20.png)

* 如果父类抛出了多个异常,子类重写父类方法时,抛出和父类相同的异常或者是父类异常的子类或者不抛出异常。

* ::父类方法没有抛出异常，子类重写父类该方法时也不可抛出异常::。 ~此时子类产生该异常，只能捕获处理，不能声明抛出~

## 自定义异常（模拟注册异常）
### 概述
![](Java%E5%BC%82%E5%B8%B8%E8%AF%A6%E8%A7%A3/CA8A0943-1A83-4F5C-8754-B2CCDF07330D.png)
* 格式:
        public class XXXExcepiton **extends Exception | RuntimeException**{
            **添加一个空参数的构造方法**
            **添加一个带异常信息的构造方法**
        }
* 注意:
1. 自定义异常类一般都是以Exception结尾,说明该类是一个异常类
2. 自定义异常类,必须的继承Exception或者RuntimeException
    	继承Exception:那么自定义的异常类就是一个编译期异常,如果方法内部抛出了编译期异常,就必须处理这个异常,要么throws,要么try...catch
    继承RuntimeException:那么自定义的异常类就是一个运行期异常,无需处理,交给虚拟机处理(中断处理)


### 模拟注册异常
要求：我们模拟注册操作，如果用户名已存在，则抛出异常并提示：亲，该用户名已经被注册。
首先定义一个登陆异常类RegisterException：
```
//业务逻辑异常
public class RegisterException extends Exception {
    /**
     * 空参构造
     */
    public RegisterException() {
    }

    /**
     *
     * @param message 表示异常提示
     */
    public RegisterException(String message) {
        super(message);
    }
}
```
模拟登陆操作，使用数组模拟数据库中存储的数据，并提供当前注册账号是否存在方法用于判断
```
public class Demo {
    // 模拟数据库中已存在账号
    private static String[] names = {"bill","hill","jill"};
   
    public static void main(String[] args) {     
        //调用方法
        try{
              // 可能出现异常的代码
            checkUsername("nill");
            System.out.println("注册成功");//如果没有异常就是注册成功
        }catch(RegisterException e){
            //处理异常
            e.printStackTrace();
        }
    }

    //判断当前注册账号是否存在
    //因为是编译期异常，又想调用者去处理 所以声明该异常
    public static boolean checkUsername(String uname) throws LoginException{
        for (String name : names) {
            if(name.equals(uname)){//如果名字在这里面 就抛出登陆异常
                throw new RegisterException("亲"+name+"已经被注册了！");
            }
        }
        return true;
    }
}
```


