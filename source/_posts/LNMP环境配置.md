---
title: LNMP环境配置
toc: true
date: 2019-08-29 17:27:27
categories: 运维
tags: linux
---

# LNMP环境搭建Yum版
## 环境
* `centos6`

## 一、挂载cd光盘
```js
mount -o loop -t iso9660 CentOS-6.10-x86_64-bin-DVD1.iso /media/cdrom1
mount -o loop -t iso9660 CentOS-6.10-x86_64-bin-DVD2.iso /media/cdrom2
```

## 二、修改`yum`源为本地`yum`源
1. `cd /etc/yum.repos.d/`
2. `mv CentOS-Base.repo CentOS-Base.repo.bak`
3. `vi CentOS-Medis.repo`，将内容修改为如下
```js
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
```js
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
```js
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
```js
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
```js
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
```js
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
```php
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



# LNMP环境搭建源码版
## 环境及版本

* centos6 
* nginx1.14 
* php5.5.6 
* mysql-5.6.45 
* pcre-8.41
* zlib-1.2.11

## 源码包获取
1. 获取php
`https://www.php.net/`->`downloads`->`Old archives`->`PHP Museum`

2. 获取nginx
`http://nginx.org`->`download`->`Stable version`

3. 获取mysql
mysql使用编译版不适用源码版
`https://www.mysql.com/`->`download`->`MySQL Community (GPL) Downloads`->`MySQL Community Server`->`Linux Generic`

## 编译环境安装
```js
yum -y install gcc gcc-c++
```

## 源码安装Nginx
### 依赖包安装
1. 依赖`yum -y install openssl openssl-devel`
2. `pcre-8.41`进入官网或从`nginx`安装配置说明中的`--with-pcre=path`参数找到官网入口
`http://www.pcre.org/->https://sourceforge.net/projects/pcre/files/->pcre->8.41`
3. `zlib-1.2.11`进入官网或从`nginx`安装配置说明中的`--with-zlib=path`参数找到官网入口
`http://zlib.net/->zlib source code, version 1.2.11, tar.gz format`

### 配置、编译、安装
1. 配置
配置信息在官网中 `documentation`->`Building nginx from Sources`栏目下
```js
./configure
    --sbin-path=/usr/local/nginx/nginx
    --conf-path=/usr/local/nginx/nginx.conf
    --pid-path=/usr/local/nginx/nginx.pid
    --with-http_ssl_module
    --with-pcre=../pcre-8.41
    --with-zlib=../zlib-1.2.11
```
执行上述配置命令后会出现错误提示，提示缺少`OpenSSL`库
```js
./configure: error: SSL modules require the OpenSSL library.
You can either do not enable the modules, or install the OpenSSL library
into the system, or build the OpenSSL library statically from the source
with nginx by using --with-openssl=<path> option.
```
使用`yum`安装`yum  -y install openssl openssl-devel`
并指定`pcre`和`zlib`库路径`../pcre-8.41`、`../zlib-1.2.11`

2. 编译&&安装
`make&&make install`

### 效果测试
1. 启动ng `/usr/local/nginx/nginx`
2. 新建测试文件`vim /usr/local/nginx/html/index.php`
3. 本机访问虚拟机ip`192.168.0.108/index.php`

## 源码安装PHP

### 依赖包安装
1. `yum -y install libxml2-devle`

### 配置、编译、安装
配置按照官网步骤进行
`Documentation->Chinese (Simplified)->Unix 系统下的安装->Unix 系统下的 Nginx 1.4.x`

1. 获取并解压 PHP 源代码
```js
tar zxf php-x.x.x
```

2. 配置并构建 PHP。在此步骤您可以使用很多选项自定义 PHP，例如启用某些扩展等。 运行 ./configure --help 命令来获得完整的可用选项清单。 在本示例中，我们仅进行包含 PHP-FPM 和 MySQL 支持的简单配置

```js
cd ../php-x.x.x
./configure --enable-fpm --with-mysqli --with-pdo-mysql
make
sudo make install
```
看到`Thank you for using PHP`.则表示配置成功

3. 创建配置文件，并将其复制到正确的位置。
```js
cp php.ini-development /usr/local/php/php.ini
cp /usr/local/etc/php-fpm.d/www.conf.default /usr/local/etc/php-fpm.d/www.conf
cp sapi/fpm/php-fpm /usr/local/bin
```
其中第二条在当前环境下需要改成
```js
cp /usr/local/etc/php-fpm.conf.default /usr/local/etc/php-fpm.conf
```

4. 需要着重提醒的是，如果文件不存在，则阻止 `Nginx` 将请求发送到后端的 `PHP-FPM` 模块， 以避免遭受恶意脚本注入的攻击。将`php.ini`文件中的配置项`cgi.fix_pathinfo`设置为`0`
```js
vim /usr/local/php/php.ini
cgi.fix_pathinfo=0
```

5. 在启动服务之前，需要修改 php-fpm.conf 配置文件，确保 php-fpm 模块使用 www-data 用户和 www-data 用户组的身份运行。
```js
vim /usr/local/etc/php-fpm.conf
```
找到以下内容并修改：
```js
; Unix user/group of processes
; Note: The user is mandatory. If the group is not set, the default user's group
;       will be used.
user = www-data
group = www-data
```
然后启动 php-fpm 服务：
```js
/usr/local/bin/php-fpm
```

6. 配置 Nginx 使其支持 PHP 应用
```js
vim /usr/local/nginx/conf/nginx.conf
```
修改默认的 `location` 块，使其支持 `.php` 文件：
```js
location / {
    root   html;
    index  index.php index.html index.htm;
}
```
下一步配置来保证对于 `.php` 文件的请求将被传送到后端的 `PHP-FPM` 模块， 取消默认的 `PHP` 配置块的注释，并修改为下面的内容：
```js
location ~* \.php$ {
    fastcgi_index   index.php;
    fastcgi_pass    127.0.0.1:9000;
    include         fastcgi_params;
    fastcgi_param   SCRIPT_FILENAME    $document_root$fastcgi_script_name;
    fastcgi_param   SCRIPT_NAME        $fastcgi_script_name;
}
```

7. 重启 `Nginx`
```js
sudo /usr/local/nginx/sbin/nginx -s stop
sudo /usr/local/nginx/sbin/nginx
```

## 源码安装MySQL
### 依赖包安装
### 配置、安装
到mysql官网查看安装步骤
`https://www.mysql.com/->DOCUMENTATION->MySQL Server->MySQL 5.6 Reference Manual->Installing and Upgrading MySQL->Installing MySQL on Unix/Linux Using Generic Binaries`

找到如下语句，即安装步骤
`To install and use a MySQL binary distribution, the command sequence looks like this:`

```js
shell> groupadd mysql
shell> useradd -r -g mysql -s /bin/false mysql
shell> cd /usr/local
shell> tar zxvf /path/to/mysql-VERSION-OS.tar.gz
shell> ln -s full-path-to-mysql-VERSION-OS mysql
shell> cd mysql
shell> scripts/mysql_install_db --user=mysql
shell> bin/mysqld_safe --user=mysql &
# Next command is optional
shell> cp support-files/mysql.server /etc/init.d/mysql.server
```

调整下命令执行顺序，按照如下步骤执行命令
```js
1. cp /root/mysql /opt/mysql
2. cd /opt/mysql
3. groupadd mysql
4. useradd -r -g mysql -s /bin/false mysql
5. cd /usr/local
6. ln -s /opt/mysql mysql
7. cd /usr/local/mysql
8. scripts/mysql_install_db --user=mysql
```
执行第8步成功后会提示如下命令。需要按照命令修改密码

```js
To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your system

PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
To do so, start the server, then issue the following commands:

  ./bin/mysqladmin -u root password 'new-password'
  ./bin/mysqladmin -u root -h localhost.localdomain password 'new-password'

Alternatively you can run:

  ./bin/mysql_secure_installation
```

修改密码
```js
./bin/mysqladmin -u root password 'new-password'
```

## 测试

命令行连接数据库
```js
mysql -u root -p
```
提示
```js
-bash: mysql: command not found
```

应为我们只通过源码安装了服务端并没有安装客户端，通过`yum`安装客户端`yum -y install mysql`

再次尝试用命令连接数据库

```js
mysql -u root -p
```
`mysql.sock`文件报错

```js
ERROR 2002 (HY000):Cant connect to local MySQL server through socket '/var/lib/mysql/mysql.sock'(2)
```

查找mysql.sock文件 
```js
find / -name mysql.sock
```

得到结果
```js
/tmp/mysql.sock
```


将`/usr/local/mysql`文件夹中的`my.cnf`(执行安装命令后才会生成)配置文件复制到`/etc`目录下
```js
cp /usr/local/mysql /etc/
```

编辑配置文件，并新增新节点
```js
vim /etc/my.cnf
```

```js
[client]
socket=/tmp/mysql.sock
```

### 数据库服务管理

