---
title: Linux软件安装
toc: true
date: 2019-08-27 21:25:10
categories: 运维
tags: linux
---

# 所有安装均在centos6操作系统下进行

# LNMP环境搭建Yum版

## 一、挂载cd光盘
```
mount -o loop -t iso9660 CentOS-6.10-x86_64-bin-DVD1.iso /media/cdrom1
mount -o loop -t iso9660 CentOS-6.10-x86_64-bin-DVD2.iso /media/cdrom2
```

## 二、修改`yum`源为本地`yum`源
1. `cd /etc/yum.repos.d/`
2. `mv CentOS-Base.repo CentOS-Base.repo.bak`
3. `vi CentOS-Medis.repo`，将内容修改为如下
```
[c7-media]
name=CentOS-$releasever - Media
baseurl=file:///media/cdrom1/
        file:///media/cdrom2/
#        file:///media/cdrecorder/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

4. 清除之前`yum`源缓存`yum clean all`
5. 生成`yum`源新缓存`yum makecache`
6. 检测新`yum`源是否配置成功`yum -y install tree``yum -y install wget`安装成功表示新`yum`源配置成功


## 三、`nginx`官网指南
1. 进入官网`http://nginx.org`
2. 右侧导航找到`documentation`
3. 左上列表找到`Installing nginx`
4. 找到`Installation on Linux` 点击`packages`进入页面
5. 点击`RHEL/CentOS`查看官方给出的新建`nginx yum`仓库的方法
6. `Beginner’s Guide`是启动使用文档

## 四、配置`nginx` `yum`源
1. 进入到yum源根目录`/etc/yum.repos.d`
2. 创建`nginx yum`源文件`touch nginx.repo`
3. 按照官方说明编辑文件内容
```
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
```

4. 检测nginx源是否配置成功`yum search nginx`
```
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * c6-media: 
nginx-stable                                                                                        | 2.9 kB     00:00     
nginx-stable/primary_db                                                                             |  53 kB     00:00     
=================================================== N/S Matched: nginx ====================================================
nginx-debug.x86_64 : debug version of nginx
nginx-debuginfo.x86_64 : Debug information for package nginx
nginx-module-geoip.x86_64 : nginx geoip module
nginx-module-geoip-debuginfo.x86_64 : Debug information for package nginx-module-geoip
nginx-module-image-filter.x86_64 : nginx image filter module
nginx-module-image-filter-debuginfo.x86_64 : Debug information for package nginx-module-image-filter
nginx-module-njs.x86_64 : nginx nJScript module
nginx-module-njs-debuginfo.x86_64 : Debug information for package nginx-module-njs
nginx-module-perl.x86_64 : nginx perl module
nginx-module-perl-debuginfo.x86_64 : Debug information for package nginx-module-perl
nginx-module-xslt.x86_64 : nginx xslt module
nginx-module-xslt-debuginfo.x86_64 : Debug information for package nginx-module-xslt
nginx-nr-agent.noarch : New Relic agent for NGINX and NGINX Plus
pcp-pmda-nginx.x86_64 : Performance Co-Pilot (PCP) metrics for the Nginx Webserver
nginx.x86_64 : High performance web server

```

## 五、安装/启动Nginx
1. 安装 `yum -y install nginx`
2. `whereis nginx` 查看相关信息

```
nginx: /usr/sbin/nginx /etc/nginx /usr/lib64/nginx /usr/share/nginx /usr/share/man/man8/nginx.8.gz
```
* `nginx.conf`主配置文件
* `ll //etc/nginx/conf.d`目录下有`default.conf`

3. 进入`/etc/nginx`
4. 启动`nginx`进入官网查看`Beginner’s Guide` 启动使用文档`nginx`

5. `ps -aux | grep nginx`看到如下信息则`nginx`启动成功
```
root      1946  0.0  0.1  47348  1084 ?        Ss   04:14   0:00 nginx: master process nginx
nginx     1947  0.0  0.1  47756  1816 ?        S    04:14   0:00 nginx: worker process
root      1949  0.0  0.0 103332   904 pts/1    S+   04:14   0:00 grep nginx

```
6. 在本机访问虚拟机`nginx`，如果访问不成功有可能需要关闭防火墙
7. 查看防火墙状态 `service iptables status`
8. 关闭防火墙 `service iptables stop`
9. 看到`nginx`欢迎页表示安装启动成功

`nginx.conf`文件中有如下配置`include /etc/nginx/conf.d/*.conf`;
意思是`conf.d`目录下的所有`.conf`结尾的文件都会被加载到`nginx.conf`中


## 六、安装php，配置Nginx识别php文件

### 1.安装php并新建测试文件
* `yum -y install php`
* `php -v`查看是否安装成功
* 在`nginx`根目录(`/usr/share/nginx`)下新建测试文件 `vi test.html vi index.php`

### 2.修改nginx配置将.php类型文件转发到php-fpm模块解释器
`php-fpm`是`php`解释器，属于一个单独的模块，需要安装启动

1. 安装`php-fpm` `yum -y install php-fpm`
2. 启动`fpm` `service php-fpm start`
3. `vi /etc/nginx/conf.d/default.conf`，解开如下注释
```
# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
#
location ~ \.php$ {
    root           html;// 表示php所在目录
    fastcgi_pass   127.0.0.1:9000;//php-fpm 监听的端口
    fastcgi_index  index.php;// 默认请求的文件
    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    include        fastcgi_params;
}
```
4. 修改参数`fastcgi_param`，`SCRIPT_FILENAME  $document_root$fastcgi_script_name`
`$document_root`表示指向`root`字段所指向的目录

5. 重启`nginx -s reload`
6. 访问 `http://192.168.2.119/index.php` 会出现`File not found.`
7. 查看日志为何出错 `cat /var/log/nginx/error.log`
```
2019/08/26 04:16:35 [error] 1947#1947: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 192.168.2.107, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "192.168.2.119", referrer: "http://192.168.2.119/"
2019/08/26 05:05:04 [notice] 2039#2039: signal process started
2019/08/26 05:05:14 [error] 2040#2040: *9 FastCGI sent in stderr: "Primary script unknown" while reading response header from upstream, client: 192.168.2.107, server: localhost, request: "GET /index.php HTTP/1.1", upstream: "fastcgi://127.0.0.1:9000", host: "192.168.2.119"
```
6. 修改`default.conf` 中`php`配置字段`root`为绝对路径`/usr/share/nginx/html`，重启`nginx`


## 七、安装/启动mysql
1. `yum -y install mysql-server mysql`
2. 启动 `service mysqld start`，出现如下提示表示启动成功
```
初始化 MySQL 数据库： Installing MySQL system tables...
OK
Filling help tables...
OK

To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your system

PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
To do so, start the server, then issue the following commands:

/usr/bin/mysqladmin -u root password 'new-password'
/usr/bin/mysqladmin -u root -h localhost.localdomain password 'new-password'

Alternatively you can run:
/usr/bin/mysql_secure_installation

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the manual for more instructions.

You can start the MySQL daemon with:
cd /usr ; /usr/bin/mysqld_safe &

You can test the MySQL daemon with mysql-test-run.pl
cd /usr/mysql-test ; perl mysql-test-run.pl

Please report any problems with the /usr/bin/mysqlbug script!

                                                           [确定]
正在启动 mysqld：                                          [确定]

```
3. 根据提示设置密码`/usr/bin/mysqladmin -u root password '123456'`
4. 重启`mysql` `service mysqld restart`
5. 连接 `mysql -u root -p`
6. 在`nginx root`目录下增加测试文件 `vi db.php`
```
<?php
    $link=mysql_connect('localhost','root','123456');
    if($link) echo 'FAILD';
    else echo 'SUCCESS';
?>
```
7. 页面报错，查看`php-fpm`日志`cat /var/log/php-fpm/www-error.log`，会发现提示如下错误
```
[26-Aug-2019 06:05:29] PHP Fatal error:  Call to undefined function mysql_connect() in /usr/share/nginx/html/db.php on line 3

```
缺少`mysql_connect`此方法
8. `yum search php`会发现有这样一个安装包 `php-mysql.x86_64 : A module for PHP applications that use MySQL databases`
9. 安装`php-mysql.x86_64`
10 .重启`php-fpm`服务 `service php-fpm restart`
11. 再次查看`http://192.168.2.119/db.php`，连接成功

## 总结
1. 配置`yum`本地源，`nginx`源
2. `yum -y install nginx php php-fpm mysql-server mysql php-mysql`
3. 配置`nginx`，将`php`请求转发给`php-fpm`解释器
4. 报错，查看`nginx`或者`php-fpm`报错日志`error.log/www-error.log`



## LNMP环境搭建源码版