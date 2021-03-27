# Collections集合工具类

## 常用功能
 `java.utils.Collections` 是集合工具类，用来对集合进行操作。部分方法如下：
### `public static <T> boolean addAll(Collection<T> c, T… elements)` 
	往集合中添加一些元素

### `public static void shuffle(List<?> list)` 
	打乱集合顺序
- - - -
### `public static <T> void sort(List<T> list) `
	* 将集合中元素按照默认规则排序(**默认升序**)，只能放list不能放set

* sort(List<T> list)的使用前提
	* 被排序的集合里存储的元素，必须实现Comparable，重写接口中的方法CompareTo定义排序的规则
 ![](Collections%E9%9B%86%E5%90%88%E5%B7%A5%E5%85%B7%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-02-25%2014.23.58.png)
 ![](Collections%E9%9B%86%E5%90%88%E5%B7%A5%E5%85%B7%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-02-25%2014.23.37.png)
* Comparable的排序规则：
	* 自己(this)-参数：升序
	* 参数-自己(this)：降序

### `public static <T> void sort(List<T> list，Comparator<? super T> )`

* Comparator和Comparable的区别
	* Comparable：自己（this）和别人（参数）比较，自己需要实现Comparable接口，重写比较的规则
	* Comparator：也是一个接口，相当于找一个第三方裁判来比较
![](Collections%E9%9B%86%E5%90%88%E5%B7%A5%E5%85%B7%E7%B1%BB/%E6%88%AA%E5%B1%8F2021-02-25%2014.27.43.png)
	* Comparator排序规则：
		* o1-o2：升序
		* o2-o1：降序

## 简述Comparable和Comparator两个接口的区别。
**Comparable**：强行对实现它的每个类的对象进行整体排序。这种排序被称为类的自然排序，类的compareTo方法被称为它的自然比较方法。只能在类中实现compareTo()一次，不能经常修改类的代码实现自己想要的排序。实现此接口的对象列表（和数组）可以通过Collections.sort（和Arrays.sort）进行自动排序，对象可以用作有序映射中的键或有序集合中的元素，无需指定比较器。
**Comparator**强行对某个对象进行整体排序。可以将Comparator 传递给sort方法（如Collections.sort或 Arrays.sort），从而允许在排序顺序上实现精确控制。还可以使用Comparator来控制某些数据结构（如有序set或有序映射）的顺序，或者为那些没有自然顺序的对象collection提供排序。