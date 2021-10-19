# 信号机制 signal

django自带一套信号机制来帮助我们**在框架的不同位置之间传递信息**。也就是说，当某一事件发生时，信号系统可以允许一个或多个发送者（senders）将通知或信号（signals）发送给一组接受者（receivers）。

### 信号系统的组成
信号系统包含以下三要素：
* 发送者－信号的发出方
* 信号－信号本身
* 接收者－信号的接受者

### 常用方法
1. **post_save** && **pre_save**
post_save在ORM模型的save()方法调用之后或之前发送信号
`django.db.models.signals.pre_save `
`django.db.models.signals.post_save`

2. **pre_delete** && **post_delete**
post_delete在ORM模型或查询集的delete()方法调用之前或之后发送信号。
`django.db.models.signals.pre_delete` 
`django.db.models.signals.post_delete`

3. **m2m_changed**
m2m_changed当多对多字段被修改时发送信号。
`django.db.models.signals.m2m_changed`

4. **request_started** && **request_finished** 
request_finished当接收和关闭HTTP请求时发送信号。
`django.core.signals.request_started` 
`django.core.signals.request_finished`


## 一、监听信号
要接收信号，请使用`Signal.connect()`方法**注册一个接收器**。当信号发送后，会调用这个接收器。

方法原型：
`Signal.connect(receiver, sender=None, weak=True, dispatch_uid=None)`
参数：
```
receiver ：当前信号连接的回调函数，也就是处理信号的函数。 
sender ：指定从哪个发送方接收信号。 
weak ： 是否弱引用
dispatch_uid ：信号接收器的唯一标识符，以防信号多次发送。
```

> ps: 在实际使用中，其实经常会使用@receiver装饰器来定义信号连接的回调函数，那么@receiver装饰器和signal.connect方法有什么区别呢？  
>   
> 在功能上他们其实是做了相同的事情，@receiver是Signal.connect()的一个thin wrapper，唯一的区别是，@receiver不仅可以接受单个信号，还可以接受信号的列表或元组，它将把函数连接到每个信号上。  
>   
> 通过查看@receiver的源码：  
```
 def receiver(signal, **kwargs):
    """
    A decorator for connecting receivers to signals. Used by passing in the
    signal (or list of signals) and keyword arguments to connect::

        @receiver(post_save, sender=MyModel)

        def signal_receiver(sender, **kwargs):
            ...
        @receiver([post_save, post_delete], sender=MyModel)

        def signals_receiver(sender, **kwargs):
            ...
    """

    def _decorator(func):
        if isinstance(signal, (list, tuple)):
            for s in signal:
                s.connect(func, **kwargs)
        else:
            signal.connect(func, **kwargs)
        return func
    return _decorator
```
> 我们可以看到receriver实际上只是简单的通过函数调用`signal.connect( func )`并返回原始函数  


下面以如何接收每次HTTP请求结束后发送的信号为例，连接到Django内置的request_finished信号。
### 1. 编写接收器
接收器其实就是一个Python函数或者方法：
```
def my_callback(sender, **kwargs):
    print(“Request finished!”)
```
注意，所有的接收器都必须接收一个`sender`参数和一个`**kwargs` 通配符参数。

### 2. 连接信号
其实就是监听信号。有两种方法可以连接信号，一种是下面的手动方式：
```
from django.core.signals import request_finished

request_finished.connect(my_callback)
```

另一种是使用receiver()装饰器：
```
from django.core.signals import request_finished
from django.dispatch import receiver

@receiver(request_finished)
def my_callback(sender, **kwargs):
    print("Request finished!")
```

### 接收特定发送者的信号
一个信号接收器，通常不需要接收所有的信号，只需要接收特定发送者发来的信号，所以需要在sender参数中，指定发送方。下面的例子，只接收MyModel模型的实例保存前的信号。
```
from django.db.models.signals import pre_save   # 另外一个内置的常用信号
from django.dispatch import receiver
from myapp.models import MyModel


@receiver(pre_save, sender=MyModel)
def my_handler(sender, **kwargs):
    ...
```
my_handler函数只在MyModel实例保存时被调用。

### 4. 防止重复信号
为了防止重复信号，可以设置dispatch_uid参数来标识你的接收器，标识符通常是一个字符串，如下所示：
```
from django.core.signals import request_finished

request_finished.connect(my_callback, dispatch_uid="my_unique_identifier")
```
最后的结果是，对于每个唯一的dispatch_uid值，你的接收器都只绑定到信号一次。


## 二、自定义信号
除了Django为我们提供的内置信号（比如前面列举的那些），很多时候，我们需要自己定义信号。
所有的信号都是`django.dispatch.Signal`的实例。
下面定义了一个新信号：
```
import django.dispatch

pizza_done = django.dispatch.Signal()
```


## 三、发送信号
Django中有两种方法用于发送信号。
* `Signal.send(sender, **kwargs)`
或者
* `Signal.send_robust（sender，** kwargs）`
必须提供sender参数（大部分情况下是一个类名），并且可以提供任意数量的其他关键字参数。


## 四、断开信号
```
Signal.disconnect(receiver=None, sender=None, dispatch_uid=None)
```
`Signal.disconnect()`用来断开信号的接收器，和Signal.connect()中的参数相同。如果接收器成功断开，返回True，否则返回False。


## 五、信号实例








