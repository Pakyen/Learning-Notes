# 8 String类、static、Arrays、Math类

# 8.1 String类
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/E67079F8-9089-4DA0-9545-5EE56D617599.png)
	* 位于java.lang下，所以不用导包
	* 程序当中所有的双引号字符串，都是String类的对象（就算没有new）
	* 字符串的内容永不可变【重点】，是一个常量
	* 正是因为字符串不可改变，所以字符串是可以共享使用的
	* 字符串 _效果上_ 相当于是char型字符数组
	* 字符串 _底层原理_ 是byte[] 字节数组

# 8.2 字符串的构造方法和直接创建
创建字符串的常见3+1种方式
* 三种构造方法：
	* public String()，创建一个空白字符串，不含有任何内容
	* public String(char[] array)，根据字符数组内容创建对应的字符串
	* public String(byte[] array)，根据字节数组的内容创建对应的字符串
* 一种直接创建：
	* String str = “Hello”

# 8.3 字符串常量池
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/7C4E557E-9283-4198-A9BC-3B7D6911AB09.png)
* 字符串常量池：程序当中直接写上的双引号字符串，就在字符串常量池中
* new的不在常量池中

* 对于基本类型来说，==是进行数值的比较
* 对于引用类型来说，==是进行地址值的比较 
（注意，String是引用类型）

从结果中可见，str1和str2的地址是一样的，str3和1、2的地址不一样
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-14%2010.53.03.png)
* str1和str2是重复利用的同一个字符串
* str3是通过new创建的，所以不在字符串常量池中，然后它自己通过构造函数把char[]转换成byte[]
	
# 8.4 字符串的比较相关方法
‘==‘是进行对象的地址值比较，如果确实需要字符串的内容比较，可以使用两个方法：
	* public boolean ::equals::(Object obj)
		* 参数可以是任何对象，任何对象都能用Object进行接收
		* *只有参数是一个字符串* ，并且内容相同才会返回true；否则,false
		* equals方法具有对称性, a.equals(b)等于b.equals(a)
		* 如果比较一个常量和一个变量：
			* ::推荐：”abc”.equals(str)::
			* 不推荐：str.equals(“abc”)
			* ⚠️因为str可能为null，会报错，空指针异常！
	* public boolean ::equalsIgnoreCase::(String str)：
		* 忽略大小写，进行内容比较 

# 8.5 字符串的获取相关方法
	* public int length();
	* public String concat(String str);
	* public char charAt(int index);
	* public int indexOf(String str);查找参数 _字符串_ 在本字符串中首次出现的索引位置，如果没有返回-1
	

# 8.6 字符串的截取方法
	* public String substring(int index):
		* 从参数位置，一直到字符串末尾，返回新字符串
	* public String substring(int begin, int end)
		* [begin,end)，左闭右开区间！
	
# 8.7 字符串的转换相关方法
	* public char[] toCharArray()
		* 将当前字符串拆分成为字符数组作为返回值
	* public byte[] getBytes()
		* 获得当前字符串底层的字节数组
	* public String replace(CharSequence, oldString, CharSequence newString)
		* 将所有出现的老字符串替换称为新的字符串  
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-14%2014.10.25.png)

# 8.8 字符串的分割方法
	* public String[] split(String regex)
		* 按照参数的规则，将字符串分割成若干部分
		* plit方法的参数其实是一个正则表达式
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-14%2014.15.11.png)
		* 注意，如果按照英文句号”.”进行切分，必须要用”\\.”		


# 8.9 静态static关键字概述
如果一个成员变量使用了static关键字，那么这个变量不再属于对象自己，而是属于所在的类。也就是多个对象共享同一个数据
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-14%2014.23.22.png)
 
# 8.10 static修饰成员变量
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/E0E8161E-2326-4072-948F-689C004D00D8.png)
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/37D00AAC-A670-4AB0-862F-98AC0FA2A588.png)
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-14%2014.30.34.png)
还可以这样用：
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/49DFD926-88FA-469B-9284-50DDD9001481.png)

# 8.11 static关键字修饰成员方法
一旦使用static修饰成员方法，那么这就成为了静态方法。
静态方法不属于对象，而属于类。
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-14%2014.40.21.png)
* 如果没有static关键字，必须首先创建对象，然后通过对象才能使用它
* 如果有了static关键字，那么不需要创建对象，直接就能通过类名称来使用它
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-14%2014.42.33.png)
* 静态方法推荐使用类名称来调用： Myclass.methodStatic();

* Summary:
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-14%2014.43.24.png)

* 注意事项：
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/3B12AB37-E1B8-4BCD-AB2E-8DA50BCA29A4.png)
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/C5E21456-C545-4436-B53F-858E5B74CFB6.png)

# 8.12 静态static的内存图
<a href='14_%E9%9D%99%E6%80%81static%E7%9A%84%E5%86%85%E5%AD%98%E5%9B%BE.flv'>14_静态static的内存图.flv</a>
![](8%20String%E7%B1%BB%E3%80%81static%E3%80%81Arrays%E3%80%81Math%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-14%2014.59.40.png)

# 8.13 静态代码块
静态代码块的格式是：
public class 类名称{
	static{
		// 静态代码块的内容
	}
}
* 特点：当第一次用到本类时，静态代码块执行::唯一的一次::
* 静态内容总是优先于非静态，所以静态代码块比构造方法先执行
* 静态代码块的典型用途：
	* 用来一次性地对静态成员变量赋值

# 8.14 数组工具类 Arrays
java.util.Arrays是一个与数组相关的工具类，里面提供了大量的静态方法，用来实现数组常见的操作

* public static String toString(数组），将参数数组变成字符串，（按照默认格式：[按照默认格式：[元素1，元素2，元素3，…])
* public static void sort(数组）：默认按照升序对数组的元素进行排序

# 8.15 数学工具类 Math
java.util.Math类是数学相关的工具类，里面提供了大量的静态方法，完成与数学运算相关的操作

* public static double abs(double num)：取绝对值
* public static double ceil(double num)：向上取整
* public static double floor(double num)：向下取整
* public static long round(double num)：四舍五入
* Math.PI 代表近似的圆周率常量 

