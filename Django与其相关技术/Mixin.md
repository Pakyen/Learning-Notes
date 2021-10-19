# Mixin

Mixin 技术是面向对象编程的一个重要技能。通常它是实现了某种功能单元的类，用于被其他子类继承，将功能组合到子类中。利用 Python 的多重继承，子类可以继承不同功能的 Mixin 类，按需动态组合使用。当多个类都实现了同一种功能时，这时应该考虑将该功能抽离成 Mixin 类。

在实现一个功能时，我们只需根据需要的 Mixin 搭积木就好了，特别的方便。在我们的应用中，我们将会大量的使用 Mixin 技术，并且，Mixin 技术在 Django 中也被频繁的应用。所以掌握 Mixin 是很必要的。
Mixin 技术能够产生，还是得益于对象的可继承性。

比如我有一个对象叫做 `Man`:
```
class Man:
    def __init__(self,name, age, height):
        self.name = name
        self.age = age
        self.height = height
```
我想让他会抽烟，于是我写了个抽烟 `Mixin`:
```
class SmokeMixin:
    def smoke(self):
        print('饭后一支烟，赛过活神仙！')
```
我还想让他会挣钱，于是写了个挣钱 `Mixin`：
```
class MakeMoneyMixin:
    def make_money(self):
        print('面向工资编程中')
```
我想让他有个老婆：
```
class WifeMixin:
    wife = None
    def wife(self):
        return '我的老婆叫{}'.format(self.wife)
```
最后，这个 Man 成了这样的人：
```
class MyMan(SmokeMixin, WifeMixin, MakeMoneyMixin):
    wife = '王翠花' 

my_man = MyMan(name='老王',age=30,height=170)
my_man.smoke() # 饭后一支烟，赛过活神仙！
my_man.make_money() # 面向工资编程中
my_man.wife() # 我的老婆叫王翠花
```
但是每个男人都是不同的，因为有的男人也单身：
```
class SingleMan(SmokeMixin, MakeMoneyMixin):
    pass
sig_man = SingleMan(name='老李',age=25,height=165)
sig_man.smoke() # 饭后一支烟，赛过活神仙！
sig_man.make_money() # 面向工资编程
```
这样我们就可以创造出很多个不同的男人，他们有相同点，也有不同点，我们要做的仅仅是把 Mixin 积木搭上去。我们的第二个男人的定义甚至只有一个 pass 。这种写法在大型工程里非常常见，只有一个 pass ，但是却有着丰富的功能。项目的视图就可以使用 Mixin 技术：
```
class APICodeView(APIListMixin,  # 获取列表
                  APIDetailMixin,  # 获取当前请求实例详细信息
                  APIUpdateMixin,  # 更新当前请求实例
                  APIDeleteMixin,  # 删除当前实例
                  APICreateMixin,  # 创建新的的实例
                  APIMethodMapMixin,  # 请求方法与资源操作方法映射
                  APIView):  # 记得在最后继承 APIView
    model = CodeModel  # 传入模型

    def list(self):  # 这里仅仅是简单的给父类的 list 函数传参。
        return super(APICodeView, self).list(fields=['name'])
```