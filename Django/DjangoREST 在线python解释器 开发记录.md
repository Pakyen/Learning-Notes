# DjangoREST 在线python解释器 开发记录
## 后端开发
### 新建项目
在终端中新建一个项目：
```
python django-admin.py startproject online_interpreter_project
```
切换到项目目录，创建app：
```
python manage.py startapp online_interpreter_app
```
同时，将不需要的文件删除，并且再新建几个空文件。按照如下来修改我们的项目结构：
```
online_interpreter_project/
    frontend/ # 前端目录
        index.html
        css/
            bootstrap.css
            main.css
        js/
            main.js
            bootstrap.js
            jquery.js
    online_interpreter_project/ # 项目配置文件
        __init__.py
        settings.py # 项目配置
        urls.py # URL 配置
    online_interpreter_app/ # 我们真正的应用在这里
        __init__.py
        views.py # 视图
        models.py # 模型
        middlewares.py # 中间件
        mixins.py # mixin
    manage.py
```
编辑项目的`settings.py`：
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'online_interpreter_app',
]
```
```
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'online_interpreter_app.middlewares.put_middleware',
]
```
`MIDDLEWARE`: 我们需要使用的中间件。由于 Django 不支持对 PUT 方法的数据处理，所以我们需要写一个中间件来给它加上这个功能。之后我们会更加详细的了解中间件的写法。
`DATABASES`: 配置我们的数据库。在这里，我们只是简单的使用了 sqlite3 数据库。

### 编写PUT中间件
现在我们先来编写 PUT 中间件，来让 Django 支持 PUT 请求。我们可以使用 POST 方法向 Django 应用上传数据，并且可以使用 request.POST 来访问 POST 数据。我们也想像使用 POST 一样来使用 PUT ，利用 request.PUT 就可以访问到 PUT 请求的数据。
**中间件是 django 很重要的一部分，它在请求和响应之间充当预处理器的角色**。
**很多通用的逻辑可以放到这里**，django 会自动的调用他们。

> POST请求和PUT请求的区别？  
> 	POST和PUT的区别容易被简单地误认为”POST表示创建资源，PUT表示更新资源.但实际上两者都可以用来创建或是更新数据.单从技术上来说,他们并没有什么区别.但是在HTTP规范中POST是非等幂的,多次调用会产生不同的结果,比如:创建一个用户,由于网络原因或是其他原因多创建了几次,那么将会有多个用户被创建.而PUT id/456则会创建一个id为456的用户,多次调用还是会创建的结果是一样的,所以PUT是等幂的.   

在这里，我们写了一个简单的中间件来处理 PUT 请求。只要是 PUT 请求，我们就对它作这样的处理。**所以，当你对某个请求都有相同的处理操作时，可以把它写在中间件里**。所以，中间件是什么呢？
**中间件只是视图函数的公共部分。你把中间件的核心处理逻辑复制粘贴到视图函数中也是能够正常运行的**。

打开`middlewares.py` :
```
from django.http import QueryDict

def put_middleware(get_response):
    def middleware(request):
        if request.method == 'PUT':  # 如果是 PUT 请求
            setattr(request, 'PUT', QueryDict(request.body))  # 给请求设置 PUT 属性，这样我们就可以在视图函数中访问这个属性了
            # request.body 是请求的主体。我们知道请求有请求头，那请求的主体就是
            # request.body 了。当然，你一定还会问，为什么这样就可以访问 PUT 请求的相关
            # 数据了呢？这涉及到了 http 协议的知识，这里就不展开了，有兴趣的同学可以自行查阅资料
        response = get_response(request)  # 使用 get_response 返回响应
        return response  # 返回响应

    return middleware  # 返回核心的中间件处理函数
```
`QueryDict` 是 `django` 专门为请求的查询字符串做的数据结构，它类似字典，但是又不是字典。
`request` 对象的 `POST` `GET` 属性都是这样的字典。类似字典，是因为 `QueryDict` 和 `python` 的 `dict` 有相似的 API 接口，所以你可以把它当字典来调用。
不是字典，是因为 `QueryDict` 允许同一个键有多个值。比如` {‘a’:[‘1’,‘2’]}`，a 同时有值 1 和 2，所以，一般不要用 `QueryDict[key]` 的形式来访问相应 key 的值，因为你得到的会是一个列表，而不是一个单一的值，应该用 `QueryDict.get(key) `来获取你想要的值，除非你知道你在干什么，你才能这样来取值。为什么会允许多个值呢，因为 GET请求中，常常有这种参数 `http://www.example.com/?action=search&achtion=filter `，
`action` 在这里有两个值，有时候我们需要对这两个值都作出响应。但是当你用 `.get(key)`方法取值的时候，只会取到最新的一个值。如果确实需要访问这个键的多个值，应该用 `.getList(key) `方法来访问，比如刚才的例子应该用`request.GET.getList(‘action’) `来访问 `action` 的多个值。

同理，对于 `POST` 请求也应该这么做。
接下来要说说 `request.body` 。做过爬虫的同学一定都知道，请求有请求头，那这个 `body`就是我们的请求体了。严格的讲，这个“请求体”应该叫做“载荷”，用英文来讲，这就叫做`“payload”`。载荷里又有许多的学问了，感兴趣的同学可以自己去了解相关的资料。只需要知道一件很简单的事情，就是把 `request.body` 放进 `QueryDict` 就可以把上传的字段转换为我们需要的字典了。

由于原生的 `request` 对象并没有 PUT 属性，所以我们需要在中间件中加上这个属性，这样我们就可以在视图函数中用 `request.PUT` 来访问 PUT 请求中的参数值了

中间件在 1.11 版本里是一个可调用对象，和之前的类中间件不同。既然是可调用对象，那就有两种写法，一种是函数，因为函数就是一个可调用对象；一种是自己用类来写一个可调用对象，也就是包含 `__call__() `方法的类。
在 1.11 版本中，、中间件对象应该接收一个 `get_response`的参数，**这个参数用来获取上一个中间件处理之后的响应**，**每个中间件处理完请求之后都应该用这个函数来返回一个响应**，我们不需要关心这个 `get _response` 函数是怎么写的，是什么东西，**只需要记得在最后调用它，返回响应就好**。这个最外层函数应该返回一个函数，用作真正的中间件处理。

> 在外层函数下写你的预处理逻辑，比如配置什么的。当然，你也可以在被返回的函数中写配置和预处理。但是这么做有时候就有些不直观，配置、预处理和核心逻辑分开，让看代码的人一眼就明白这个中间件是在做什么。最通常的例子是，很多的 API 会对请求做许多的处理，比如记录下这个请求的 IP 地址就可以先在这里做这个步骤；又比如，为了控制访问频率，可以先读取数据库中的访问数据，根据访问数据记录来决定要不要让这个请求进入到视图函数中。我们对 PUT 请求并没有什么预处理或者配置操作要进行，所以就什么都没写。  
> 中间件的处理逻辑虽然简单，但是中间件的写法和作用还是需要掌握的。  

### 创建模型
接下来，创建模型，编辑`models.py`：
```
from django.db import models

# 创建 Code 模型
class CodeModel(models.Model):
    name = models.CharField(max_length=50)  # 名字最长为 50 个字符
    code = models.TextField()  # 这个字段没有文本长度的限制
    
    def __str__(self):
        return 'Code(name={},id={})'.format(self.name,self.id)
```

> 我们的 API 返回的是 `json`数据类型，所以我们需要把最基础的响应方式更改为 `JsonResponse` 。同时，我们还有一个问题需要考虑，那就是如何把模型数据转换为 `json`类型。 我们知道 `REST` 中所说的 “表现（表层）状态转换” 就是这个意思，把不同类型的数据转换为统一的类型，然后传送给前端。如果前端要求是 `json` 那么我们就传 `json` 过去，如果前端请求的是 `xml` 我们就传 `xml` 过去。这就是“内容协商（协作）”。  

回到我们的问题，我们该如何**把模型数据转换为 json 数据**呢？ 把其它数据按照一定的格式保存下来，这个过程我们称为“序列化”。“序列化”这个词其实很形象，它把一系列的数据，按照一定的方式给整整齐齐的排列好，保存下来，以便他用。在 Django 中，Django 为我们提供了一些简单的序列化工具，我们可以使用这些工具来把模型的内容转换为 json 格式。

其中很重要的工具便是 `serializers` 了，看名字我们就这到它是用来干什么的。其核心函数` serialize(format, queryset[,fields]) `就是用**于把模型查询集转换为 json 字符串**。
它接收的三个参数分别为 format，format 也就是序列化形式，如果我们需要 json 形式的，我们就把 format 赋值为 ‘json’ 。
第二个参数为查询集或者是一个含有模型实例的可迭代对象，也就是说，这个参数只能接收类似于列表的数据结构。
fields 是一个可选参数，他的作用就和 Django 表单中的 fields 一样，是用来控制哪些字段需要被序列化的。

编辑`views.py` :
```
from django.views import View  # 引入最基本的类视图
from django.http import JsonResponse # 引入现成的响应类
from django.core.serializers import serialize  # 引入序列化函数
from .models import CodeModel  # 引入 Code 模型，记得加个 `.`  哦。
import json  # 引入 json 库，我们会用它来处理 json 字符串。

# 定义最基本的 API 视图
class APIView(View):
    def response(self,
                 queryset=None,
                 fields=None,
                 **kwargs):
        """
        序列化传入的 queryset 或 其他 python 数据类型。返回一个 JsonResponse 。
        :param queryset: 查询集，可以为 None
        :param fields: 查询集中需要序列化的字段，可以为 None
        :param kwargs: 其他需要序列化的关键字参数
        :return: 返回 JsonResponse
        """

        # 根据传入参数序列化查询集，得到序列化之后的 json 字符串
        if queryset and fields:
            serialized_data = serialize(format='json',
                                        queryset=queryset,
                                        fields=fields)
        elif queryset:
            serialized_data = serialize(format='json',
                                        queryset=queryset)
        else:
            serialized_data = None
        # 这一步很重要，在经过上面的查询步骤之后， serialized_data 已经是一个字符串
        # 我们最终需要把它放入 JsonResponse 中，JsonResponse 只接受 python 数据类型
        # 所以我们需要先把得到的 json 字符串转化为 python 数据结构。
        instances = json.loads(serialized_data) if serialized_data else 'No instance'
        data = {'instances': instances}
        data.update(kwargs)  # 添加其他的字段
        return JsonResponse(data=data)  # 返回响应
```
需要注意的是，我们先序列化了模型，然后又用 json 把它转换为了 python 的字典结构，因为我们还需要把模型的数据和我们的其它数据（kwargs）放在一起之后才会把它变成真正的 json 数据类型

接下来，重头戏到了，我们需要编写我们的 Mixin 了。 在编写 Mixin 之前，我们需要遵循以下几个原则：
1. 每个 Mixin 只完成一个功能。这就像是我们举的例子一样，一个 Mixin 只会让我们的“Man”类多一个功能出来。这是为了在使用的时候能够更加清晰的明白这个 Mixin 是干什么的，同时能够做到灵活的解耦功能，做到“即插即用”。
2. 每个 Mixin 只操作自己知道的属性和方法，还是那我们之前的 “Man” 类来做例子。我们知道我们写的几个 Mixin 最终都是用于 Man 类的，然而 Man 类的属性有 name、age ，所以在我们的 Mixin 中也可以像这样来访问这些属性： `self.name` , `self.age` 。因为这些属性都是已知的。当然啦，Mixin 自己的属性当然也是可以自己调用的啦。那在 Mixin 中我们需要用到其它的 Mixin 的属性的时候该怎么办呢?很简单，直接继承这个 Mixin 就好了。 我们的 Mixin 最终是要作用到视图上的，所以我们可以把我们的基础视图的属性当作是已知属性。 我们的 APIView 是 View 类的子类，所以 View 的所有属性和方法我们的 Mixin 都可以调用。我们通常用到的属性有:
*  `kwargs`: 这是传入视图函数的关键字参数，我们可以在类视图中使用 `self.kwargs` 来访问这些传入的关键字参数。
* `args`: 传入视图的位置参数。

编写 Mixin 是为了代码的复用和代码的解耦，所以在正式开始编写之前，我们必须要想好，哪一些 Mixin 是我们需要编写的，哪一些逻辑是必须要写到视图函数中。
首先，凡是对于有查询动作的请求，我们都有一个从数据库中提取查询集的过程，所以我们需要编
写一个提取查询集的 Mixin 。
第二，对于查询集来说，有时候我们需要的是整个查询集，有时候只是需要一个单一的查询实例，比如在更新和删除的时候，我们都是在对一个实例进行操作。所以我们还需要编写一个能够提取出单一实例的 Mixin 。
第三，对于 API 的通用操作来说，根据 REST 原则，每个请求都有自己的对应动作，比如 put 对应的是修改动作，post 对应的是创建动作，delete 对应的是删除动作，所以我们需要为这些通用的 API 动作一一编写 Mixin 。
第四，正如第三条考虑到的那样， API 的不同请求是有自己对应的默认动作的。如果我们的视图就是想简单的使用他们的默认动作，也就是 post 是创建动作，put 是修改动作，我们希望视图函数能自己将这些请求自己就映射到这些默认动作上，这样在之后的开发我们就可以什么都不用做了，连最基本的 get post 视图方法都不需要我们编写。所以我们需要编写一个方法映射 Mixin 。
最后，就我们的应用而言，我们应用是为了提供在线解释器服务，所以会有一个执行代码的功能，虽然到目前，这个功能的核心函数执行的代码很简单，但是谁能保证他一直都是这样简单呢？所以为了保持良好的视图解耦性，我们也需要把这部分的代码单独独立出来成为一个 Mixin 。

现在，让我们开始编写我们的 Mixin 。我们编写 Mixin 的活动都会在 mixins.py 中进行。

`mixin.py`:
	`IntegrityError` 错误会在像数据库写入数据创建不成功时被抛出，这是我们需要捕捉并做出响应的错误。

获取查询集 Mixin 的编写：
```
from django.db import models, IntegrityError # 查询失败时我们需要用到的模块
import subprocess # 用于运行代码
from django.http import Http404 # 当查询操作失败时返回404响应

class APIQuerysetMinx(object):
    """
    用于获取查询集。在使用时，model 属性和 queryset 属性必有其一。

    :model: 模型类
    :queryet: 查询集
    """
    model = None
    queryset = None

    def get_queryset(self):
        """
        获取查询集。若有 model 参数，则默认返回所有的模型查询实例。
        :return: 查询集
        """

        # 检验相应参数是否被传入，若没有传入则抛出错误
        assert self.model or self.queryset, 'No queryset fuound.'
        if self.queryset:
            return self.queryset
        else:
            return self.model.objects.all()
```
可以看到，我们的 Mixin 的设计很简单，只是为子类提供了两个参数 queryset和model，并且 `get_queryset` 这个方法会使用这两个属性返回相应的所有的实例查询集。我们可以这样使用它：
```
class GETView(APIQuerysetMinx, View):
    model = MyModel
    def get(self, *args, **kwargs):
        return self.get_queryset()
```

这样我们的视图是不是看起来就方便，清晰了很多，视图逻辑和具体的操作逻辑相分离，这样方便别人阅读自己的代码，一看就知道是什么意思。在之后的 Mixin 使用也是同理的。

编写获取单一实例的 Mixin ：
```
class APISingleObjectMixin(APIQuerysetMinx):
    """
    用于获取当前请求中的实例。

    :lookup_args: list, 用来规定查询参数的参数列表。默认为 ['pk','id]
    """
    lookup_args = ['pk', 'id']

    def get_object(self):
        """
        通过查询 lookup_args 中的参数值来返回当前请求实例。当获取到参数值时，则停止
        对之后的参数查询。参数顺序很重要。
        :return: 一个单一的查询实例
        """
        queryset = self.get_queryset() # 获取查询集
        for key in self.lookup_args:
            if self.kwargs.get(key):
                id = self.kwargs[key] # 获取查询参数值
                try:
                    instance = queryset.get(id=id) # 获取当前实例
                    return instance # 实例存在则返回实例
                except models.ObjectDoesNotExist: # 捕捉实例不存在异常
                    raise Http404('No object found.') # 抛出404异常响应
        raise Http404('No object found.') # 若遍历所有参数都未捕捉到值，则抛出404异常响应
```

我们可以看到，获取单一实例的方式是从传入视图函数的关键字参数kwargs中获取对应的 id 或者 pk 然后从查询集中获取相应的实例。并且我们还可以灵活的配置查询的关键词是什么，这个 Mixin 还很方便使用的。

接下来我们需要编写的是获取列表的 Mixin:
```
class APIListMixin(APIQuerysetMinx):
    """
    API 中的 list 操作。
    """
    def list(self, fields=None):
        """
        返回查询集响应
        :param fields: 查询集中希望被实例化的字段
        :return: JsonResopnse
        """
        return self.response(
            queryset=self.get_queryset(),
            fields=fields) # 返回响应
```
我们可以看到，我们只是简单的返回了查询集，并且默认的方法还支持传入需要的序列化的字段。

执行创建操作的 Mixin：
```
class APICreateMixin(APIQuerysetMinx):
    """
    API 中的 create 操作
    """
    def create(self, create_fields=None):
        """
        使用传入的参数列表从 POST 值中获取对应参数值，并用这个值创建实例，
        成功创建则返回创建成功响应，否则返回创建失败响应。
        :param create_fields: list, 希望被创建的字段。
        若为 None, 则默认为 POST 上传的所有字段。
        :return: JsonResponse
        """
        create_values = {}
        if create_fields: # 如果传入了希望被创建的字段，则从 POST 中获取每个值
            for field in create_fields:
                create_values[field]=self.request.POST.get(field)
        else:
            for key in self.request.POST: # 若未传入希望被创建字段，则默认为 POST 上传的
                                            # 字段都为创建字段。
                create_values[key]=self.request.POST.get(key);
        queryset = self.get_queryset() # 获取查询集
        try:
            instance = queryset.create(**create_values) # 利用查询集来创建实例
        except IntegrityError: # 捕捉创建失败异常
            return self.response(status='Failed to Create.') # 返回创建失败响应
        return self.response(status='Successfully Create.') # 创建成功则返回创建成功响应
```
我们可以看到，作为 API 的 Mixin ，创建的默认动作已经是从 POST 中获取相应的数据，这就不用我们把提取数据的逻辑硬编码在视图中了，而且考虑到了足够多的情况。并且我们还手动的传入了 status ，方便前端开发能够清楚的知道操作是否成功。

实例查询 Mixin:
```
class APIDetailMixin(APISingleObjectMixin):
    """
    API 操作中查询实例操作
    """
    def detail(self, fields=None):
        """
        返回当前请求中的实例
        :param fields: 希望被返回实例中哪些字段被实例化
        :return: JsonResponse
        """
        return self.response(
            queryset=[self.get_object()],
            fields=fields)
```
同理，我们只是简单的调用了 get_object 方法，并没有做其它的处理。

更新 Mixin:
```
class APIUpdateMixin(APISingleObjectMixin):
    """
    API 中更新实例操作
    """
    def update(self, update_fields=None):
        """
        更新当前请求中实例。更新成功则返回成功响应。否则，返回更新失败响应。
        若传入 updata_fields 更新字段列表，则只会从 PUT 上传值中获取这个列表中的字段，
        否则默认为更新 POST 上传值中所有的字段。
        :param update_fields: list, 实例需要被更新的字段
        :return: JsonResponse
        """
        instance = self.get_object() # 获取当前请求中的实例
        if not update_fields: # 若无字段更新列表，则默认为 PUT 上传值的所有数据
            update_fields=self.request.PUT.keys()
        try: # 迭代更新实例字段
            for field in update_fields:
                update_value = self.request.PUT.get(field) # 从 PUT 中取值
                setattr(instance, field, update_value) # 更新字段
            instance.save() # 保存实例更新
        except IntegrityError: # 捕捉更新错误
            return self.response(status='Failed to Update.') # 返回更新失败响应
        return self.response(
            status='Successfully Update')# 更新成功则返回更新成功响应
```
setattr 的作用就是给一个对象设置属性，当查询的实例被找到之后，我们采用这种方法来给实例更新值。因为我们在这种情况下不能使用 . 路径符来访问字段，因为我们不知道有哪些字段会被更新。同时，作为 API 的 Mixin ，更新时获取数据的地方已经默认为从 PUT 请求中获取数据。

删除操作Mixin:
```
class APIDeleteMixin(APISingleObjectMixin):
    """
    API 删除实例操作
    """
    def remove(self):
        """
        删除当前请求中的实例。删除成功则返回删除成功响应。
        :return: JsonResponse
        """
        instance = self.get_object() # 获取当前实例
        instance.delete() # 删除实例
        return self.response(status='Successfully Delete') # 返回删除成功响应
```
需要注意的是，我们的方法名不叫 delete ，而是 remove ，这是因为 delete 是请求方法名，我们不能占用它。

运行代码的 Mixin:
```
class APIRunCodeMixin(object):
    """
    运行代码操作
    """
    def run_code(self, code):
        """
        运行所给的代码，并返回执行结果
        :param code: str, 需要被运行的代码
        :return: str, 运行结果
        """
        try:
            output = subprocess.check_output(['python', '-c', code], # 运行代码
                                             stderr=subprocess.STDOUT, # 重定向错误输出流到子进程
                                             universal_newlines=True, # 将返回执行结果转换为字符串
                                             timeout=30) # 设定执行超时时间
        except subprocess.CalledProcessError as e: # 捕捉执行失败异常
            output = e.output # 获取子进程报错信息
        except subprocess.TimeoutExpired as e: # 捕捉超时异常
            output = '\r\n'.join(['Time Out!', e.output]) # 获取子进程报错，并添加运行超时提示
        return output # 返回执行结果
```

这个也不多说，就只是简单的把之前的函数式更改为了类。不过要注意的是，如果你用的是 py2，subprocess 有的属性的引用方式会和 3 有写不同，大家可以自行去查阅如何正确引入相关的属性。
前几个 Mixin 都没有很详细的说，下面这个 Mixin 我们需要详细的说明。
```
class APIMethodMapMixin(object):
    """
    将请求方法映射到子类属性上

    :method_map: dict, 方法映射字典。
    如将 get 方法映射到 list 方法，其值则为 {'get':'list'}
    """
    method_map = {}
    def __init__(self,*args,**kwargs):
        """
        映射请求方法。会从传入子类的关键字参数中寻找 method_map 参数，期望值为 dict类型。寻找对应参数值。
        若在类属性和传入参数中同时定义了 method_map ，则以传入参数为准。
        :param args: 传入的位置参数
        :param kwargs: 传入的关键字参数
        """
        method_map=kwargs['method_map'] if kwargs.get('method_map',None) \
                                        else self.method_map # 获取 method_map 参数
        for request_method, mapped_method in method_map.items(): # 迭代映射方法
            mapped_method = getattr(self, mapped_method) # 获取被映射方法
            method_proxy = self.view_proxy(mapped_method) # 设置对应视图代理
            setattr(self, request_method, method_proxy) # 将视图代码映射到视图代理方法上
        super(APIMethodMapMixin,self).__init__(*args,**kwargs) # 执行子类的其他初始化

    def view_proxy(self, mapped_method):
        """
        代理被映射方法，并代理接收传入视图函数的其他参数。
        :param mapped_method: 被代理的映射方法
        :return: function, 代理视图函数。
        """
        def view(*args, **kwargs):
            """
            视图的代理方法
            :param args: 传入视图函数的位置参数
            :param kwargs: 传入视图函数的关键字参数
            :return: 返回执行被映射方法
            """
            return mapped_method() # 返回执行代理方法
        return view # 返回代理视图
```

我们先给子类提供了一个 method_map 的属性，这是一个字典，子类可以通过给这个字典配置相应的值来使用我们的 APIMethodMapMixin ，字典的键为请求的方法名，值为要执行的操作。。接下来看看 __init__ 方法，首先，会在子类视图实例化的时候寻找 method_map 参数，如果找到了就会以这个参数作为方法映射的字典，在子类中编写的配置就不会生效了。也就是说：
```
# views.py
class ExampleView(APIMethodMapMixin, APIView):
    method_map = {'get':'list','put':'update'}

# urls.py

urlpatterns = [url(r'^$',ExampleView.as_view(method_map={'get':'list'}))]
```

如果在初始化视图类的时候也传入了 method_map 参数，那我们定义在 ExampleView 中的属性就没用了，视图会以初始化时的参数作为最终标准。由于我们的字典只是一个字符串，我们要做的是把子类的对应操作方法和请求方法对应起来，所以我们首先使用 getattr 来获取子类的响应操作的方法，然后利用了 view_proxy 代理了视图方法。为什么我们需要这个代理方法？原因很简单，因为在默认的视图中，View 会向视图传递参数，然而，我们的操作方法，他们的参数和被传入视图的参数是截然不同的，所以我们需要使用一个函数来代理接收这些参数，这个函数就是我们视图代理函数返回的 view 函数，这个函数会接收所有传向视图的参数，然后不对这些参数做出处理，只是简单的调用被映射的方法。
python 基础很不错的同学应该已经发现了，我们的 view_proxy 的写法不就是一个装饰器的写法吗？是的，装饰器也是这样写的，只是我们在 __init__ 中手动调用了它而已，平时我们用 @ 来使用装饰器和我们手动调用的过程是完全相同的。在最后，我们把操作方法设置为了请求对应方法的值，这样我们就可以成功的调用相应的操作了。别忘了在最后调用 super 哦。
以上便是我们所有的 Mixin 的编写。现在，我们来完成编写 views.py 。

首先，在顶上引入这些包：
```
from django.views import View  # 引入最基本的类视图
from django.http import JsonResponse, HttpResponse  # 引入现成的响应类
from django.core.serializers import serialize  # 引入序列化函数
from .models import CodeModel  # 引入 Code 模型，记得加个 `.`  哦。
import json  # 引入 json 库，我们会用它来处理 json 字符串。
from .mixins import APIDetailMixin, APIUpdateMixin, \
    APIDeleteMixin, APIListMixin, APIRunCodeMixin, \
    APICreateMixin, APIMethodMapMixin, APISingleObjectMixin  # 引入我们编写的所有 Mixin
```

我们的核心 API：
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

有了 Mixin 是不是很方便，这种感觉不要太爽。
接下来完成运行代码的 API :
```
class APIRunCodeView(APIRunCodeMixin,
                     APISingleObjectMixin,
                     APIView):
    model = CodeModel  # 传入模型

    def get(self, request, *args, **kwargs):
        """
        GET 请求仅对能获取到 pk 值的 url 响应
        :param request: 请求对象
        :param args: 位置参数
        :param kwargs: 关键字参数
        :return: JsonResponse
        """
        instance = self.get_object()  # 获取对象
        code = instance.code  # 获取代码
        output = self.run_code(code)  # 运行代码
        return self.response(output=output, status='Successfully Run')  # 返回响应

    def post(self, request, *args, **kwargs):
        """
        POST 请求可以被任意访问，并会检查 url 参数中的 save 值，如果 save 为 true 则会
        保存上传代码。
        :param request: 请求对象
        :param args: 位置参数
        :param kwargs: 关键字参数
        :return: JsonResponse
        """
        code = self.request.POST.get('code')  # 获取代码
        save = self.request.GET.get('save') == 'true'  # 获取 save 参数值
        name = self.request.POST.get('name')  # 获取代码片段名称
        output = self.run_code(code)  # 运行代码
        if save:  # 判断是否保存代码
            instance = self.model.objects.create(name=name, code=code)
        return self.response(status='Successfully Run and Save',
                             output=output)  # 返回响应

    def put(self, request, *args, **kwrags):
        """
        PUT 请求仅对更改操作作出响应
        :param request: 请求对象
        :param args: 位置参数
        :param kwrags: 关键字参数
        :return: JsonResponse
        """
        code = self.request.PUT.get('code')  # 获取代码
        name = self.request.PUT.get('name')  # 获取代码片段名称
        save = self.request.GET.get('save') == 'true'  # 获取 save 参数值
        output = self.run_code(code)  # 运行代码
        if save:  # 判断是否需要更改代码
            instance = self.get_object()  # 获取当前实例
            setattr(instance, 'name', name)  # 更改名字
            setattr(instance, 'code', code)  # 更改代码
            instance.save()
        return self.response(status='Successfully Run and Change',
                             output=output)  # 返回响应
```
值得注意的是，我们使用了一个 `save` 参数来判断上传的代码是否需要保存，因为上传方式都是 POST 我们在这种情况下就需要增加新的参数来决定是否需要保存。而且由于我们没有怎么使用 Mixin ，所有的字段我们都是手动提取的，所有的操作过程都是我们自己写的，就显得有点笨搓搓的。由此可见 Mixin 是多么的好用。

别忘了，我们还要提供静态文件服务：
```
# 主页视图
def home(request):
    """
    读取 'index.html' 并返回响应
    :param request: 请求对象
    :return: HttpResponse
    """
    with open('frontend/index.html', 'rb') as f:
        content = f.read()
    return HttpResponse(content)


# 读取 js 视图
def js(request, filename):
    """
    读取 js 文件并返回 js 文件响应
    :param request: 请求对象
    :param filename: str-> 文件名
    :return: HttpResponse
    """
    with open('frontend/js/{}'.format(filename), 'rb') as f:
        js_content = f.read()
    return HttpResponse(content=js_content,
                        content_type='application/javascript')  # 返回 js 响应


# 读取 css 视图
def css(request, filename):
    """
    读取 css 文件，并返回 css 文件响应
    :param request: 请求对象
    :param filename: str-> 文件名
    :return: HttpResponse
    """
    with open('frontend/css/{}'.format(filename), 'rb') as f:
        css_content = f.read()
    return HttpResponse(content=css_content,
                        content_type='text/css')  # 返回 css 响应
```

在静态文件的响应中需要把响应头更改为正确的响应头，不然浏览器就不认识你传回去的是什么静态件了。
最后，按照我们之前的设计，完成我们的 API URL 配置。
编辑你的 urls.py，这个文件是和你的 settings.py 在同一个目录哦
```
"""online_interpreter_project URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/2.2/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path
# 这是我们的 URL 入口配置，我们直接将入口配置到具体的 URL 上。

from django.conf.urls import url, include  # 引入需要用到的配置函数
# include 用来引入其他的 URL 配置。参数可以是个路径字符串，也可以是个 url 对象列表

from online_interpreter_app.views import APICodeView, APIRunCodeView, home, js, css  # 引入我们的视图函数
from django.views.decorators.csrf import csrf_exempt  # 同样的，我们不需要使用 csrf 功能。

# 注意我们这里的 csrf_exempt 的用法，这和将它作为装饰器使用的效果是一样的

# 普通的集合操作 API
generic_code_view = csrf_exempt(APICodeView.as_view(method_map={'get': 'list',
                                                                'post': 'create'}))  # 传入自定义的 method_map 参数
# 针对某个对象的操作 API
detail_code_view = csrf_exempt(APICodeView.as_view(method_map={'get': 'detail',
                                                               'put': 'update',
                                                               'delete': 'remove'}))
# 运行代码操作 API
run_code_view = csrf_exempt(APIRunCodeView.as_view())
# Code 应用 API 配置
code_api = [
    url(r'^$', generic_code_view, name='generic_code'),  # 集合操作
    url(r'^(?P<pk>\d*)/$', detail_code_view, name='detail_code'),  # 访问某个特定对象
    url(r'^run/$', run_code_view, name='run_code'),  # 运行代码
    url(r'^run/(?P<pk>\d*)/$', run_code_view, name='run_specific_code')  # 运行特定代码
]
api_v1 = [url('^codes/', include(code_api))]  # API 的 v1 版本
api_versions = [url(r'^v1/', include(api_v1))]  # API 的版本控制入口 URL
urlpatterns = [
    url(r'^api/', include(api_versions)),  # API 访问 URL
    url(r'^$', home, name='index'),  # 主页视图
    url(r'^js/(?P<filename>.*\.js)$', js, name='js'),  # 访问 js 文件，记得，最后没有 /
    url(r'^css/(?P<filename>.*\.css)$', css, name='css')  # 访问 css 文件，记得，最后没有 /
]
urlpatterns = [
    path('admin/', admin.site.urls),
]
```
记得，在静态文件服务的 url 后面没有 / ，因为在前端引用的时候是不会加 / 的，这是对一个文件的直接访问。
最后，回到项目路径下，运行：
```
python manage.py makemigrations
python manage.py migrate
```

创建好数据库之后我们就可以进入前端的开发了。
我们的后端就算完成了。

[Django RESTful 系列教程（二）（下） - 知乎](https://zhuanlan.zhihu.com/p/32129225)