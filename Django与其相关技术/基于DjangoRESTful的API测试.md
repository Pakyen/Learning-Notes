# 基于DjangoRESTful的API测试


## DRF中的测试工具
在DjangoRestFramework中，比较常用的测试类有：
* `APIRequestFactory`：类似于Django的RequestFactory。支持对视图发送各种请求，对比各种响应。
* `APIClient`：类似于Django的Client。支持对url发起GET和POST、测试响应。
* `APITestCase`：类似于Django的TestCase。各种测试类的父类。

### 创建测试请求
`Django`的`RequestFactory`可以创建一个请求实例，对视图函数发起请求。`DjangoRESTFramework`的`APIRequestFactory`类拓展了`Django`的`RequestFactory`；它支持几乎所有的HTTP动词。语法如下：
```
factory = APIRequestFactory()
request = factory.post(uri, post_data)
```

在polls/tests.py中创建第一个测试

```
# in polls/tests.py

# from django.test import TestCase

# Create your tests here.
from rest_framework.test import APITestCase
from rest_framework.test import APIRequestFactory

from polls import apiview, apiviewsets


class TestPoll(APITestCase):
    def setUp(self):
        # 设置初始化的值
        self.factory = APIRequestFactory()
        self.view = apiviewsets.PollViewSet.as_view({'get': 'list'})
        self.uri = '/polls/'

    def test_list(self):
        request = self.factory.get(self.uri)
        response = self.view(request)
        self.assertEqual(
            response.status_code, 
            200,
            '预期200状态的响应，但响应码为{}.'.format(response.status_code)
        )
```

**运行测试的方法**
```
$ python manage.py test
```

![](%E5%9F%BA%E4%BA%8EDjangoRESTful%E7%9A%84API%E6%B5%8B%E8%AF%95/%E6%88%AA%E5%B1%8F2021-09-29%20%E4%B8%8A%E5%8D%8811.29.51.png)

可以看到我们的测试失败了。这是因我访问这个视图需要认证，而测试没有认证。 我们来编写一个带认证的测试:

```
# in polls/tests.py

# from django.test import TestCase

# Create your tests here.
from django.contrib.auth import get_user_model

from rest_framework.authtoken.models import Token
from rest_framework.test import APITestCase
from rest_framework.test import APIRequestFactory

from polls import apiview, apiviewsets


class TestPoll(APITestCase):
    def setUp(self):
        self.factory = APIRequestFactory()
        self.view = apiviewsets.PollViewSet.as_view({'get': 'list'})
        self.uri = '/polls/'
        self.user = self.setup_user()
        self.token = Token.objects.create(user=self.user)
        self.token.save()

    @staticmethod
    def setup_user():
        User = get_user_model()
        return User.objects.create_user(
            'test',
            email='testuser@test.com',
            password='823w74ytrh3948gh!'
        )

    def test_list(self):
        request = self.factory.get(
            self.uri,
            HTTP_AUTHORIZATION='Token {}'.format(self.token.key)
        )
        request.user = self.user
        response = self.view(request)
        self.assertEqual(
            response.status_code, 
            200,
            '预期200状态的响应，但响应码为{}.'.format(response.status_code)
        )
```

再来运行测试，结果如图：
![](%E5%9F%BA%E4%BA%8EDjangoRESTful%E7%9A%84API%E6%B5%8B%E8%AF%95/%E6%88%AA%E5%B1%8F2021-09-29%20%E4%B8%8A%E5%8D%8811.30.25.png)


### 使用APIClient
**使用APIClient不需要先创建request，可以直接使用GET和POST**
```
# in polls/test.py

# from django.test import TestCase

# Create your tests here.
from django.contrib.auth import get_user_model

from rest_framework.authtoken.models import Token
from rest_framework.test import APITestCase
from rest_framework.test import APIRequestFactory
from rest_framework.test import APIClient

from polls import apiview, apiviewsets


class TestPoll(APITestCase):
    def setUp(self):
        self.factory = APIRequestFactory()
        self.client = APIClient()
        self.view = apiviewsets.PollViewSet.as_view({'get': 'list'})
        self.uri = '/polls/'
        self.user = self.setup_user()
        self.token = Token.objects.create(user=self.user)
        self.token.save()

    @staticmethod
    def setup_user():
        User = get_user_model()
        return User.objects.create_user(
            'test',
            email='testuser@test.com',
            password='823w74ytrh3948gh!'
        )

    def test_list(self):
        request = self.factory.get(
            self.uri,
            HTTP_AUTHORIZATION='Token {}'.format(self.token.key)
        )
        request.user = self.user
        response = self.view(request)
        self.assertEqual(
            response.status_code, 
            200,
            '预期200状态的响应，但响应码为{}.'.format(response.status_code)
        )

    def test_list2(self):
        response = self.client.get('/api-polls/polls/')
        self.assertEqual(
            response.status_code,
            200,
            '预期200状态的响应，但响应码为{}.'.format(response.status_code)
        )
```

如果现在运行测试，会因为没有认证而不能通过。我们可以使用APIClient.login进行认证。

```
# in polls/tests.py

class TestPoll(APITestCase):
    ... ...

    def test_list2(self):
        # self.client.login(username='test', password='823w74ytrh3948gh!')
        self.client.force_authenticate(user=self.user, token=self.token)
        response = self.client.get('/api-polls/polls/')  # 注意这里使用的不是self.uri
        self.assertEqual(
            response.status_code,
            200,
            '预期200状态的响应，但响应码为{}.'.format(response.status_code)
        )
```

`APIClient`提供了
* client.login(username=‘lauren’, password=‘secret’)，使用账号密码
* client.credentials(HTTP_AUTHORIZATION=‘Token ‘ + token.key)，使用token
* client.force_authenticate(user=user)，强制认证
三种方式进行认证，即使用账号密码，使用token，使用强制认证等。此处使用的是强制认证。 值得注意的是，APIClient模拟的是客户端，所有self.client.get(uri)中的uri，要使用不含主机名的完整链接。

官方文档见： [Testing - Django REST framework](https://link.zhihu.com/?target=https%3A//www.django-rest-framework.org/api-guide/testing/%23apiclient)  


### POST测试
之前都是进行GET测试，现在开始进行POST测试。
```
class TestPoll(APITestCase):
    … …

    def test_create(self):
        self.client.login(username=‘test’, password=‘823w74ytrh3948gh!’)  # 这一次，我们使用了用户名、密码验证
        params = {
            ‘question’: ‘如何看待第一届杨超越杯编程大赛？’,
            ‘created_by’: self.user.id
        }
        response = self.client.post(‘/api-polls’ + self.uri, params)
        self.assertEqual(
            response.status_code,
            201,
            ‘预期201状态的响应，但响应码为{}.’.format(response.status_code)
        )  # 注意post成功的状态码不是200，而是201
```


## 参考
[Testing - Django REST framework](https://www.django-rest-framework.org/api-guide/testing/)
















