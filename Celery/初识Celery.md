# 初识Celery

## Celery 是什么
Celery 是一个 基于python开发的分布式异步消息任务队列，通过它可以轻松的实现任务的异步处理， 如果你的业务场景中需要用到异步任务，就可以考虑使用celery。

举几个实例场景中可用的例子:
* **异步任务**：将耗时的操作任务提交给Celery去异步执行，比如发送短信/邮件、消息推送、音频处理等等
* **定时任务**：比如每天定时执行爬虫爬取指定内容
* 还可以使用celery实现简单的分布式爬虫系统等等
> 所以Celery适合用于Web应用。当用户触发的一个操作需要较长时间才能执行完成时，可以把它作为任务交给Celery去异步执行，执行完再返回给用户。这段时间用户不需要等待，提高了网站的整体吞吐量和响应时间。  

## Celery的优点
* **简单**：Celery 易于使用和维护，并且它 不需要配置文件 ，并且配置和使用还是比较简单的（后面会讲到配置文件可以有）
* **高可用**：当任务执行失败或执行过程中发生连接中断，celery 会自动尝试重新执行任务
* **快速**：单个 Celery 进程每分钟可处理数以百万计的任务，而保持往返延迟在亚毫秒级
* **灵活**： Celery 几乎所有部分都可以扩展或单独使用，各个部分可以自定义。

## Celery 架构
Celery的组件包括：
1. **Celery Beat**：**任务调度器**，Beat进程会读取配置文件的内容，周期性地将配置中到期需要执行的任务发送给**任务队列**。

2. **Celery Worker**：**执行任务的消费者**，或叫**任务执行单元**，通常会在多台服务器运行多个消费者来提高执行效率。

3. **Broker**：**消息代理**，或者叫作**消息中间件**，接受任务生产者发送过来的任务消息，存进队列再按序分发给任务消费方（通常是消息队列或者数据库）。

4. **Producer**：调用了Celery提供的API、函数或者装饰器而产生任务并交给任务队列处理的都是任务生产者。

5. **Result Backend**：任务处理完后保存状态信息和结果，以供查询。Celery默认已支持Redis、RabbitMQ、MongoDB、Django ORM、SQLAlchemy等方式。

Celery的执行流程如下：
![](%E5%88%9D%E8%AF%86Celery/DFB82761-014F-4637-90EF-55D6C1372227.png)

### 什么是任务队列

任务队列一般用于线程或计算机之间分配工作的一种机制。

任务队列的输入是一个称为任务的工作单元，有专门的职程（Worker）进行不断的监视任务队列，进行执行新的任务工作。

Celery 通过消息机制进行通信，通常使用中间人（Broker）作为客户端和职程（Worker）调节。启动一个任务，客户端向消息队列发送一条消息，然后中间人（Broker）将消息传递给一个职程（Worker），最后由职程（Worker）进行执行中间人（Broker）分配的任务。

Celery 可以有多个职程（Worker）和中间人（Broker），用来提高Celery的高可用性以及横向扩展能力。


- - - -
## 学习过程中遇到的问题整理
### @task与@shared_task的区别
当我们使用@app.task装饰器定义我们的异步任务时，那么这个任务依赖于根据项目名myproject生成的Celery实例。
```
app = Celery('myproject')
 
 
@app.task(bind=True)
def debug_task(self):
    print('Request: {0!r}'.format(self.request))
```
然而我们在进行Django开发时为了保证每个app的可重用性，我们经常会在每个app文件夹下编写异步任务，这些任务并不依赖于具体的Django项目名。使用@shared_task 装饰器能让我们避免对某个项目名对应Celery实例的依赖，使app的可移植性更强。
```
from __future__ import absolute_import
from celery import shared_task
 
 
@shared_task
def add(x, y):
    return x + y
```
> bind = True的作用是绑定任务  
> 	一个绑定任务意味着任务函数的第一个参数总是任务实例本身(**self**)  

- - - -
参考：
[Celery 初次使用 - Celery 中文手册](https://www.celerycn.io/ru-men/celery-chu-ci-shi-yong)








