---
layout:     post
title:      "VoLTE领域，4G切换2/3G基本知识"
subtitle:   "SRVCC切换判别"
date:       2018-1-25 10:10:00
author:     "Ace"
header-img: "img/post-bg-kuaidi.jpg"
tags:
    - VoLTE
    - LTE
    - SRVCC
    - eSRVCC
    - CCO
    - Redirection
---


> 今天是2018年1月20日，最近一直在研究VoLTE的技术细节，尤其是SRVCC和eSRVCC。在研究的过程中，我觉得有必要对这里的一些概念和技术进行总结，以方便后来的人员快速经历这些阶段

<br>首先列出一些专业术语。
<li>联机状态:是终端的一种工作模式，类似人的清醒状态。在规范和参考书中，联机状态称为Connect Mode，所以也有翻译为连接态的。</li>
<li>PSHO:（异系统切换）是指由LTE系统从联机状态转换到异系统的联机状态。4G数据业务切换至3G数据业务。通过测量、切换准备，切换命令，切换完成，RRC连接没有中断，数据业务完美切换。</li>
<li>Redirection(重定向)，指终端从一种制式(4G)的联机状态转换到另一种制式（2/3G）的待机状态。传输数据时4G通过release with redirect 进入2/3G待机状态，进而随机接入2/3G网络，保证数据不间断。</li>
<li>CCO，是Redirection一种，专指4G切换至2G的专用流程。</li>
<li></li>
<br>

<br><b>异系统切换策略</b>

<br><br>
1. 安装 Git 软件，执行下面的命令
<br><br>

sudo yum update && sudo yum install git

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
<br>    "host" => "localhost",     //getenv('OPENSHIFT_MYSQL_DB_HOST'),
<br>    "port" => 3306,         // getenv('OPENSHIFT_MYSQL_DB_PORT'),
<br>    "name" => "open",
<br>    "user" => "open",         // getenv('OPENSHIFT_MYSQL_DB_USERNAME'),
<br>    "pass" => "123456",     // getenv('OPENSHIFT_MYSQL_DB_PASSWORD')
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