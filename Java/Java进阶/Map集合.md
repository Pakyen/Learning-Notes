# Map集合
> Map接口取代了Dictionary类，虽然Dictionary类完全是一个抽象类，不是接口  
`public interface Map<K,V>`（key, value）
* 一个映射不能包含重复的键(key)，每个键只能映射到一个值(value)

## Map的特点：
	1. 键是唯一的（key是不允许重复的，值可以重复）
	2. 键和值一一对应，一个键对应一个值
	3. 靠键维护它们的关系
	4. Map集合是一个双列集合，一个元素包含两个值（key,value）
	5. Map集合中的元素，key和value的数据类型可以相同，也可以不同

## Map接口的常用实现类
1. `HashMap<K,V> `：存储数据采用的**哈希表**结构，元素的**存取顺序不能保证一致**。由于要保证键的唯一、不重复， ~需要重写键的hashCode()方法、equals()方法~
2. `LinkedHashMap<K,V>`：存储数据采用的**哈希表结构+链表结构**。通过 ~链表结构可以保证元素的存取顺序一致~ ； ~通过哈希表结构可以保证的键的唯一、不重复~ ， ~需要重写键的hashCode()方法、equals()方法~ 。
- - - -
`java.util.HashMap<k,v> implements Map<k,v>`
`java.util.LinkedHashMap<k,v> extends HashMap<k,v>`
### HashMap集合
* 特点：
	1. HashMap集合的底层是**哈希表**结构：查询的速度特别快
		JDK1.8之前：数组+单向链表
		JDK1.8之后：数组+单向链表/红黑树(链表长度超过8时)：提高查询速度
	2. HashMaP集合是一个**无序**的集合，存取元素的顺序可能不一致

### LinkedHashMap
* 特点：
	1. LinkedHashMap集合的底层是**哈希表+链表**（保证迭代的顺序）
	2. LinkedHashMap集合是一个有序的集合，元素存取顺序一致

> tips：Map接口中的集合都有两个泛型变量<K,V>,在使用时，要为两个泛型变量赋予数据类型。两个泛型变量<K,V>的数据类型可以相同，也可以不同  

## Map接口中的常用方法：
![](Map%E9%9B%86%E5%90%88/18A94FAF-3736-41C9-8099-3BB9027DEB33.png)
> 使用put方法时，若指定的键(key)在集合中没有，则没有这个键对应的值，返回null，并把指定的键值添加到集合中；   
> 若指定的键(key)在集合中存在，则**返回值**为集合中键对应的值（**该值为替换前的值**）， ~并把指定键所对应的值，替换成指定的新值~ 。   

## Map的遍历方式
1. 通过键找值：
	1. 获取Map中所有的键，由于键是唯一的，所以返回一个Set集合存储所有的键。方法提示:`keyset()`
	2. 遍历键的Set集合，得到每一个键（迭代器）`Iterator<E> it = set.iterator();`
	3. 根据键，获取键对应的值 `get(K key)`

2. 通过键值对对象Entry遍历
	1. 获取Map集合中，所有的键值对(Entry)对象，以Set集合形式返回。方法提示:`entrySet()`
	2. 遍历包含键值对(Entry)对象的Set集合，得到每一个键值对(Entry)对象
	3. 通过键值对(Entry)对象，获取Entry对象中的键与值。 方法提示:`getkey()` `getValue()`

## Entry对象：键值对对象
`Map.Entry<K,V>`：Map接口中有一个内部接口Entry（项）
> Entry将键值对的对应关系封装成了对象，即键值对对象  
> 这样我们在遍历Map集合时，就可以从每一个键值对（Entry）对象中获取对应的键与对应的值  
![](Map%E9%9B%86%E5%90%88/39595810-2B67-4D8F-A640-9B953E2CE91C.png)

## HashMap存储自定义类型键值
* 当给HashMap存储自定义类型的key时，如果这个key是对象，就要保证对象唯一，那就必须重写对象的hashCode()和euqals方法()
* 如果要保证map中key的存取顺序一致，可以使用java.util.LinkedHashMap集合来存放
> 源码参见：  
<a href='day04%20%E3%80%90Map%E3%80%91.md'>day04 【Map】.md</a>
![](Map%E9%9B%86%E5%90%88/%E6%88%AA%E5%B1%8F2021-02-25%2021.08.40.png)

## LinkedHashMap
`java.util.LinkedHashMap<k,v> extends HashMap<k,v>`
* 有序的集合，继承自HashMap
* 底层：哈希表+链表（记录元素的顺序）
* 定义格式：
` LinkedHashMap<String, String> map = new LinkedHashMap<String, String>();`

## Hashtable集合

 `java.util.Hashtable<K,V>集合 implements Map<K,V>接口`

* Hashtable:底层也是一个**哈希表**,是一个::线程安全::的集合,是**单线程集合**,::速度慢::
> 线程安全的，是同步的，所以速度慢  
* HashMap:底层是一个哈希表,是一个线程不安全的集合,是多线程的集合,速度快

* HashMap集合(之前学的所有的集合):可以存储null值,null键
* Hashtable集合,::不能存储null值,null键::

* ~Hashtable和Vector集合一样,在JDK1.2版本之后被更先进的集合(HashMap,ArrayList)取代了~

> *ArrayList取代Vector*  
> *Hashmap取代了Hashtable*   

* Hashtable的子类Properties依然活跃在历史舞台
* Properties集合是一个唯一和IO流相结合的集合
