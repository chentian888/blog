---
title: Linux入门
toc: true
date: 2019-08-30 17:27:27
categories: 运维
tags: linux
layout: false
---

# linux入门
## 一、命令基本格式
### 1.locate
* `locate 文件名`,在数据库空按文件名搜索，优点搜索速度更快，缺点是只能按照文件名搜索
* `/var/lib/mlocate`,搜索此目录下的数据库
* `/etc/updatedb.conf` 按照此配置规则进行搜索
开启搜索限制 PRUNE_BIND_MOUNTS="yes"
搜索时，不搜索的文件系统 PRUNEFS=
搜索时，不搜索的文件类型 PRUNENAMES=
搜索时，不搜索的路径 PRUNEPATHS=

系统默认/etc/updatedb.conf
```javascript
PRUNE_BIND_MOUNTS = "yes"
PRUNEFS = "9p afs anon_inodefs auto autofs bdev binfmt_misc cgroup cifs coda configfs cpuset debugfs devpts ecryptfs exofs fuse fuse.sshfs fusectl gfs gfs2 gpfs hugetlbfs inotifyfs iso9660 jffs2 lustre mqueue ncpfs nfs nfs4 nfsd pipefs proc ramfs rootfs rpc_pipefs securityfs selinuxfs sfs sockfs sysfs tmpfs ubifs udf usbfs fuse.glusterfs ceph fuse.ceph"
PRUNENAMES = ".git .hg .svn"
PRUNEPATHS = "/afs /media /mnt /net /sfs /tmp /udev /var/cache/ccache /var/lib/yum/yumdb /var/spool/cups /var/spool/squid /var/tmp /var/lib/ceph"
```


* `updatedb`
强制更新数据库,默认数据库一天更新1次
出现如下情况可执行更新数据库命令
>locate: can not stat () `/var/lib/mlocate/mlocate.db': No such file or directory

### 2.whereis和which
### 3.find
搜索所有的资源比较耗时

### 4.grep
### 5.find和grep的区别


## 二、文件处理命令
## 三、文件搜索命令
## 四、帮助命令
## 五、压缩与解压缩
## 六、关机和重启命令
## 七、其它常用命令
