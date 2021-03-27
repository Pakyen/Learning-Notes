# 4-1 IDEA基础

## 1.集成开发环境 
Integrated Development Environment, IDE
是一种专门用来提高软件开发效率的软件

## 2. IDEA的项目结构
![](4-1%20IDEA%E5%9F%BA%E7%A1%80/%E6%88%AA%E5%B1%8F2021-01-08%2014.12.15.png)
最为细致的文件夹叫做包，1个package里可以保存多个文件
一个模块里可以包括多个包
一个项目可以包括多个模块

### src文件夹
创建好的Module里会有一个src文件夹，所有的代码都要写在src文件夹内部，iml文件里是idea的一些配置信息，与代码无关
![](4-1%20IDEA%E5%9F%BA%E7%A1%80/%E6%88%AA%E5%B1%8F2021-01-08%2020.57.07.png)
External Libraries是外部库，点开后发现是jdk而已，这些我们也用不上

### out文件夹
运行java class后，像终端运行生成的.class文件，都被放在了out文件夹中



## 导入Module
Project Structure - Modules - import Module
然后一路无脑next即可


