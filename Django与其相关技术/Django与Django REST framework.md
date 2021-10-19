# Django与Django REST framework


Django和DjangoRest框架还是有一定区别。
Django主要有MVC和MTV的设计模式，DjangoRest是在Django的基础上封装了Restful接口的功能。比如在Django中，我们可能需要自己写Get Post等很复杂的请求，但是在DjangoRest中，DjangoRest为我们构建Web API提供了许多灵活的方法。

## Rest API 架构
![](Django%E4%B8%8EDjango%20REST%20framework/6B23734B-774A-4410-8123-3B35D38AEBEC.png)
各个组件说明：
Url Patterns, 将请求路由到具体处理的视图；
View，处理HTTP请求并返回HTTP Response；
Serializer，序列化/反序列化模型数据；
Models，模型，负责与数据库相关操作；
与常用的的Django应用相比，Restful风格接口仅仅是多使用了Serializer组件。
