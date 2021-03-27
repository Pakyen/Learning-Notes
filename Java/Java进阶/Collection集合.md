# Collection集合
## 集合概述
我们已经知道一种集合ArrayList<E>，那么集合到底是什么呢？
* 集合：集合是Java提供的一种容器，可以用来存储多个数据

集合和数组都是容器，那它们的区别是什么？
* 数组的长度是固定的，集合的长度是可变的
* 数组中存储的是同一类型的元素，可以存储基本数据类型值
* ::集合存储的都是对象，而且对象的类型可以不一致::。在开发中一般当对象多的时候，就用集合进行存储

## 集合框架
JAVASE提供了满足各种需求的API，在使用这些API前，先了解其继承与接口操作架构，才能了解何时采用哪个类，以及类之间如何彼此合作，从而达到灵活应用。
* 集合按照其存储结构可以分为两大类，分别是
	1. 单列集合`java.util.Collection`和
	2. 双列集合`java.util.Map`

* Collection: 单列集合类的根接口，用于存储一系列符合某种规则的元素，它有两个重要的子接口，分别是
	1. `java.util.List`和
		* List的特点是：元素::有序::，元素::可以重复::
		* List接口的主要实现类有`java.util.ArrayList`和`java.util.LinkedList`、Vector集合

	2. `java.util.Set`
		* Set的特点是：元素::无序::，元素::不可重复::
		* Set接口的主要实现类有`java.util.HashSet`和`java.util.TreeSet`
> 总结：集合分为单列集合和双列集合，单列集合是Collection，双列集合是Map  
> Collection有两个重要的子接口，分别是List和Set  
> List接口的实现有ArrayList和LinkedList  
> Set接口的实现有HashSet和TreeSet  

## Collection常用功能
Collection是所有单列集合的父接口，因此在Collection中定义了单列集合(List和Set)通用的一些方法，这些方法可用于操作所有的单列集合。方法如下：

> Collection的子接口有：List:【ArrayList，LinkedList】，Set:【HashSet，TreeSet】，它们都能使用这些方法  

* public boolean `add(E e)`： 把给定的对象添加到当前集合中 。
* public void `clear()` :清空集合中所有的元素。
* public boolean `remove(E e)`: 把给定的对象在当前集合中删除。
* public boolean `contains(E e)`: 判断当前集合中是否包含给定的对象。
* public boolean `isEmpty()`: 判断当前集合是否为空。
* public int `size()`: 返回集合中元素的个数。
* public Object[] `toArray()`: 把集合中的元素，存储到数组中。

### 方法演示
```
import java.util.ArrayList;
import java.util.Collection;

public class Demo1Collection {
    public static void main(String[] args) {
		// 创建集合对象 
    	// 使用多态形式
    	Collection<String> coll = new ArrayList<String>();
    	// 使用方法
    	// 添加功能  boolean  add(String s)
    	coll.add("小李广");
    	coll.add("扫地僧");
    	coll.add("石破天");
    	System.out.println(coll);

    	// boolean contains(E e) 判断o是否在集合中存在
    	System.out.println("判断  扫地僧 是否在集合中"+coll.contains("扫地僧"));

    	//boolean remove(E e) 删除在集合中的o元素
    	System.out.println("删除石破天："+coll.remove("石破天"));
    	System.out.println("操作之后集合中元素:"+coll);
    	
    	// size() 集合中有几个元素
		System.out.println("集合中有"+coll.size()+"个元素");

		// Object[] toArray()转换成一个Object数组
    	Object[] objects = coll.toArray();
    	// 遍历数组
    	for (int i = 0; i < objects.length; i++) {
			System.out.println(objects[i]);
		}

		// void  clear() 清空集合
		coll.clear();
		System.out.println("集合中内容为："+coll);
		// boolean  isEmpty()  判断是否为空
		System.out.println(coll.isEmpty());  	
	}
}
```

## 总结

![](Collection%E9%9B%86%E5%90%88/%E6%88%AA%E5%B1%8F2021-02-24%2021.21.47.png)