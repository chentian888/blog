---
title: Linux软件安装
toc: true
date: 2019-08-27 21:25:10
categories: 运维
tags: linux
---

# 准备工作
**挂载光盘**
```
查看已经挂在的光盘
df -h
```

```
查看端口占用情况
netstat -tlun
```
* 方式一，使用虚拟机连接的`cd`，在光盘设置开启使用`iso`镜像文件
挂载`cd`到`/mnt/cdrom`目录下
```
mkdir /mnt/cdrom
mound /dev/sr0 /mnt/cdrom
```
所有`rpm`包都在`/mnt/cdrom/Package/`目录下

* 方法二，将`iso`包上传到服务器中手动挂载
将`CentOS-6.10-x86_64-bin-DVD1.iso` `CentOS-6.10-x86_64-bin-DVD2.iso`上传到`/mnt`中
创建文件夹`/mnt/cdrom1`、`/mnt/cdrom2`分别挂载2个`iso`文件
分别执行命令

```
mount -o loop -t iso9660 CentOS-6.10-x86_64-bin-DVD1.iso /mnt/cdrom1
mount -o loop -t iso9660 CentOS-6.10-x86_64-bin-DVD2.iso /mnt/cdrom2
```

**说明**安装过的`rpm`包只用输入包名，没有安装果的`rpm`包必须输入包全名，下文的安装说明均以安装`Apache`为例


# RPM 安装软件
## rpm命令

**一、安装命令**
```
rpm -ivh httpd-
```

* `-i install`安装
* `-v verbose`显示详细信息
* `-h hash`显示进度
* `--nodeps`不检测依赖性

以安装`apache`为例子，需要依次安装下列`rpm`包
```
httpd-2.4.6-88.el7.centos.x86_64.rpm
httpd-devel-2.4.6-88.el7.centos.x86_64.rpm
httpd-manual-2.4.6-88.el7.centos.noarch.rpm
httpd-tools-2.4.6-88.el7.centos.x86_64.rpm
```

**说明**
`rpm`安装软件包的过程中会出现各种依赖报错，找到需要依赖的包或者文件，依次用`rpm`进行安装即可，若出现依赖包过多繁杂的情况可用`yum`安装缺失的包即可
* 若出现类似报错`libdb-devel(x86-64) is needed by apr-util-devel-1.5.2-6.el7.x86_64`表示安装当前`rpm`包缺少该依赖，安装当前所缺依赖即可
* 若出现库文件依赖例如：`libaprutil-1.so.0()(64bit) is needed by apr-util-devel-1.5.2-6.el7.x86_64`则需要去网站(`http://rpmfind.net`)查询该库文件包含在哪个`rpm`包中，并安装对应的`rpm`包

**二、更新**
```
rpm -Uvh 包全名 
```

如果包全名是没有安装的`rpm`包，则更新命令和安装命令效果一样

**三、卸载**
```
rpm -e 包名
```

* `-e` 卸载(erase)
* `--nodeps`不检查依赖性


## rpm查询

一、查询命令
```
rpm -q
```

* `-q query`查询
* `-qa all` all查询所有安装rpm包
* `-qi infomation` 查询软件信息
* `-qp package` 查询未安装包信息
* `-ql list` 列表
* `-qf file` 查询文件所属哪个rpm包
* `-qR Require` 查询rpm包需要依赖哪些包

**小技巧** `rpm -q|grep httpd`


## rpm校验
**一、命令**
```
rpm -V
```

* `-V verify`验证

**二、核验结果**

* `S size` 文件大小是否改变
* `M mime` 文件类型或者文件权限rwx是否改变
* `5 mdd5` 文件MD5校验是否改变，文件类容是否改变
* `D device` 设备主从代码是否改变
* `L` 文件路径是否改变
* `U` 文件属性(所有者)是否改变
* `G group` 文件属性组是否改变
* `T` 文件修改时间是否改变

* `c config file`配置文件
* `d documentation`文档
* `g ghost file`鬼文件，很少见，就是该文件不应该被包含在该rpm包中
* `L Licese file`授权文件
* `r read me`描述文件

## rpm包中文件提取

**一、命令**

```
rpm2cpio 包全名 | cpio -idv .文件绝对路径
```
* `-rpm2cpio`将`rpm`包转换为`cpio`格式命令
* `-cpio`是一个标准工具，它用于创建软件档案文件和从档案文件中提取文件


## rpm包默认安装路径

* `/etc/`配置文件安装目录
* `/usr/bin/`可执行命令安装目录
* `/usr/lib/`程序所使用函数库保存位置
* `/usr/share/doc/`基本软件使用手册保存位置
* `/usr/share/man/`帮助文件保存位置

## Apache启动
`rpm`包安装的服务可以使用系统服务管理命令来管理，例如`rpm`包安装的`apache`的启动方法是

```
/etc/rc.d/init.d/httpd start(linux标准启动方法)
service httpd start
```

# YUM 安装软件

## 准备工作
**一、搭建本地yum源**
1. 进入到yum源目录编辑`cd /etc/yum.repos.d/`
2. 重命名默认yunm源文件`mv CentOS-Base.repo CentOS-Base.repo.bak`
3. 修改目标yum源文件`vim /etc/yum.repos.d/CentOS-media.repo`

原始内容
```
[c7-media]
name=CentOS-$releasever - Media
baseurl=file:///media/CentOS/
        file:///media/cdrom/
        file:///media/cdrecorder/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

修改后类容
```
[c7-media]
name=CentOS-$releasever - Media
baseurl=file:///mnt/cdrom/
#        file:///media/cdrom/
#        file:///media/cdrecorder/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

## yum命令
* 获取可以安装yum软件列表`yum list`
* 搜索yum源是否存在某软件`yum search 包名`
* 安装软件`yum -y install 包名`，`-y yes`
* 更新软件`yum -y update 包名`
* 卸载软件`yum -y remove 包名`

## yum组管理命令
* 获取可以安装组列表`yum grouplist`
* 安装组`yum groupinstall 软件组名(只能是英文)`
* 卸载组`yum groupremove 软件组名(只能是英文)`


# 源码安装
**说明**
源码包安装的服务不能被服务管理命令管理，因为没有安装到牧人路径中，所有只能用绝对路径进行服务管理，如
`/usr/local/apache2/bin/apachectl start`

## 安装apache(http://archive.apache.org/dist/httpd/)
一、下载
使用`wget http://archive.apache.org/dist/httpd/httpd-2.2.9.tar.gz`下载，或者手动下载将安装包上传至服务器`/root`目录下

二、解压缩
`tar -zxvf httpd2.2.9.tar.gz`

三、指定源码包配置/安装

* 定义需要的功能选项
* 检测系统环境是否符合安装要求
* 把定义好的功能选项和检测系统环境的信息都写入Makefile文件，用于后续的编辑

1. `httpd2.2.9`目录中存在`configure`文件，基本上每个源码包都会有这个文件，`./configure --help`用于查看当前源码包支持的安装配置项
`./configure`命令用于软件配置与检查
2.  `httpd2.2.9`目录中存在`INSTALL`文件，基本上每个源码包都会有这个文件`vim INSTALL`会看到安装指引和启动说明等信息
3. 分别执行以下三个命令`./configure --prefix=/usr/local/apache2`、`make`、`make install`

四、启动Apache
1. 停止之前使用`rpm`方式安装的`apache`服务`service httpd stop`
2. 启动使用源码包安装的`apache`服务  `/usr/local/apache2/bin/apachectl start`

**提示**
报错这个不要紧
```
httpd: Could not reliably determine the server's fully qualified domain name, using localhost.localdomain for ServerName
```
五、测试`Apache`服务是否启动成功

* 从本机访问虚拟机`http://192.168.2.114/`
* 如果访问不通请检查防火墙是否开启，若开启，关闭防火墙
* 查看防火墙状态`firewall-cmd --state`
* 停止`firewall` `systemctl stop firewalld.service`

**注意**
1. `make`之后如果报错，报错之后可以执行`make clean`清除编译后的缓存/临时文件文件，清除整个安装过程，就跟没有安装一样
2. `make install`之后如果报错，需要执行`make clean`并且删除`--prefix`指定的安装目录


# 脚本安装
## 网址
1. https://lnmp.org/
2. 下载lnmp1.6-full.tar.gz按照文档执行安装命令