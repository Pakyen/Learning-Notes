# 解决线程安全问题：同步代码块、同步方法、Lock锁

卖票案例出现了线程安全问题，卖出了不存在的票和重复的票
<a href='Demo01Ticket.java'>Demo01Ticket.java</a>
<a href='RunnableImpl(1).java'>RunnableImpl(1).java</a>
解决线程安全问题的方法：
1. 同步代码块
2. 同步方法
3. Lock 锁
## 同步代码块（解决线程安全问题的第一种方法）
* 解决线程安全问题的一种方案:使用同步代码块
    * 格式:
```
synchronized(锁对象){
    可能会出现线程安全问题的代码(访问了共享数据的代码)
}
```

* 注意:
				1. 通过代码块中的锁对象,可以使用任意的对象
				2. 但是必须保证多个线程使用的锁对象是同一个
				3. 锁对象作用:
		::把同步代码块锁住,只让一个线程在同步代码块中执行::
```
public class RunnableImpl implements Runnable{
    //定义一个多个线程共享的票源
    private  int ticket = 100;

    //创建一个锁对象
    Object obj = new Object();

    //设置线程任务:卖票
    @Override
    public void run() {
        //使用死循环,让卖票操作重复执行
        while(true){
           //同步代码块
            synchronized (obj){
                //先判断票是否存在
                if(ticket>0){
                    //提高安全问题出现的概率,让程序睡眠
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    //票存在,卖票 ticket--
                    System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                    ticket--;
                }
            }
        }
    }
}
```

### 同步技术的原理
使用了一个**锁对象**，这个锁对象叫**同步锁**，也叫**对象锁**，也叫**对象监视器**
![](%E8%A7%A3%E5%86%B3%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98%EF%BC%9A%E5%90%8C%E6%AD%A5%E4%BB%A3%E7%A0%81%E5%9D%97%E3%80%81%E5%90%8C%E6%AD%A5%E6%96%B9%E6%B3%95%E3%80%81Lock%E9%94%81/%E6%88%AA%E5%B1%8F2021-03-04%2009.36.02.png)

![](%E8%A7%A3%E5%86%B3%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98%EF%BC%9A%E5%90%8C%E6%AD%A5%E4%BB%A3%E7%A0%81%E5%9D%97%E3%80%81%E5%90%8C%E6%AD%A5%E6%96%B9%E6%B3%95%E3%80%81Lock%E9%94%81/2D659921-E3B4-48E1-AAFE-FFBF6B0F8E0B.png)

- - - -
## 同步方法（解决线程安全问题的第二种方法）
解决线程安全问题的二种方案:使用同步方法
* 使用步骤:
				1. ::把访问了共享数据的代码抽取出来,放到一个方法中::
				2. ::在方法上添加synchronized修饰符::

* 格式: 定义方法的格式
```
修饰符 synchronized 返回值类型 方法名(参数列表){
    可能会出现线程安全问题的代码(访问了共享数据的代码)
}

public synchronized void payTicket(){
        //先判断票是否存在
        if(ticket>0){
            //提高安全问题出现的概率,让程序睡眠
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            //票存在,卖票 ticket--
            System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
            ticket--;
}
```
<a href='RunnableImpl.java'>RunnableImpl.java</a>

* 同步方法也会把方法内部的代码锁住，只让一个线程执行
* 同步方法的锁对象是谁?
	* ::就是实现类对象:: new RunnableImpl() ，也是就是::this::

所以我们也可以把方法中的synchronized去掉，然后在方法里加上同步代码块，锁对象使用this（实现类对象）
```
public void payTicket(){
    synchronized  (this){
        //先判断票是否存在
        if(ticket>0){
            //提高安全问题出现的概率,让程序睡眠
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            //票存在,卖票 ticket--
            System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
            ticket--;
        }
}
```

### 静态的同步方法
```
public class RunnableImpl implements Runnable{
public static int ticket = 100; //变量也得是静态的，静态访问静态
public static synchronized void payTicketStatic(){
            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                //票存在,卖票 ticket--
                System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                ticket--;
            }

}}
```
注意：
::静态的同步方法的锁对象不能使用this::，因为this是创建对象之后产生的，而静态方法优先于对象
静态方法的锁对象是本类的class属性 -> class文件对象（反射）


所以上面的静态的同步方法也可以改写为同步代码块的形式（在一个普通静态方法内）：
```
public class RunnableImpl implements Runnable{
public static int ticket = 100; //变量也得是静态的，静态访问静态
public static void payTicketStatic(){
        synchronized (RunnableImpl.class){
            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                //票存在,卖票 ticket--
                System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                ticket--;
            }
        }

}}
```

## Lock 锁（接口）（解决线程安全问题的第三种方式） 
`java.util.concurrent.locks`，Lock接口实现提供了比使用synchronized方法和语句可获得更广泛的锁定操作

第三种解决线程安全问题的方式：使用Lock锁

* Lock接口中的方法：
	1. `void lock()`     获取锁
	2. `void unlock()` 释放锁

* 使用步骤
	1. 在成员位置创建一个`ReentrantLock对象`
	2. 在可能会出现安全问题的代码**前**调用Lock接口中的方法lock获取锁
	3. 在可能会出现安全问题的代码**后**调用Lock接口中的方法unlock释放锁

```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
public class RunnableImpl implements Runnable{
    //定义一个多个线程共享的票源
    private  int ticket = 100;

    //1.在成员位置创建一个ReentrantLock对象
    Lock l = new ReentrantLock();
    /*//设置线程任务:卖票
    @Override
    public void run() {
        //使用死循环,让卖票操作重复执行
        while(true){
           //2.在可能会出现安全问题的代码前调用Lock接口中的方法lock获取锁
           l.lock();

            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                //票存在,卖票 ticket--
                System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                ticket--;
            }

            //3.在可能会出现安全问题的代码后调用Lock接口中的方法unlock释放锁
            l.unlock();
        }
    }*/
}
```
* ::lock有个更好的的写法::，就是上面的代码中，把 ::l.unlock()::放在了try…catch的::finnaly::中，这样无论程序是否异常,都会把锁释放
```
	  //1.在成员位置创建一个ReentrantLock对象
    Lock l = new ReentrantLock();
	  //设置线程任务:卖票
    @Override
    public void run() {
        //使用死循环,让卖票操作重复执行
        while(true){
            //2.在可能会出现安全问题的代码前调用Lock接口中的方法lock获取锁
            l.lock();

            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                    //票存在,卖票 ticket--
                    System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                    ticket--;
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    //3.在可能会出现安全问题的代码后调用Lock接口中的方法unlock释放锁
                    l.unlock();//无论程序是否异常,都会把锁释放
                } 
            }
        }
    }
```




