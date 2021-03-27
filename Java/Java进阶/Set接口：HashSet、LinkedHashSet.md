# Set接口：HashSet、LinkedHashSet

`java.util.Set`接口和java.util.List接口一样，同样继承自Collection接口，它与Collection接口中的方法基本一致，并没有对Collection接口进行功能上的扩充，只是比Collection接口更加严格了。与List接口不同的是，Set接口中元素无序，并且都会以某种规则保证存入的元素不出现重复

* Set接口的特点：
	1. 不允许存储重复的元素
	2. 没有索引，没有带索引的方法，也不能使用普通的for循环遍历

> tips:Set集合取出元素的方式可以采用：迭代器、增强for  
Set集合有多个子类，这里我们介绍其中的`java.util.HashSet`、
`java.util.LinkedHashSet`这两个集合。

## 1 HashSet集合介绍
`java.util.HashSet`集合 implements Set接口

* HashSet特点：
	1. 不允许存储重复的元素
	2. 没有索引，没有带索引的方法，也不能使用普通的for循环遍历
	3. 是一个无序的集合，存储元素和取出元素的顺序可能不一致
	4. 底层是一个`哈希表结构（查询速度非常快）`

![](Set%E6%8E%A5%E5%8F%A3%EF%BC%9AHashSet%E3%80%81LinkedHashSet/%E6%88%AA%E5%B1%8F2021-02-25%2013.36.28.png)
![](Set%E6%8E%A5%E5%8F%A3%EF%BC%9AHashSet%E3%80%81LinkedHashSet/%E6%88%AA%E5%B1%8F2021-02-25%2013.37.15.png)


## 2 HashSet集合存储数据的结构（哈希表）
### 哈希值
是一个十进制的整数，由系统随机给出
> 就是对象的地址，是逻辑地址，模拟出来的，不是数据实际存储的物理地址  
> 在Object类有一个方法，可以获取对象的哈希值    
> `int hashCode()`  

### 哈希表
* jdk1.8之前，哈希表=数组+链表
* jdk1.8+，
	* 哈希表 = 数组+链表;
	* 哈希表 = 数组+红黑树（提高查询速度）

* 哈希表的特点：查询速度快
![](Set%E6%8E%A5%E5%8F%A3%EF%BC%9AHashSet%E3%80%81LinkedHashSet/%E6%88%AA%E5%B1%8F2021-02-25%2013.51.56.png)
根据哈希值，将哈希值相同的元素分为一组，所以查询快（数组本身查询就快）
如果两个元素不同，但是哈希值相同，就会产生哈希冲突，所以用链表将它们连起来。
当链表的长度>8的时候将链表转换为红黑树

![](Set%E6%8E%A5%E5%8F%A3%EF%BC%9AHashSet%E3%80%81LinkedHashSet/%E6%88%AA%E5%B1%8F2021-02-25%2013.46.25.png)
> 总而言之，**JDK1.8**引入红黑树大程度优化了HashMap的性能，那么对于我们来讲::保证HashSet集合元素的唯一，其实就是根据对象的**hashCode**和**equals**方法来决定的::  
>   
> **如果我们往集合中存放自定义的对象，那么保证其唯一，就必须复写hashCode和equals方法建立属于当前对象的比较方式**  
![](Set%E6%8E%A5%E5%8F%A3%EF%BC%9AHashSet%E3%80%81LinkedHashSet/%E6%88%AA%E5%B1%8F2021-02-25%2013.48.52.png)

## 3 HashSet存储自定义类型元素
给HashSet中存放自定义类型元素时，需要重写对象中的hashCode和equals方法，建立自己的比较方式，才能保证HashSet集合中的对象唯一
![](Set%E6%8E%A5%E5%8F%A3%EF%BC%9AHashSet%E3%80%81LinkedHashSet/%E6%88%AA%E5%B1%8F2021-02-25%2013.56.58.png)
![](Set%E6%8E%A5%E5%8F%A3%EF%BC%9AHashSet%E3%80%81LinkedHashSet/4CAC4C5F-B69D-4A1C-A183-6A2C11C81F3C.png)
![](Set%E6%8E%A5%E5%8F%A3%EF%BC%9AHashSet%E3%80%81LinkedHashSet/%E6%88%AA%E5%B1%8F2021-02-25%2013.58.28.png)


## LinkedHashSet
我们知道HashSet保证元素唯一，可是元素存放进去是没有顺序的，那么我们要保证有序，怎么办呢？
在HashSet下面有一个子类java.util.LinkedHashSet，它是链表和哈希表组合的一个数据存储结构

	* 可预知迭代顺序
	* Set接口的哈希表和链表实现
	* 与HashSet的不同之处在于，LinkedHashSet维护着一个运行于所有条目的双重链接列表，此链表底ing一了迭代顺序，按照将元素插入到set中的顺序进行迭代

`java.utils.LinkedHashSet集合 extends继承 HashSet集合`
LinkedList集合的特点：
		* 底层是哈希表（数组+链表/红黑树）+链表，多了一条链表（记录元素的存储顺序，保证元素有序）

