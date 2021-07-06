# mac下使用阿里云+Wordpress搭建个人博客

## 目录

- - - -
搭建方式：
*ubuntu+apache2+mysql+php+wordpress* 
即所谓的LAMP方式

[在阿里云ECS服务器Ubuntu16.04LTS上部署apache2+php+mysql环境_づ奈何ā的博客-CSDN博客](https://blog.csdn.net/weixin_42878826/article/details/81451668)
[如何在阿里云服务器上搭建wordpress博客？ - 知乎](https://www.zhihu.com/question/36495153)
[在阿里云服务器上搭建wordpress_啊呜比比是少年的博客-CSDN博客](https://blog.csdn.net/qq_35673187/article/details/78823308)
## 1. 购买云服务器
要搭建个人博客，首先就需要一台个人的服务器。我购买的是阿里云的[云服务器ECS](https://cn.aliyun.com/product/ecs)，当然，购买腾讯云、华为云等均可。
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2015.06.33.png)

### 1.1 登陆云服务器
在购买ECS服务器后，系统会创建一个ECS实例**。每一个ECS实例对应一台已购买的云服务器**。您可以通过电脑上自带的终端工具访问云服务器，进行应用部署和环境搭建。

1. 在ECS实例列表页面，选择实例的所属地域。
2. 找到目标实例，然后在操作列选择【更多】> **【**密码/密钥】 > 【重置实例密码**】**，然后在弹出的对话框设置ECS实例的登录密码
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/C2F205A4-7B85-40D9-8AD8-81D7417C7B77.png)
3. 在弹出的页面，单击【立即重启】使新密码生效。
4. 在ECS实例列表页面，复制ECS实例的公网IP地址。
5. 打开电脑上的命令行终端工具。
	* Windows：Powershell
	* MAC：Terminal
 
### 1.2 连接云服务器
1. 终端中输入`ssh -v`查看电脑中是否安装了SSH
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/9E1D6E2E-DCF2-4B61-9DA0-B6E7DD6AA88F.png)

2. 输入命令`ssh root@[公网ip地址]`，再输入yes，最后输入密码，连接到云服务器
3. 登陆成功
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/29D1393A-21AA-4D59-B227-DA7CAC9F7CD4.png)


## 2. 配置环境
### 2.1 阿里云主机开放端口
接下来，在阿里云服务器-安全组-配置规则里，开放apache2的80端口
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2016.06.38.png)
	- 80／80为开放端口 
	- 0.0.0.0/0为允许所有ip访问


### 2.2 安装MySQL
1. 安装mySQL
`apt-get install mysql-server mysql-client` 
> 如果显示**E: Unable to locate package mysql-server**  
> 使用命令 **sudo apt-get update** 更新软件源即可  
2. 配置
`mysql -u root -p `
3. 建立数据库
> 建一个数据库，wordpress配置信息会用到  
`CREATE DATABASE wordpress;`

### 2.3 安装apache2
	* 安装：`apt-get install apache2`
	* 重启：`service apache2 restart`
利用主机浏览器登入服务器公网ip地址，显示apache页面则成功安装。
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2016.18.39.png)



### 2.4 安装PHP
* 安装PHP通过一下命令：
`apt-get install php`
* 运行完成之后可以通过一下命令查看php版本
`php -v`
* 安装apache的php模块，使apache可以调用php引擎处理php程序
`apt-get install libapache2-mod-php`
* 运行完成之后通过命令查看是否安装成功
`cat /etc/apache2/mods-enabled/php7.4.load`
> 注意，php7.4代表我的php版本  
> 如果显示下列内容，则代表成功:  
>   ![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2017.00.50.png)  
* 检查PHP运行环境是否安装完成
通过以下命令在apache的www根目录写入phpinfo.php文件
`vi /var/www/html/phpinfo.php`
输入内容

### 2.5 apache2配置
进入apache2.conf
`vim /etc/apache2/apache2.conf`

将下面两句话添加到配置文件:
```
AddType application/x-httpd-php .php .htm .html
AddDefaultCharset UTF-8
```

重启服务：
`service apache2 restart`

### 2.6 安装FileZilla
我们可以使用FileZilla
> FileZilla 是一款高效的 FTP 客户端工具。FileZilla 可以帮助您将本地计算机上的文件上传到虚拟主机实例中。FileZilla Server则是一个小巧并且可靠的支持FTP&SFTP的FTP服务器软件。  
[Download FileZilla Client for Mac OS X](https://filezilla-project.org/download.php?platform=osx)

然后：
1. 在阿里云的安全组规则中添加安全规则
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2016.28.25.png)
2. FileZilla工具中新增站点，配置如下
> 注意：使用FTP协议时，端口号填21，使用SFTP协议时，端口号填22。其中站点的传输模式需要配置为“主动”，否则连接时会报错，提示“20秒后无活动，连接超时”。  

![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/AD6DC807-0E40-4C22-B570-27FFFCB44716.png)
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2016.34.51.png)
但是因为阿里云ECS的问题配置失败，最后选择了SFTP传输协议
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/9AF97E91-1F27-470D-B811-3F61C905BD4A.png)

> 参考：  
> [阿里云ECS链接FTP（Filezilla） – Jianxin Shen](https://jianxinshen.com/aliyun-ecs-contect-ftp/)  
> [FileZilla连接阿里云_good for 的博客-CSDN博客_阿里云filezilla](https://blog.csdn.net/u010535048/article/details/97492676)  

### 2.7 phpMyadmin安装（可视化数据库）
安装：选择apache2，配置数据库，输入密码。 
`sudo apt-get install phpmyadmin`

创建phpMyAdmin快捷方式 
`sudo ln -s /usr/share/phpmyadmin /var/www/html`

启用Apache mod_rewrite模块 
`sudo a2enmod rewrite`
`systemctl restart apache2`

重启服务

```
apt-get install php7.0
apt-get install libapache2-mod-php7.0
apt-get install php7.0-mysq
```


* 若遇到密码或账号不对的问题
在终端输入：
`vim /etc/phpmyadmin/config-db.php`
可以查看自己数据库管理工具的账户和密码：
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/C5A11813-C546-4011-A175-AD699C330658.png)
之后再输入账号密码便可进入可视化数据库
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2019.49.18.png)
## 3. Wordpress
### 3.1 wordpress的安装
1. 将 [wordpress下载地址](https://cn.wordpress.org/download/) 所下载的wordpress的tar压缩包利用FileZilla导入到/`var/www`目录下 

2. 解压缩到当前目录
`tar -xvf wordpress-5.7.2-zh_CN.tar.gz`

3. 授权
> `sudo chmod -R 777 /var/www/html/`  
`sudo chmod -R 777 /var/www/`

### 3.2 wordpress配置
1. 修改sites-available目录下更改conf文件
`vim /etc/apache2/sites-available/000-default.conf`

2. 在DocumentRoot /var/www/wordpress后添加以下内容来设置主页的位置
```
<Directory /var/www/wordpress/>
        AllowOverride All
</Directory>
```


3. 浏览器中输入：`[IP地址]/wordpress`
点击回车按键，我们将看到 WordPress 的安装页面，接下来是一个提示页面，你需要准备数据库连接信息：
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2019.50.55.png)
配置数据库信息（mySQL）：
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/BEB10A2F-DE8F-44B3-B143-DEA0428A83A2.png)
> 如果遇到了连接出错的问题，可以查看wordpress根目录下的config文件：  
>   ![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2020.00.03.png)  
* 方法一：
	* **2.修改sql里的user列表里的root的密码，让它不是空密码状态**
	* mysql> use sql;
	* mysql> ALTER USER ‘root’@‘localhost’ IDENTIFIED WITH mysql_native_password BY ‘新密码’;
	* mysql> flush privileges;
* 方法二：
>   ![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-07%2019.59.21.png)  
> 找到根目录下的 wp-config-sample.php 复制这个文件，并把它重新命名为wp-config.php当作备份，修改原来文件中的数据库的用户名和密码：  
> `cp wp-config-sample.php wp-config.php`  
>   
> *database_name_here* 改成任意你想命名的Mysql数据库名称  
>   
> *username_here* Mysql默认是root，如果你没改过用户名的话就改成root就ok，改了填上相应的用户名  
>   
> *password_here*  改成你的数据库密码  
>   
>   ![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/BC0659C2-39D9-4E23-A979-72B493962AFC.png)  
>   
> 进入config文件修改密码：  
> `vim wp-config-sample.php`  
>   
>   ![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/3CB4A002-1F35-47BF-A917-779ABD1F2836.png)  

### 3.3 Wordpress设置
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/E4F5BB23-23FA-4D1B-A40F-11888EC16CE3.png)


## 4. 购买域名与备案
1. 进入阿里云控制台，点击左上角的 **产品与服务** 菜单，将菜单滚动到最下面，选择 域名：
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/39B21536-1298-444D-8AA8-4AEB1F3B0326.png)

2. 搜索自己需要的域名并购买

3. 邮箱验证
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/D52E1C24-3652-4D6D-81DB-AF5B0364C6D8.png)

4. 实名认证 
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/56208318-3C2B-46D9-A96F-B64C9F1D8F71.png)

假设现在已经申请好了一个域名，接下来就是要备案，只有备案通过，才能将域名绑定到你的ECS服务器上。

5. 点击控制台右上方的菜单 **备案** → **备案专区**，进入阿里云备案系统，按照提示一步一步操作即可：
![](mac%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%98%BF%E9%87%8C%E4%BA%91+Wordpress%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%88%AA%E5%B1%8F2021-06-09%2021.54.07.png)
在域名备案期间，你可以继续通过 IP 地址设计自己的网站，等备案完成之后，再将域名绑定到虚拟主机上。


## 5. 解析
[如何在阿里云服务器上搭建wordpress博客？ - 知乎](https://www.zhihu.com/question/36495153)




## 6.配置主题
[Ubuntu如何开启FTP服务_一只青木呀-CSDN博客_ubuntu开启ftp服务](https://blog.csdn.net/weixin_45309916/article/details/107855067)

按着这个教程创建了用户，能连接上ftp，但是提示无法复制文件
[ubuntu 使用vsftpd 创建FTP服务（用户名密码登录，限制列出目录）_Lincoln 的专栏-CSDN博客_vsftpd](https://blog.csdn.net/soslinken/article/details/79304076)