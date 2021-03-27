# 7 Scanner类、Random类、ArrayList类

# 7.1 API文档的使用
<a href='JDK_API_1_6_zh_CN.CHM'>JDK_API_1_6_zh_CN.CHM</a>
API: Application Programming Interface，应用程序编程接口
Java API是一本程序员的字典，是JDK中提供给我们使用的类的说明文档。
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2010.33.04.png)
	1. 看类属于哪个包，介绍
	2. 看类的构造方法摘要（用到哪个就来查询即可）
	3. 方法摘要

# 7.2 Scanner概述及其API文档的使用
Scanner功能：可以实现键盘输入数据，到程序当中
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2010.39.24.png)
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2010.42.25.png)

引用类型的一般使用步骤：
1. 导包
	* 只有java.lang包下的内容不需要导包，其他的包都需要import语句
	* import java.util.Scanner;
2. 创建
	* 类名称 对象名 = new 类名称();
	* Scanner sc = new Scanner(System.in);//代表从键盘输入
3. 使用
	* 对象名.成员方法名
	* int num = sc.nextInt(); //获取键盘输入的下一个int数字
	* String str = sc.next(); //获取键盘输入的一个字符串
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2010.45.05.png)
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2010.46.07.png)
其实我什么获取string叫next()而不是next string，是因为，键盘输入的所有东西其实都是字符串

# 7.3 匿名对象的说明
创建对象的标准格式：
类名称 对象名 = new 类名称();

匿名对象就是只有右边的对象，没有左边的名字和赋值运算符
new 类名称();
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/415522E0-B5A9-4951-B750-6C3D25B2057E.png)
注意事项：
	* 匿名对象只能使用唯一的一次，下次再用不得不再创建一个新对象
使用建议：
	* 如果确定有一个对象只需要使用唯一的一次，就可以用匿名对象

# 7.4 匿名对象作为方法的参数和返回值
* 匿名对象作为方法的参数：
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/53D344E5-E581-46B7-AD87-913E17AA77E1.png)
* 匿名对象作为方法的返回值：
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/801C2517-B16F-4507-816C-1A10874767A1.png)

# 7.5 Random类的概述和基本使用
作用：产生随机数字
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2010.58.22.png)

![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2010.59.46.png)

# 7.6 Random生成指定范围的随机数
 注意nextInt()方法是 _左闭右开_
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/E984DB52-410D-4AF3-AEC9-018941C6545B.png)

# 7.7 对象数组
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/D7C8B01B-C10A-4D29-B753-0440B272518F.png)
但数组有一个缺点，一旦创建，程序运行期间长度不可以改变
但有一种容器，比数组更灵活，更好用，那就是ArrayList集合

# 7.8 ArrayList集合的概述和基本使用
数组的长度不可以发生改变
* 但ArrayList集合的长度是可以随意变化的
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2011.10.17.png)
* 对于ArrayList来说，有一个尖括号<E>代表泛型
* 泛型，也就是装在集合当中的所有元素，全都是统一的任意类型
* 注意，::泛型只能是引用类型，不是基本类型::
（8种基本类型：byte short int long float double boolean char）

![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2011.13.41.png)
tips：从jdk1.7+开始，右边的<>内可以不写内容
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/F4CA7E31-736A-4EC9-AC8C-B59AA99C2330.png)

* 注意：对于ArrayList集合来说，直接打印得到的不是地址值，而是内容
		如果内容是空，得到的是空的中括号[]

# 7.9 ArrayList集合的常用方法和遍历
常用的一定要背下，不常用的可以查阅api文档

常用方法有：
	* public boolean *add*(E e)，向集合当中添加元素，参数的类型和泛型一致（布尔表示添加的动作是否成功）
	【对于ArrayList集合来说，添加动作是一定成功的，但是对于其他集合来说，添加动作不一定会成功】

	* public E *get*(int index)，从集合当中读取元素，参数是索引编号，返回值就是对应位置的元素

	* public E *remove*(int index)，从集合当中删除元素，参数是缩影编号，返回值就是被删除掉的元素

	* public int *size*()，获取集合的尺寸长度，返回值是集合中包含的元素个数

#  7.10 ArrayList集合存储基本数据类型
如果希望像集合ArrayList当中存储基本类型，必须使用基本类型对应的*包装类*
包装类就是把基本数据类型包装成对象
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-01-13%2011.33.06.png)
![](7%20Scanner%E7%B1%BB%E3%80%81Random%E7%B1%BB%E3%80%81ArrayList%E7%B1%BB/BDDA7FCF-ADBE-4F74-BE6F-E32A8E25DD57.png)

从JDK1.5+开始，支持自动装箱、自动拆箱
自动装箱：基本类型 -> 包装类型
自动拆箱：包装类型 -> 基本类型
 








