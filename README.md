# Introduction

---
###sqli-labs简介 
对于想要学习web安全的同学 , 这是一个非常好的学习有关SQL注入的学习资料
类似于闯关的模式 , 每一个关卡都有非常多的思路和利用方式
这些关卡包含了各种常见的SQL注入姿势 : 
* 明注
* 盲注
 * 基于bool
 * 基于时间
 * 基于报错

> [Github地址](https://github.com/Audi-1/sqli-labs)

---
环境要求 ： 
php
mysql
web服务器(Aapche或者其他)
> 注 : 
这里笔者使用操作系统是ubuntu16.04 , 如果大家使用的操作系统是Widnows的话
区别只是在如何去安装和配置php/mysql/apache服务器
这些百度/Google上已经有很多很详细的教程了 , 就不再赘述
大家可以直接使用例如` xampp ` / ` wamp ` 这样的服务器套件

---
部署流程 : 
1. 搭建lamp环境
2. 从github上克隆最新的sqli-labs仓库
3. 将克隆的仓库移动到apache的web目录下
4. 配置php文件(数据库密码)
5. 浏览器访问启动页面进行安装

---
部署教程 : 
1. 搭建lamp环境 : 
```
sudo apt-get install apache2
sudo apt-get install php5
sudo apt-get install libapache2-mod-php5
sudo apt-get install mysql-server
```
> 注 : 如果你的ubuntu是16.* , 在安装php的时候可能会出现找不到php5的情况 , 这个是因为ubuntu16以后对php5不再进行支持 , 如果你使用`apt-get install php`的话 , 安装的默认为php7 , 但是这个问题是可以通过添加php5的第三方源来解决的 , 可以使用下面这条几条命令 : 
```
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install php5.6
```
2. 启动apache和mysql
```
sudo service apache2 start
sudo service mysql start
```
3. 测试php是否安装成功 , 使用php探针
在web目录下编写php代码保存为phpinfo.php
```php
<?php
        phpinfo();
?>
```
访问 `http://127.0.0.1/phpinfo.php` , 如果出现php的基本信息则说明php已经安装成功 , 这个时候就可以把这个探针文件删除
3. 测试php连接数据库是否成功
在web目录下编写php代码保存为 : database.php
```
<?php
        $con = mysql_connect("localhost","root","password"); 
        // 这里填写数据库的地址(由于是本机因此localhost或者127.0.0.1都可以)/用户名/密码
        if ($con) {
                echo "OK!";
        }else {
                die('Could not connect: ' . mysql_error());
        }
?>
```
然后访问`http://127.0.0.1/database.php` , 如果数据库配置正确的话 , 页面会显示`OK!` , 同理也可以删除这个文件
否则会显示错误信息 , 可以根据这个错误信息去寻找解决方案
2. 安装git
```
apt-get install git
```
3. 从github上克隆`sqli-labs`仓库
```
git clone https://github.com/Audi-1/sqli-labs.git
```
如果只是为了练习sql注入的话 , 这一步还有上面的安装git的那一步其实可以省略 , 因为github提供了一个直接下载仓库源码压缩包的功能 , 下载解压之后也是相同的效果
4. 进入sqli-labs目录, 修改数据库配置文件 : `sqli-labs/sql-connections/db-creds.inc`
```
<?php
//give your mysql connection username n password
$dbuser ='root';
$dbpass ='';
$dbname ="security";
$host = 'localhost';
$dbname1 = "challenges";
?>
```
5. 复制这个目录到apache的web目录(默认为 : `/var/www/html/`)
```
sudo cp -r ./sqli-labs /var/www/html/
```
6. 然后在浏览器中访问 : `http://127.0.0.1/` , 就可以看到启动的页面了
7. 点击页面中的`Setup/reset Database for labs`链接 , 让其进行安装(这个过程其实就是创建必要的数据库和表 , 并插入一些测试数据)

---
参考资料 : 
1. [ubuntu 搭建lamp](http://jingyan.baidu.com/article/a681b0de36ad683b18434691.html)
1. 如何使用git
2. 如何使用github
3. 如何安装配置apache服务器
4. 如何安装php
5. 如何安装mysqle
