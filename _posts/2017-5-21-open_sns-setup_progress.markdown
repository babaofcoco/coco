---
layout:     post
title:      "开源社交网站open，安装以及遇到的问题"
subtitle:   "open安装"
date:       2017-5-21 13:10:00
author:     "Ace"
header-img: "img/post-bg-kuaidi.jpg"
tags:
    - facebook
    - SNS
    - open
    - linux
---


> 今天是2017年5月21日，今天在浏览github上的开源项目时，偶然看到一个开源的社交网站，名字起得也非常开源，就叫open。出于好奇，想看看这个开源的社交网站长什么样子，所以就根据说明在自己的虚拟机上安装了一下，在这个过程中也遇到很多问题。为了让更多人了解这个开源项目，我把安装过程和遇到的问题整理出来。

<br>首先列出一些基本信息。

<li>项目发起人：Subin Siby
<li>开源项目的地址：http://open.subinsb.com </li>
<li>博客地址：http://open.subinsb.com/blog</li>
<li>github地址：https://github.com/subins2000/open</li>
<br>

<br><b>Linux安装过程：</b>

<br><br>
1. 安装 Git 软件，执行下面的命令
<br><br>

sudo apt-get update && sudo apt-get install git

<br><br>		
2. 改变目录到httpd的页面路径 /var/www 当httpd版本>= V2.5 路径是/var/www/html
<br><br>


cd /var/www


<br><br>
3. 下载代码到此目录的open下，
<br><br>


git clone https://github.com/subins2000/open.git open


<br><br>
4. 把open目录下所有open.subinsb.com字符串，替换为你自己的服务器域名或者IP，我这里是192.168.16.204。注意这里有子目录open，我在/etc/httpd/conf/httpd.conf上加了一个虚拟域名，所以把整个open目录当成我的虚拟域名的工作目录。
<br><br>


<br>
<br>NameVirtualHost *:80
<br><VirtualHost *:80>
<br>    DocumentRoot /home/apache/www/open
<br></VirtualHost>


<br><br>
5. 创建一个数据库 open
<br>


<br>mysql -uroot -p
<br>create database open;


<br><br>		
6. 创建数据库所需要的表，所有表都在open目录下的tables.sql文件中

<br><br>
7. 配置open网站的配置文件，主要是config.php文件，更改数据库名称和用户名密码
<br>

<br>
<br>define("DATABASE", serialize(array(
<br>	"host" => "localhost", 	//getenv('OPENSHIFT_MYSQL_DB_HOST'),
<br>	"port" => 3306, 		// getenv('OPENSHIFT_MYSQL_DB_PORT'),
<br>	"name" => "open",
<br>	"user" => "open", 		// getenv('OPENSHIFT_MYSQL_DB_USERNAME'),
<br>	"pass" => "123456", 	// getenv('OPENSHIFT_MYSQL_DB_PASSWORD')
<br>)));


<br><br>
8. 重启myqld、httpd，用ie登陆查看http://192.168.16.204
<br>


<br>service mysqld restart
<br>service httpd restart
		

<br><br>

<b>遇到的问题</b>
<br><br>

1. 安装过程中，在执行第4步的时候，注意查看index.php这个文件。里面有一行注释的代码要根据open网站的情况，开放或者注释。规则是，
<br>
<br>

<li>当网站的根目录就是网站域名，没有子目录的话不需要打开，例如：http://192.168.16.204; http://open.org  http://192.168.16.204:8800;
<li>当网站的根目录是域名下的子目录，需要将其打开，例如：http://192.168.16.204/open;   http://open.org/open  http://192.168.16.204:8800/open;



<br><br>
<br>/* This is not needed if Open is in site's document root, but needed if Open is in a sub folder
<br>---------------------
<br>// Make the request URL relative to the base URL of Lobby installation. http://localhost/open will be changed to "/" and http://open.local to "/"
<br>$lobbyBase = str_replace($_SERVER['DOCUMENT_ROOT'], "", $docRoot);
<br>$_SERVER['REQUEST_URI'] = str_replace($lobbyBase, "", $_SERVER['REQUEST_URI']);
<br>* -------------------
<br>*/


<br><br>
2. MySQLsock位置被我改变了，导致PDO访问mysql的时候出现错误。

<br><i>Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.soc</i>

<br>
<br>解决方法：
<br>找到PDO建林数据库连接的地方加上“unix_socket=/home/mysqldata/mysql/mysql.sock”,这里涉及到两个文件./inc/class.open.php和./inc/class.logsys.php
<br>


<br>new PDO("mysql:dbname={$database["name"]};unix_socket=/home/mysqldata/mysql/mysql.sock;host={$database["host"]};port={$database["port"]}"


<br><br>
3. 在注册的时候出现错误：
<br><i>Fatal error: Call to undefined function mcrypt_get_iv_size() in /home/apache/www/open/inc/class.open.php on line 79</i>

<br><br>
解决方法：

<br>yum install php-mcrypt mcrypt
<br>在文件/etc/php.ini 中添加 extension=mcrypt.so

<br><br>
3. 注册的时候出现：
<br>
<i>SMTP Error: Could not authenticate. Warning: file_put_contents(/home/apache/www/open/inc/nextMailAccount.txt): failed to open stream: Permission denied in /home/apache/www/open/inc/class.open.php on line 617</i>
<br><br>
解决方法：
<br>
无
<br><br>


Over