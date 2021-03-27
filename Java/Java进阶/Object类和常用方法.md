# Object类和常用方法

## 介绍
* java.lang.Object类是Java语言中的根类，即所有类的父类
* 每个都是用Object类作为超类，所有对象（包括数组）都实现这个类的方法

## 常用方法
Object类里定义的方法所有的对象都能使用
### toString()
![](Object%E7%B1%BB%E5%92%8C%E5%B8%B8%E7%94%A8%E6%96%B9%E6%B3%95/%E6%88%AA%E5%B1%8F2021-02-19%2010.24.25.png)
但是打印地址值往往没什么意义，所有有时候需要重写Object类的toString方法
(在idea中使用Alt+Insert可以直接选择自动重写toString)
	* 要查看一个类是否重写了toString方法，直接打印这个类的对象的名字即可
		* 如果没有重写toString方法，打印的就是地址值(默认)
		* 如果重写了toString方法，那么就按照重写的方式打印
### equals()
比较两个对象是否相等
* 源码：
```
public boolean qeuals(Object obj){
	return (this == obj);
}
```
	* 这里是比较两个对象是否相等(地址值)	
	* 基本数据类型：比较的是值，
	* 引用数据类型：比较的是两个对象的地址值
 
Object类的equals方法默认比较的是两个对象的地址值，没有意义
所以我们需要重写equals方法，比较两个对象的属性值
	* 问题：隐含着一个多态（父类引用子类对象）
		* 多态的弊端：无法使用子类特有的内容，只能使用父类的方法
		* 解决办法：可以使用向下转型（强转）把Object类型转换为子类
		* 如有自定义类Person，有成员变量name 
```
// 重写equals方法		
public boolean equals(Object obj){
		Person p = (Person)obj;
		boolean b = this.name.equals(p.name);
		return b;
}
```
	* 可以增加两个判断提交运行效率
		* if(obj==null || getClass()!= obj.getClass()) return false;
		* if(obj==this) return true;
	* 当然idea中使用Alt+Insert也有equals方法的重写

### 避免空指针异常 equals(o1, o2)
null是不能调用方法的，会抛出空指针异常
![](Object%E7%B1%BB%E5%92%8C%E5%B8%B8%E7%94%A8%E6%96%B9%E6%B3%95/FC8236C4-50B8-4AA0-B8C3-C88DE164802A.png)
equals(o1,o2)对两个对象进行比较，可以避免空指针异常，因为它的源码里相对于equals(obj)加入了对空值的判断！
equals(o1,o2)源码：
```
public static boolean equals(Object a, Object b){
	return a==b || (a != null && a.equals(b));
}
// 如果传入的对象是空，那么直接 a==b进行比较
// 如果不是空，那么再比较两个对象
```