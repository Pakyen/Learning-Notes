# 迭代器 Iterator

## 前言
Collection接口和Set接口里面是没有索引的（虽然List有），所以不能使用普通的for循环来遍历它
但是如果对每一个集合的接口都实现遍历的方法会很麻烦，所以就有了迭代器Iterator。不管是哪个集合，都可以用迭代器取出里面的元素。

## Iterator接口
### 介绍
在程序开发中，经常需要遍历集合中的所有元素。针对这种需求，JDK专门提供了一个接口`java.util.Iterator`。Iterator接口也是Java集合中的一员，但它与Collection、Map接口有所不同，Collection接口与Map接口主要用于存储元素，而Iterator主要用于迭代访问（即遍历）Collection中的元素，因此Iterator对象也被称为迭代器。

> 迭代的概念：  
> **迭代**：即Collection集合元素的通用获取方式。 ~在取元素之前先要判断集合中有没有元素~ ，如果有，就把这个元素取出来，继续在判断，如果还有就再取出出来。一直把集合中的所有元素全部取出。这种取出方式专业术语称为迭代  

### 获取迭代器的方法
想要遍历Collection集合，那么就要获取该集合迭代器完成迭代操作，下面介绍一下获取迭代器的方法：
	* `public Iterator iterator()`: 获取集合对应的迭代器，用来遍历集合中的元素。

### Iterator接口的常用方法
	* `public boolean hasNext()`：如果仍有元素可以迭代，则返回true	
	* `public E next()`：返回迭代的下一个元素

### Iterator的使用方法
* Iterator是一个接口，无法直接使用，需要使用Iterator接口的实现类对象，获取实现类的方式比较特殊。
* Collection接口中有一个方法，较Iterator()，这个方法返回的就是迭代器的实现类对象
* `Iterator <E> iterator()` 返回此collection的元素上进行迭代的迭代器

### 迭代器的使用步骤：
	1. 使用集合中的方法iterator()获取迭代器的实现类对象，使用Iterator接口接收（多态） 
	2. 使用Iterator接口中的方法hasNext()判断还有没有下一个元素
	3. 使用Iterator接口中的方法next()取出集合中的下一个元素

> tips:：在进行集合元素取出时，如果集合中已经没有元素了，还继续使用迭代器的next方法，将会发生java.util.NoSuchElementException没有集合元素的错误  

## 迭代器的实现原理
以代码为例,
```
// 使用多态方式 创建对象
Collection<String> coll = new ArrayList<String>();

//省略添加元素部分代码
//使用迭代器 遍历   每个集合对象都有自己的迭代器
Iterator<String> it = coll.iterator();

//  泛型指的是 迭代出 元素的数据类型

while(it.hasNext()){ //判断是否有迭代元素
		String s = it.next();//获取迭代出的元素
		System.out.println(s);
}
```
![](%E8%BF%AD%E4%BB%A3%E5%99%A8%20Iterator/%E6%88%AA%E5%B1%8F2021-02-24%2021.43.26.png)
迭代器的指针从-1开始，其实每次迭代只做两件事情：
	1. 判断有没有下一个元素，有的话就移动到下一位，取出下一个元素
	2. 把指针向后移动一位
	
## 增强for循环
增强for循环(也称for each循环)是**JDK1.5**以后出来的一个高级for循环， ~专门用来遍历数组和集合的~ 。它的::内部原理其实是个Iterator迭代器，所以在遍历的过程中，不能对集合中的元素进行增删操作::

> Collection扩展了一个Iterable接口  
> public interface Collection<E>extends Iterable<E>：  
> 		所有的单列集合都可以使用增强for（foreach）  
>    
> 这个接口`public interface Iterable<T>`的作用就是：  
> 实现这个接口允许对象成为“foreach”语句的目标  

格式：
```
for(元素的数据类型  变量 : Collection集合or数组){ 
  	//操作代码
		//内部原理是迭代器，所以遍历过程中，不能对集合中的元素进行增删操作
}
```

### 使用
1. 遍历数组
```
int[] arr = {3,5,6,87};
for(int a : arr){
		System.out.println(a);
```

2. 遍历集合
```
Collection<String> coll = new ArrayList<String>();
coll.add("aa");
coll.add("bb");
coll.add("cc");

for(String s :coll){
   System.out.println(s);}
```

* 注意事项：
	* for-each必须有被遍历的目标
	* 目标只能是数组或集合
	* foreach仅仅只能作为遍历操作出现



## 总结
![](%E8%BF%AD%E4%BB%A3%E5%99%A8%20Iterator/%E6%88%AA%E5%B1%8F2021-02-24%2022.02.18.png)
![](%E8%BF%AD%E4%BB%A3%E5%99%A8%20Iterator/%E6%88%AA%E5%B1%8F2021-02-24%2022.03.22.png)
![](%E8%BF%AD%E4%BB%A3%E5%99%A8%20Iterator/A767394A-6C1F-41A4-8294-55B075176825.png)



