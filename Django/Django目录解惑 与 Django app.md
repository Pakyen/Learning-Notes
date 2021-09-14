# Django目录解惑 与 Django app
在初学DjangoRest时，对Django中app的概念不是很清晰，所以在此对Django项目与Django中的app的区别进行一个简要的记录。

## Django项目目录
一个新建立的项目结构大概如下：
```
newProject/
    manage.py
    newProject/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

各文件和目录解释：
* 外层的`newProject/`目录与Django无关，只是你项目的容器，可以任意重命名。
* `manage.py`：一个命令行工具，管理Django的交互脚本。
* 内层的`newProject/`目录是真正的项目文件包裹目录，它的名字是你引用内部文件的Python包名，例如：mysite.urls。
* `newProject/__init__.py`:一个定义包的空文件。
* `newProject/settings.py`:项目的配置文件。
* `newProject/urls.py`:路由文件，所有的任务都是从这里开始分配，相当于Django驱动站点的目录。
* `newProject/wsgi.py`:一个基于WSGI的web服务器进入点，提供底层的网络通信功能，通常不用关心。
* `newProject/asgi.py`：一个基于ASGI的web服务器进入点，提供异步的网络通信功能，通常不用关心。

## Django项目与Django app
Django项目通常是使用django-admin工具创建的项目结构，执行如下命令创建Django项目myproject：
```
django-admin startproject myproject
```
而Django应用是在Django项目中，使用Django项目的manage.py（等同于django-admin工具的项目定制版）创建，在myproject/路径下执行如下命令创建应用myApp：
```
python manage.py startapp myApp
```
* 一个Django项目就是一个基于Django的Web应用。
* 一个Django项目中包含一组配置和若干个Django app。
* 一个Django app就是一个可重用的Python软件包，提供一定的功能。
* 一个Django app中可以包含models, views, templates, template tags, static files, URLs等。
* 一个Django项目可以包含多个Django app。
* 一个Django app也可以被包含到多个Django项目中，因为Django app是可重用的Python软件包。

## INSTALLED_APP
在settings.py中，我们可以看到有一个叫做INSTALLED_APP的变量，该变量的值是一个list，给出在Django项目中包含的Django应用。

Django框架默认情况下，Django项目中包含如下Django应用：
```
INSTALLED_APPS = [
    'django.contrib.admin',			站点管理系统
    'django.contrib.auth',			认证系统
    'django.contrib.contenttypes',		content types框架
    'django.contrib.sessions',			session框架
    'django.contrib.messages',			message框架
    'django.contrib.staticfiles',		静态文件管理框架
]
```