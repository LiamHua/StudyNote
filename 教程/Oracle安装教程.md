## Oracle 安装教程

### 一、介绍

### 二、安装

1. ##### 创建运行oracle数据库的系统用户和用户组

```shell
[root@localhost ~]# groupadd oinstall     # 创建oinstall用户组
[root@localhost ~]# groupadd dba     # 创建dba用户组
[root@localhost ~]# useradd -g oinstall -g dba -m oracle  # 创建oracle用户并加入到oinstall和dba用户组
[root@localhost ~]# passwd oracle     # 给oracle用户设置密码
Changing password for user oracle.
New password: 
BAD PASSWORD: it is too short
BAD PASSWORD: is a palindrome     # 密码过于简单，不用理会
Retype new password:      # 重复密码
passwd: all authentication tokens updated successfully.
[root@localhost ~]# id oracle     # 查看新建的用户
uid=501(oracle) gid=502(dba) groups=502(dba)
```



2. ##### 修改系统核心参数

+ 修改用户的SHELL的限制，修改/etc/security/limits.conf文件，将下列内容加入该文件

```shell
oracle soft nproc 2047
 
oracle hard nproc 16384
 
oracle soft nofile 1024
 
oracle hard nofile 65536
```



+ 修改/etc/pam.d/login 文件，将下列内容加入该文件

```shell
session required /lib/security/pam_limits.so
session required pam_limits.so
```



+ 修改linux内核，修改/etc/sysctl.conf文件，将下列内容加入该文件（将文件中已存在的重复配置注释掉）

```shell
fs.file-max = 6815744
 
fs.aio-max-nr = 1048576
 
kernel.shmall = 2097152
 
kernel.shmmax = 2147483648
 
kernel.shmmni = 4096
 
kernel.sem = 250 32000 100 128
 
net.ipv4.ip_local_port_range = 9000 65500
 
net.core.rmem_default = 4194304
 
net.core.rmem_max = 4194304
 
net.core.wmem_default = 262144
 
net.core.wmem_max = 1048576
```



+ ==要使 /etc/sysctl.conf 更改立即生效，执行以下命令==

```shell
sysctl -p
```



+ 编辑 /etc/profile，将下列内容加入该文件

```shell
if [ $USER = "oracle" ]; then
 
if [ $SHELL = "/bin/ksh" ]; then
 
ulimit -p 16384
 
ulimit -n 65536
 
else
 
ulimit -u 16384 -n 65536
 
fi
 
fi
```



+ 创建数据库软件目录和数据文件存放目录

```shell
mkdir /home/oracle/app
 
mkdir /home/oracle/app/oracle
 
mkdir /home/oracle/app/oradata
 
mkdir /home/oracle/app/oracle/product
```



+ 更改目录属主为Oracle用户所有，输入

```shell
chown -R oracle:oinstall /home/oracle/app
```



+ 配置oracle用户的环境变量，首先切换到oracle 用户，然后修改 .bash_profile 文件，增加以下内容

```shell
export ORACLE_BASE=/home/oracle/app
 
export ORACLE_HOME=$ORACLE_BASE/oracle/product/11.2.0/dbhome_1
 
export ORACLE_SID=orcl
 
export PATH=$PATH:$HOME/bin:$ORACLE_HOME/bin
 
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/usr/lib
```



+ 关闭防火墙

```shell
# Red Hat（CentOS） 6
chkconfig iptables off     # 永久关闭
service iptables stop      # 临时关闭，重启失效

# Red Hat（CentOS） 7
systemctl stop firewalld.service      # 关闭防火墙
systemctl disable firewalld.service   # 禁止防火墙开机自启动
```



+ ==关闭selinux，修改/etc/selinux/config 文件，修改为  SELINUX=disabled ，然后重启 (**重启后生效**)==



3. ##### 解决依赖问题

```shell
# 需要如下依赖包
binutils-2.17.50.0.6
compat-libstdc++-33-3.2.3
elfutils-libelf-0.125
elfutils-libelf-devel-0.125
elfutils-libelf-devel-static-0.125
gcc-4.1.2
gcc-c++-4.1.2
glibc-2.5-24
glibc-common-2.5
glibc-devel-2.5
glibc-headers-2.5
kernel-headers-2.6.18
ksh-20060214
libaio-0.3.106
libaio-devel-0.3.106
libgcc-4.1.2
libgomp-4.1.2
libstdc++-4.1.2
libstdc++-devel-4.1.2
make-3.81
sysstat-7.0.2
unixODBC-2.2.11
unixODBC-devel-2.2.11

# 安装依赖包
yum -y install binutils compat-libstdc++ elfutils-libelf elfutils-libelf-devel elfutils-libelf-devel-static gcc gcc-c++ glibc glibc-common glibc-devel glibc-headers kernel-headers ksh libaio libaio-devel libgcc libgomp libstdc++ libstdc++-devel make sysstat unixODBC unixODBC-devel

# 检查依赖包
rpm -q binutils compat-libstdc++ elfutils-libelf elfutils-libelf-devel elfutils-libelf-devel-static gcc gcc-c++ glibc glibc-common glibc-devel glibc-headers kernel-headers ksh libaio libaio-devel libgcc libgomp libstdc++ libstdc++-devel make sysstat unixODBC unixODBC-devel
```

> 安装依赖中若compat-libstdc++-33-3.2.3不存在，该依赖为 Oracle Text 所需，自行选择下载安装



4. ##### 设置将安装图形界面在客户端显示（服务端不需要安装桌面）

+ 设置服务端环境变量，登陆 oracle 用户，执行以下命令

```shell
export DISPLAY=192.168.12.134:0.0     # ：0.0 前的ip地址为客户端ip
```



+ 启动 Xmanager，新建Xstart 连接，填写基本参数后执行命令选项选择第三个

![image-20201203171416429](https://pictures.huazai.fun/uPic/image-20201203171416429.png)

+ 点击确定保存后双击来启动刚刚创建的Xstart连接
+ 执行database目录中的 runInstaller 来开始安装

> 注意 runInstall 及关联文件是否具有 x 权限，若没有则会报权限不足的错误，加上 x 权限即可，chmod u+x file

