### 1. 换源并删除桌面

```shell
#!/bin/sh
sudo rpm -qa | grep yum | xargs sudo rpm -e --nodeps
sudo rpm -e python-urlgrabber-3.9.1-8.el6.noarch
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-urlgrabber-3.9.1-11.el6.noarch.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-3.2.29-81.el6.centos.noarch.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-iniparse-0.3.1-2.1.el6.noarch.rpm
sudo rpm -ivh python-urlgrabber-3.9.1-11.el6.noarch.rpm
sudo rpm -ivh python-iniparse-0.3.1-2.1.el6.noarch.rpm
sudo rpm -ivh yum-metadata-parser-1.1.2-16.el6.x86_64.rpm yum-3.2.29-81.el6.centos.noarch.rpm yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm
wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
sudo mv CentOS6-Base-163.repo /etc/yum.repos.d
sudo sed -i 's/\$releasever/6/g' /etc/yum.repos.d/CentOS6-Base-163.repo
yum clean all
sudo yum -y groupremove "X Window System" "Desktop"
sudo sed -i '26s/5/3/' /etc/inittab

find . -name '*.rpm' | xargs rm -rf
```



### 2. 添加管理员权限

```shell
sed -i  "98a\uxun  ALL=\(ALL\)  ALL" /etc/sudoers
```





### 3. 	root测试

```shell
#!/bin/sh
rpm -qa | grep yum | xargs rpm -e --nodeps
rpm -e python-urlgrabber-3.9.1-8.el6.noarch
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-urlgrabber-3.9.1-11.el6.noarch.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-3.2.29-81.el6.centos.noarch.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-iniparse-0.3.1-2.1.el6.noarch.rpm
rpm -ivh python-urlgrabber-3.9.1-11.el6.noarch.rpm
rpm -ivh python-iniparse-0.3.1-2.1.el6.noarch.rpm
rpm -ivh yum-metadata-parser-1.1.2-16.el6.x86_64.rpm yum-3.2.29-81.el6.centos.noarch.rpm yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm
wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
mv CentOS6-Base-163.repo /etc/yum.repos.d
sed -i 's/\$releasever/6/g' /etc/yum.repos.d/CentOS6-Base-163.repo
yum clean all

sudo yum -y groupremove "X Window System" "Desktop"
sudo sed -i '26s/5/3/' /etc/inittab

find . -name '*.rpm' | xargs rm -rf


```

