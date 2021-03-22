## WebLogic安装教程

### 一、介绍

### 二、安装

1. ##### 使用 uxun 用户登陆服务器

   

2. ##### 在 uxun 用户目录下创建 app目录，app目录是各个中间件的运行目录；在app目录下创建 utils 目录用于存储所需要用到中间件安装包，如果已经存在该文件夹则不需要再创建

```shell
[uxun@localhost ~]$ pwd
/home/uxun
[uxun@localhost ~]$ mkdir app app/utils
```



3. ##### 将 weblogic 安装包 wls1211_generic.jar 上传到 utils 目录中



4. ##### 安装前请确保服务器上有jdk1.7 及以上环境

```shell
[uxun@localhost utils]$ java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
```



5. 在 app 目录下新建 weblogic 目录

```shell
mkdir weblogic
```



6. 开始安装 weblogic，在 utils 目录下执行以下命令

```shell
[uxun@localhost utils]$ java -jar wls1211_generic.jar

./configure --prefix=/home/uxun/app/nginx --with-pcre=/home/uxun/app/utils/pcre-8.40 --with-openssl=/home/uxun/app/utils/openssl-1.0.2p --with-stream --with-zlib=/home/uxun/app/utils/zlib-1.2.11 --with-http_ssl_module
```



7. 开始正式安装

+ 点击 next

![image-20201128184214575](https://pictures.huazai.vip/uPic/image-20201128184214575.png)

+ 将目录修改为刚刚创建的 weblogic 目录

![image-20201128184349482](https://pictures.huazai.vip/uPic/image-20201128184349482.png)

+ 取消勾选

![image-20201128184818954](https://pictures.huazai.vip/uPic/image-20201128184818954.png)

+ 典型安装

![image-20201128184913163](https://pictures.huazai.vip/uPic/image-20201128184913163.png)

+ 确定 jdk 版本，点击 next

  ![image-20201128185005081](https://pictures.huazai.vip/uPic/image-20201128185005081.png)

+ 确定安装目录，点击 next

  ![image-20201128185044275](https://pictures.huazai.vip/uPic/image-20201128185044275.png)

+ 确定所要安装的组件，点击 next

![image-20201128185202718](https://pictures.huazai.vip/uPic/image-20201128185202718.png)

+ 正在安装

  ![image-20201128185244314](https://pictures.huazai.vip/uPic/image-20201128185244314.png)

+ 安装完成，点击 done 结束安装

  ![image-20201128185606430](https://pictures.huazai.vip/uPic/image-20201128185606430.png)



### 三、创建 domain 域

1. 执行 weblogic目录 bin 中的 config.sh 脚本

   ```shell
   [uxun@localhost bin]$ pwd
   /home/uxun/app/weblogic/wlserver_12.1/common/bin
   [uxun@localhost bin]$ ./config.sh
   ```

   

   
