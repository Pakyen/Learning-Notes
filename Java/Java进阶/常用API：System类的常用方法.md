# 常用API：System类的常用方法
## 介绍
`java.lang.System`类中提供了大量的静态方法，可以获取与系统相关的信息或系统级操作，在System类的API文档中，常用的方法有：
	* `public static long currentTimeMillis()`:返回当前时间的毫秒值
> 	Millis表示millisecond，毫秒  
	* `public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)`：将数组中指定的数据（一般是源数组的一部分）拷贝到另一个数组中
	
## currentTimeMillis()方法
这个方法可以用来测试程序的效率：
	1. 程序执行前，获取一次毫秒值
	2. 程序执行后，获取一次毫秒值
```
long s = System.currentTimeMillis()
//CODE SNIPPET
long e = System.currentTimeMillis()
//然后查看(e-s)即可
```
## arraycopy(…)方法
参数：
	* src：源数组
	* srcPos：源数组中的起始位置
	* dest：目标数组
	* destPost：目标数组中的起始位置
	* Length：要复制的数组元素的数量
```
int[] src = {1,2,3,4,5};
int[] dest = {6,7,8,9,10};
//将src数组的前三个元素复制到dest数组的前三个位置上
System.arraycopy(src,0,dest,0,3);
//复制后dest为：[1,2,3,9,10]
```

