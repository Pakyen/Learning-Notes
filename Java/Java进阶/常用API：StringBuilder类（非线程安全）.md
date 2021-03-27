# 常用API：StringBuilder类（非线程安全）

## StringBuilder类
### 原理
StringBuilder类也称为字符串缓冲区

> Java中的字符串是引用数据类型，基于String类，Java程序中所有字符串的字面值都作为这个类的实例实现  
> **字符串是常量，它们的值在创建之后不能更改**  
> 因为String对象是不可变的，所以可以共享，  
> 如`String str = "abc";`  
> 等效于：  
> `char data[] = ['a','b','c'];`  
> `String str = new String(data);`  

> Java字符串的底层是一个被final修饰的数组，不能改变，是一个常量  
> private final byte[] value;  

如果进行字符串的相加，内存中就会有多个字符串，占用空间多，效率低下
如
```
String s = "a" + "b" + "c" = "abc"

"a","b","c"三个字符串
"a"+"b" "ab" 1个字符串
"ab"+"c" "abc" 1个字符串
所以这样的字符串相加会产生5个字符串
```

* 字符串缓冲区StringBuilder支持可变的字符串，可以提高字符串的操作效率（可以看作一个长度可以变化的字符串）
* 底层也是一个数组，但是没有被final修饰，可以改变长度
`byte[] value = new byte[16];`初始容量是16位
![](%E5%B8%B8%E7%94%A8API%EF%BC%9AStringBuilder%E7%B1%BB%EF%BC%88%E9%9D%9E%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%EF%BC%89/%E6%88%AA%E5%B1%8F2021-02-24%2013.34.00.png)
* StringBuilder在内存中始终是一个数组，占用空间少，效率高
* 如果超出了StringBuilder的容量，会自动的扩容

### StringBuilder的构造方法
* `public StringBuilder()`：构造一个空的StringBuilder容器
* `public StringBuilder(String str)`：构造一个StringBuilder容器，并将字符串添加进去
### StringBuilder类的成员方法
1. `public StringBuilder append(...)`：添加任意类型数据的字符串形式，并返回当前对象自身
	* append方法返回的是this，返回调用方法的对象
	```
	StringBuilder bu1 = new StringBuilder();
	// StringBuilder bu2 = bu1.append("abc"); 
	//这两个对象实际上是一个对象
	//使用append方法无需接受返回值
	bu1.append("abc");
	```
	* append方法返回值是对象，支持链式编程
		* 如 `bu1.append("abc").append(1).append(true)`
		
2. `StringBuilder reverse();` ：反转内容

3. `String toString() `：将缓冲区内容（StringBuilder对象）转为字符串
	* `String s = bu1.toString();`