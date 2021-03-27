# 常用API：日期时间类

## 毫秒值的概念和作用
## 1 Date类
`java.util.Date`类表示特定的瞬间，精确到毫秒（不在lang包下，需要导包）
### 毫秒值的概念
毫秒：千分之一秒
特定的瞬间，一个时间点，一刹那时间

### 毫秒值的作用
可以对时间和日期进行计算

如2099-01-03 到 2088-01-1中间一共有多少天
可以把日期转换为毫秒进行计算，计算完再把毫秒转换为日期

	* 把日期转换为毫秒：
		* 当前日期：2088 - 01 -01
		* 时间原点（0毫秒）：1970年1月1日 00:00:00
		* 就是计算当前日期到时间原点之间一共经历了多少毫秒
		* `System.currcentTimeMillis()` 获取当前系统时间到时间原点经历了多少毫秒
		* 1天 = 24×60×60 = 86400秒 =86400000毫秒

	* 注意：中国属于东八区，会把时间增加8个小时
	* 时间原点是英国格林威治时间

## Date类的构造方法和常用成员方法
* 构造方法
	* Date()：获取当前系统的日期和时间
	* Date(long date)： 传递毫秒值，把毫秒转换为Date日期

* 常用成员方法
	* `Long getTime()`：把日期转换为毫秒，相当于`System.currcentTimeMillis()`
	

## 2 DateFormat类：java.text.SimpleDateFormat类
`java.text.DateFormat`是日期/时间格式化子类的抽象类，我们通过这个类可以帮我们完成日期和文本之间的转换，也就是可以在Date对象和String对象之间进行来回转换
	* 格式化：按照指定格式，从Date对象转换为String对象
	* 解析：按照指定格式，从String对象转换为Date对象

> 查看jdk可以发现，DateFormat类是一个抽象类`public abstract class DateFormat`，直接父类是`java.text.Format`  
> 父类Format的直接已知子类为：`DateFormat`、`MessageFormat`、`NumberFormat`  

 * DateFormat类的作用：
	 * 格式化：日期 -> 文本
	 * 解析：文本 -> 日期
* DateFormat类的成员方法：
	* `String format(Date date)`：
		* 按照给定的pattern，把Date日期格式化为合格的字符串
	* `Date parse(String source)`：
		* 把符合模式的字符串，解析为Date日期

### 构造方法
由于DateFormat为抽象类， ~不能直接使用~ ，所以需要常用的子类`java.text.SimpleDateFormat`，这个类需要一个pattern来制定格式化或解析的标准，构造方法为：
* `public SimpleDateFormat(String pattern):`用给定的模式和语言环境的日期格式符号构造SimpleDateFormat
参数pattern是一个字符串，代表日期时间的自定义格式
	* pattern：
		* 区分大小写的：
			* y 年
			* M 月
			* d 日
			* H 时
			* m 分
			* s 秒
		* e.g. “yyyy-MM-dd HH:mm:ss”
		* e.g. “”yyyy年MM月dd日 HH时mm分ss秒“
		* 注意：模式中的字母不能改变，链接模式的符号可以改变

### format()方法和parse()方法的使用：
* String format(Date date)
	* 使用步骤：
		1. 创建SimpleDateFormat对象，构造方法中传递指定的pattern
		2. 调用SimpleDateFormat对象中的方法format()，将Date日期格式化为指定的字符串
* Date parse(String source)
	* 使用步骤：
		1. 创建SimpleDateFormat对象，构造方法中传递指定的pattern
		2. 调用SimpleDateFormat对象中的parse，把符合模式的字符串解析为Date日期
	* 注意：
	public Date parse(String source) throws ParseException
	parse方法声明了一个异常叫解析异常ParseException
	如果字符串和构造方法中的模式不一样，那么程序就会抛出异常
	调用一个抛出了异常的方法，就必须得处理这个异常，要么throws继续声明抛出异常，要么try…catch自己处理这个异常

### 练习，计算出一个人已经出生了多少天
#### 分析
	1. 使用Scanner类中的方法next，获取出生日期
	2. 使用DateFormat类中的方法parse，把字符串的出生日期解析为Date格式
	3. 把Date格式的出生日期转换为毫秒值
	4. 获取当前日期，转换为毫秒值
	5. 使用当前日期的毫秒值减去出生日期的毫秒值
	6. 把毫秒值转换为天
```
public class Test{
	//parse有异常要声明
	public static void main(String[] args) throws ParseExceptiion{ 

		//获取出生日期
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入您的出生日期，格式为yyyy-MM-dd");
		String bthDay = sc.next();
		//使用parse，将字符串转换为Date格式
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		Date birthdayDate = sdf.parse(bthDay);
		//把出生日期转换为毫秒值
		long bthDayTime = new Date().getTime();
		long time = long todayTime - bthDayTime;
		//把毫秒值的差，转换为天
		System.out.println(time/1000/60/60/24);
	}
}
```

## Calendar类
### 介绍
java.util.Calendar类是一个抽象类，里面提供了很多操作日历字段的方法（YEAR、MONTH、DAY_OF_MONTH、HOUR）
Calendar类无法直接创建对象使用，里边有一个静态方法叫getInstance()，该方法返回了Calendar类的子类对象
`static Calendar getInstance()`使用默认时区和语言环境获得一个日历
### 获取对象的方式
```
Calendar c = Calendar.getInstance(); //多态
```

### Calendar类的常用成员方法
	* `public int get(int field)`：返回给定日历字段的值
	* `public void set(int field, int value)`：将给定的日历字段设置为指定值
	* `public Date getTime()`：返回一个表示此Calendar时间值的Date对象（从历元年到现在的毫秒值）
	* `public void add(int n, int value)`：将指定日历字段增加或者减少指定的值
		* 如：
```
Calendar c = Calendar.getInstance();
c.set(Calender.YEAR, 2000);
//这里可以用c.get查看c的是什么时候
//add方法
c.add(Calendar.YEAR, 2);
//这里输入c.get(Calendar.YEAR)得到的就是2002了 
```
### 成员方法的参数：
	* int field：日历类的字段，可以使用Calendar类的静态成员变量获取
	* public static final int YEAR = 1; 年
	* public static final int MONTH = 2; 月
	* public static final int DATE = 5; 月中的某一天
	* public static final int DAY_OF_MONTH = 5; 月中的某一天，和上面的DATE字段完全相同
	* public static final int HOUR = 10; 时
	* public static final int MINUTE = 5; 分
	* public static final int SECOND = 5; 秒

## 日期时间类总结
![](%E5%B8%B8%E7%94%A8API%EF%BC%9A%E6%97%A5%E6%9C%9F%E6%97%B6%E9%97%B4%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-02-24%2012.15.37.png)
![](%E5%B8%B8%E7%94%A8API%EF%BC%9A%E6%97%A5%E6%9C%9F%E6%97%B6%E9%97%B4%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-02-24%2012.16.09.png)