## 安装mysql

从最新版本的linux系统开始，默认的是 Mariadb而不是mysql！这里依旧以mysql为例进行展示

1、先检查系统是否装有mysql

```
rpm -qa | grep mysql
```

下载mysql的repo源

```
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
```

安装mysql-community-release-el7-5.noarch.rpm包

```
sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
```

安装mysql

```
sudo yum install mysql-server
```

如果报错，内容含有

```
Error: Package: mysql-community-libs-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: libc.so.6(GLIBC_2.17)(64bit)
Error: Package: mysql-community-server-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: libc.so.6(GLIBC_2.17)(64bit)
Error: Package: mysql-community-server-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: systemd
Error: Package: mysql-community-server-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: libstdc++.so.6(GLIBCXX_3.4.15)(64bit)
Error: Package: mysql-community-client-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: libc.so.6(GLIBC_2.17)(64bit)
 You could try using --skip-broken to work around the problem
 You could try running: rpm -Va --nofiles --nodigest
```

解决

```
yum install glibc.i686
yum list libstdc++*
```

重置密码前，首先要登录

```
mysql -u root
```

登录时有可能报这样的错：ERROR 2002 (HY000): Can’t connect to local MySQL server through socket ‘/var/lib/mysql/mysql.sock’ (2)，原因是/var/lib/mysql的访问权限问题。下面的命令把/var/lib/mysql的拥有者改为当前用户：

```
sudo chown -R openscanner:openscanner /var/lib/mysql
```

如果报`chown: 无效的用户: "openscanner:openscanner"`错误，更换命令，并用 ll 查看目录权限列表

```
chown root /var/lib/mysql/
ll
```

附：
① 更改文件拥有者 (chown )
[root@linux ~]# chown 账号名称 文件或目录
② 改变文件的用户组用命令 chgrp
[root@linux ~]# chgrp 组名 文件或目录
③ 对于目录权限修改之后，默认只是修改当前级别的权限。如果子目录也要递归需要加R参数
Chown -R : 进行递归,连同子目录下的所有文件、目录

然后，重启服务：

```
service mysqld restart
```

重启mysql服务后才生效 `# service mysqld restart`



## 开启远程连接

登录

```
use mysql;
```

创建一个远程用户

```
GRANT ALL PRIVILEGES ON *.* TO 'hourongtian'@'%' IDENTIFIED BY '901112rongtian' WITH GRANT OPTION;
```

刷新

```
flush privileges;
```

