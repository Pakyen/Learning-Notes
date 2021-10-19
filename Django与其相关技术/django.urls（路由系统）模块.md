# django.urls（路由系统）模块

## 简介
干净、优雅的URL方案是高质量Web应用程序中的一个重要细节。Django中的urls模块可以帮助我们设计RESTful风格的url，这个模块在本质上是是URL模式（简单的正则表达式）与Python函数（您的视图）之间的简单映射。
URL配置(URLconf)就像Django 所支撑网站的目录。它的本质是URL与要为该URL调用的视图函数之间的映射表。
我们就是以这种方式告诉Django，对于这个URL调用这段代码，对于那个URL调用那段代码


## Django是如何处理一个请求的？
当用户从您的Django驱动的站点请求页面时，这是系统遵循的算法来确定要执行哪些Python代码：
	* 一旦生成url页面请求，请求传递到urls.py；
	* Django去urlpatterns中匹配链接（Django会在匹配到的第一个就停下来）；
	* 一旦匹配成功，就会去执行，path后面的方法，Django便会给出相应的view页面（该页面可以为一个Python的函数，或者基于view（Django内置的）的类），也就是用户看到的页面；
	* 若匹配失败，则出现错误的页面。

## URLconf 配置
如上文所述，URLconf是Django网站的目录。其本质是url pattern与要为该url pattern调用的试图函数的映射。

### 编写url patterns的三种基础方法 —— path() 
可以利用path()方法来编写urlpatterns
**path(route, view, kwargs=None, name=None)**
返回一个元素，以便包含在 **urlpatterns** 中。

1. 直接映射views中的简单函数
```
#urls.py

    from app import views #这里的app是你自己的应用的名字
    from django.urls import path
	  
    # view.index是一个函数
    urlpatterns = [
                path('index/', views.index, name='index'),
    ]
```

2. 从views中即成的类
```
#urls.py
    from app.views import LoginView
    from django.urls import path
    urlpatterns = [
                path('login/', LoginView.as_view(), name='login'),
    ]
```
```
#views.py
class LoginView(View):
    #请求为get时
		def get(self,request):
		 ...
		return render(request, 'login.html')
	 #请求为post时
		def post(self,request):
        ...
        return render(request,'login.html')
```

3. 导入其他的URL文件（当有许多个urls.py文件时）
```
 #urls.py(系统默认的)
    from django.urls import include, path
    urlpatterns = [
        path('login/', include('app.urls'))#假设自己新建的urls在app（应用中）
    ]
```

### 正则表达式形式的url patterns —— re_path()
也可以使用re_path()方法来用正则表达式的形式匹配urlpatterns
**re_path(route, view, kwargs=None, name=None)**

需要注意的是，导入的包不是path而不是re_path
```
import django.urls import re_path 
urlpatterns = [
                re_path(r'^articles/(?P<year>[0-9]{4})/$', view.year, name='article'),
                re_path(r'^blog/(page-(\d+)/)?$',blog_articles),
     ]   
```
这样子可以很方便的匹配到具体某一年的文章，而不用对“每一年”都写一个path，这样子可以极大的减轻工作量。

### include()方法
**include(module, namespace=None)**
**include(patten_list)**
**include(pattern_list, app_namespace), namespace=None)**
include()方法收一个完整的 Python 导入路径到另一个应该被 “包含” 在这里的 URLconf 模块。
**include()** 也接受一个返回 URL 模式的迭代函数或一个包含这种迭代函数加上应用程序名称空间的二元元组作为参数。
**参数:**
	* **module** — URLconf 模块（或模块名称）
	* **namespace** ( [str](https://docs.python.org/3/library/stdtypes.html#str) ) — 包含的 URL 条目的实例命名空间。
	* **pattern_list** — 可迭代的 **path()** 和／或 **re_path()** 实例。
	* **app_namespace** ( [str](https://docs.python.org/3/library/stdtypes.html#str) ) — 被包含的 URL 条目的应用命名空间



## 总结
path方法适用于页面较少的网站，re_path可以利用正则表达的优势适用于较多的页面的网站