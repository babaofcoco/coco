---
layout:     post
title:      "VoLTE����4G�л�2/3G����֪ʶ"
subtitle:   "SRVCC�л��б�"
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


> ������2018��1��20�գ����һֱ���о�VoLTE�ļ���ϸ�ڣ�������SRVCC��eSRVCC�����о��Ĺ����У��Ҿ����б�Ҫ�������һЩ����ͼ��������ܽᣬ�Է����������Ա���پ�����Щ�׶�

<br>�����г�һЩרҵ���
<li>����״̬:���ն˵�һ�ֹ���ģʽ�������˵�����״̬���ڹ淶�Ͳο����У�����״̬��ΪConnect Mode������Ҳ�з���Ϊ����̬�ġ�</li>
<li>PSHO:����ϵͳ�л�����ָ��LTEϵͳ������״̬ת������ϵͳ������״̬��4G����ҵ���л���3G����ҵ��ͨ���������л�׼�����л�����л���ɣ�RRC����û���жϣ�����ҵ�������л���</li>
<li>Redirection(�ض���)��ָ�ն˴�һ����ʽ(4G)������״̬ת������һ����ʽ��2/3G���Ĵ���״̬����������ʱ4Gͨ��release with redirect ����2/3G����״̬�������������2/3G���磬��֤���ݲ���ϡ�</li>
<li>CCO����Redirectionһ�֣�רָ4G�л���2G��ר�����̡�</li>
<li></li>
<br>

<br><b>��ϵͳ�л�����</b>

<br><br>
1. ��װ Git �����ִ�����������
<br><br>

sudo yum update && sudo yum install git

<br><br>        
2. �ı�Ŀ¼��httpd��ҳ��·�� /var/www ��httpd�汾>= V2.5 ·����/var/www/html
<br><br>


cd /var/www


<br><br>
3. ���ش��뵽��Ŀ¼��open�£�
<br><br>


git clone https://github.com/subins2000/open.git open


<br><br>
4. ��openĿ¼������open.subinsb.com�ַ������滻Ϊ���Լ��ķ�������������IP����������192.168.16.204��ע����������Ŀ¼open������/etc/httpd/conf/httpd.conf�ϼ���һ���������������԰�����openĿ¼�����ҵ����������Ĺ���Ŀ¼��
<br><br>


<br>
<br>NameVirtualHost *:80
<br><VirtualHost *:80>
<br>    DocumentRoot /home/apache/www/open
<br></VirtualHost>


<br><br>
5. ����һ�����ݿ� open
<br>


<br>mysql -uroot -p
<br>create database open;


<br><br>        
6. �������ݿ�����Ҫ�ı����б���openĿ¼�µ�tables.sql�ļ���

<br><br>
7. ����open��վ�������ļ�����Ҫ��config.php�ļ����������ݿ����ƺ��û�������
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
8. ����myqld��httpd����ie��½�鿴http://192.168.16.204
<br>


<br>service mysqld restart
<br>service httpd restart
        

<br><br>

<b>����������</b>
<br><br>

1. ��װ�����У���ִ�е�4����ʱ��ע��鿴index.php����ļ���������һ��ע�͵Ĵ���Ҫ����open��վ����������Ż���ע�͡������ǣ�
<br>
<br>

<li>����վ�ĸ�Ŀ¼������վ������û����Ŀ¼�Ļ�����Ҫ�򿪣����磺http://192.168.16.204; http://open.org  http://192.168.16.204:8800;
<li>����վ�ĸ�Ŀ¼�������µ���Ŀ¼����Ҫ����򿪣����磺http://192.168.16.204/open;   http://open.org/open  http://192.168.16.204:8800/open;



<br><br>
<br>/* This is not needed if Open is in site's document root, but needed if Open is in a sub folder
<br>---------------------
<br>// Make the request URL relative to the base URL of Lobby installation. http://localhost/open will be changed to "/" and http://open.local to "/"
<br>$lobbyBase = str_replace($_SERVER['DOCUMENT_ROOT'], "", $docRoot);
<br>$_SERVER['REQUEST_URI'] = str_replace($lobbyBase, "", $_SERVER['REQUEST_URI']);
<br>* -------------------
<br>*/


<br><br>
2. MySQLsockλ�ñ��Ҹı��ˣ�����PDO����mysql��ʱ����ִ���

<br><i>Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.soc</i>

<br>
<br>���������
<br>�ҵ�PDO�������ݿ����ӵĵط����ϡ�unix_socket=/home/mysqldata/mysql/mysql.sock��,�����漰�������ļ�./inc/class.open.php��./inc/class.logsys.php
<br>


<br>new PDO("mysql:dbname={$database["name"]};unix_socket=/home/mysqldata/mysql/mysql.sock;host={$database["host"]};port={$database["port"]}"


<br><br>
3. ��ע���ʱ����ִ���
<br><i>Fatal error: Call to undefined function mcrypt_get_iv_size() in /home/apache/www/open/inc/class.open.php on line 79</i>

<br><br>
���������

<br>yum install php-mcrypt mcrypt
<br>���ļ�/etc/php.ini ����� extension=mcrypt.so

<br><br>
3. ע���ʱ����֣�
<br>
<i>SMTP Error: Could not authenticate. Warning: file_put_contents(/home/apache/www/open/inc/nextMailAccount.txt): failed to open stream: Permission denied in /home/apache/www/open/inc/class.open.php on line 617</i>
<br><br>
���������
<br>
��
<br><br>


Over