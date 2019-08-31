---
title: Linux入门
toc: true
date: 2019-08-30 17:27:27
updated: {{date}}
categories: 运维
tags: linux
layout: true
---

# linux入门
## 一、命令基本格式
## 二、文件处理命令
## 三、文件搜索命令

### locate
* `locate 文件名`,在数据库空按文件名搜索，优点搜索速度更快，缺点是只能按照文件名搜索
* `/var/lib/mlocate`,搜索此目录下的数据库
* `/etc/updatedb.conf` 按照此配置规则进行搜索
- 开启搜索限制 `PRUNE_BIND_MOUNTS="yes"`
- 搜索时，不搜索的文件系统 `PRUNEFS=`
- 搜索时，不搜索的文件类型 `PRUNENAMES=`
- 搜索时，不搜索的路径 `PRUNEPATHS=`

系统默认`/etc/updatedb.conf`

```javascript
PRUNE_BIND_MOUNTS = "yes"
PRUNEFS = "9p afs anon_inodefs auto autofs bdev binfmt_misc cgroup cifs coda configfs cpuset debugfs devpts ecryptfs exofs fuse fuse.sshfs fusectl gfs gfs2 gpfs hugetlbfs inotifyfs iso9660 jffs2 lustre mqueue ncpfs nfs nfs4 nfsd pipefs proc ramfs rootfs rpc_pipefs securityfs selinuxfs sfs sockfs sysfs tmpfs ubifs udf usbfs fuse.glusterfs ceph fuse.ceph"
PRUNENAMES = ".git .hg .svn"
PRUNEPATHS = "/afs /media /mnt /net /sfs /tmp /udev /var/cache/ccache /var/lib/yum/yumdb /var/spool/cups /var/spool/squid /var/tmp /var/lib/ceph"
```

### updatedb
强制更新数据库,默认数据库一天更新1次
出现如下情况可执行更新数据库命令
>locate: can not stat () `/var/lib/mlocate/mlocate.db': No such file or directory

### whereis和which

**whereis**
`whereis`搜索系统命令的命令，只用于搜索系统命令，不能搜索文件

```
搜索命令所在路径及帮助文档所在位置
whereis 命令名
```

选项：
* `-b`只查找可执行文件
* `-m`只查找帮助文件

**which**
`which`的功能和`whereis`功能差不多

```
命令所在位置和命令别名
which 命令名
```

提示：
* 不是所有命令都有别名
* whereis可以搜索命令所在位置和帮助文档
* which可以搜索命令所在位置和命令别名

### find

搜索所有的资源比较耗时
```
搜索文件
find [搜索范围] [搜索条件]
```


1. 搜索文件`find 路径 -name install.log`
应避免大范围索索，会非常耗费系统资源`find`是在系统当中搜索符合条件的文件名，如果需要匹配，使用通配符匹配，通配符是完全匹配

* `*`匹配任意内容
* `?`匹配任意一个字符
* `[]`匹配任意一个中括号内的字符

2. 搜索文件不区分大小写`find 路径 -iname install.log`
3. 按照所有者搜索文件`find 路径 -iname -user root`
4. 查找没有所有者的文件`find 路径 -nouser`。`sys`、`proc`是内核产生存在没有所有者的文件，例如外来数据，移动硬盘，u盘没有所有者
5. 查找xx天内/前/的文件`find 路径 -mtime +10`

* `-10`10天内修改文件
* `10`10天当前修改的文件
* `+10`10天前修改的文件
* `atime`文件访问时间 `access`
* `ctime`改变文件属性 `change`
* `mtime`修改文件内容 `modify`

6. 查找固定大小的文件`find 路径 -size 25k`
* `-25k`小于25kb的文件
* `25k`等于25kb的文件
* `+25k`大于25kb的文件
* `k`小写
* `M`大写

7. 查找i节点是262422的文件`find 路径 -inum 262422`

8. 查找区间范围内文件`find 路径 -size +20k -a -size -50k`查找etc目录下，大于20kb并且小于50kb的文件
* `-a and`逻辑与，两个条件都满足
* `-o or`逻辑或，两个条件满足一个即可

9. 查找etc目录下 大于20kb并且小于50kb的文件，并显示详细信息`find 路径 -size +20k -a -size -50k  -exec ls -lh {} \;`
解释：
* 对于前面语句的结果交给-exec xxxx {} \;进行第二次处理
* 命令2处理命令1的搜索结果 ，对搜索结果执行操作


### grep
在文件当中匹配符合条件的字符串
```
grep [选项] 字符串 文件名
```

选项：
* `-i`忽略大小写`ignore`
* `-v`排除指定字符串`exclude`


### find和grep的区别
#### find命令
在系统中搜索符合条件的文件名，如果需要匹配，使用通配符匹配，通配符是完全匹配
#### grep命令
在文件当中搜索符合条件的字符串，如果需要匹配，使用正则表达式进行匹配，正则表达式是包含匹配



## 四、帮助命令
### man
man 命令manual
```
man ls
输入/-d，能进行搜索类似ctrl+f的功能，按键n显示下一个，案件shift+
```

```
查看命令有几种帮助级别
man -f 命令=whatis 命令
```
* 1 查看命令的帮助
* 2 查看可被内核调用的函数
* 3 查看函数和函数库的帮助
* 4 查看特殊文件的帮助 ，主要是/dev目录下的文件
* 5 查看配置文件的帮助
* 6 查看游戏的帮助
* 7 查看其他杂项的帮助
* 8 查看系统管理员可用命令的帮助
* 9 查看和内核相关文件的帮助

举例:
>man -5 passwd

>man -4 null

>man -8 ifconfig

**查看和命令相关的所有帮助**
```
man -k 命令=apropos 命令
```

其它帮助命令

1. `命令 --help`
2. `help 命令`
但是只能获取内部命令的帮助例如cd，可以用whereis来确定是否是内部命令，只要whereis找不到执行文件就是内部命令

3. `info 命令`
* `enter`进入子帮助页面
* `-u`进入上层页面
* `-n`进入下一个帮助小结
* `-p`进入上一个帮助小结
* `-q`退出



## 五、压缩与解压缩
常用压缩格式 `.zip`、`.gz`、`.bz2`、`.tar.gz`、`.tar.bz2`

### zip
压缩
```
压缩文件
zip 压缩文件名 源文件

压缩目录
zip -r 压缩文件名 源目录
```

解压缩
```
unzip 解压缩文件
```

### gzip
压缩
```
压缩为.gz格式的压缩文件，源文件会消失
gzip 源文件

压缩为.gz格式，源文件保留
gzip -c 源文件 > 压缩文件
例如：gzip -c cangls>cangls.gz

压缩目录下所有的子文件，但是不能压缩目录
gzip -r 目录
```

解压缩

```
解压缩文件，源压缩文件会消失
gzip -d 压缩文件


解压缩文件，源压缩文件会消失
gunzip 压缩文件
```

### bz2
压缩

```
bzip2 源文件
压缩为.bz2格式，不保留源文件

bzip2 -k 源文件
压缩之后保留源文件


```
注意：bzip2命令不能有压缩目录

解压缩
```
解压缩 bzip2 -k 解压缩文件，保留压缩文件
bzip2 -d 解压缩文件，不保留源文件

解压缩 -k 保留压缩文件，保留压缩文件
bunzip2 压缩文件，不保留源文件
```

### tar.gz
压缩
解压缩

### tar.bz2
压缩
解压缩



## 六、关机和重启命令
## 七、其它常用命令
