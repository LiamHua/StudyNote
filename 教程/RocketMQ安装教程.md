## RocketMQ安装教程

### 一、介绍



### 二、安装

1. ##### 使用 uxun 用户登陆服务器

   

2. ##### 在 uxun 用户目录下创建 app目录，app目录是各个中间件的运行目录；在app目录下创建 utils 目录用于存储所需要用到中间件安装包，如果已经存在该文件夹则不需要再创建

```shell
[uxun@localhost ~]$ pwd
/home/uxun
[uxun@localhost ~]$ mkdir app app/utils
```



3. ##### 将 rocketmq 安装包 alibaba-rocketmq-3.2.6.tar_102.gz 上传到 utils 目录中并解压到app目录中

```shell
[uxun@localhost utils]$ tar -xf alibaba-rocketmq-3.2.6.tar_102.gz -C ..
```



4. ##### 安装前请确保服务器上有jdk1.7 及以上环境

```shell
[uxun@localhost utils]$ java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
```



5. ##### 进入 alibaba-rocketmq 目录中，并依次创建以下目录作为RocketMQ的master和slave数据和日志存放的文件夹

```shell
cd alibaba-rocketmq
# 创建以下目录
mkdir logs 
mkdir -p data/store/commitlog
mkdir -p data/store/consumerqueue
mkdir -p data/store/checkpoint
mkdir -p data/store/abort
mkdir -p data/store/index
mkdir -p data/store/commitlog2
mkdir -p data/store/consumerqueue2
mkdir -p data/store/checkpoint2
mkdir -p data/store/abort2
mkdir -p data/store/index2
```



6. ##### 如果 RocketMQ 安装在测试服务器上，那么我们还需要修改配置文件中关于JVM的参数，因为一般情况下测试服务器性能小于生成服务器，如果不修改的话，那么就会影响服务器的性能，甚至是RocketMQ的broker都启动失败。修改步骤如下：

   + ##### 进入 alibaba-rocketmq 的 bin 目录，修改 runbroker.sh 文件来修改broker启动时的JVM内存

   ```shell
   [uxun@localhost app]$ cd alibaba-rocketmq/bin/
   [uxun@localhost bin]$ vim runbroker.sh
   ```

   ![image-20201127170508174](https://pictures.huazai.fun/uPic/image-20201127170508174.png)

   

   + ##### 修改 runserver.sh 文件来修改nameserver启动时的JVM内存

   ```shell
   [uxun@localhost bin]$ vim runserver.sh
   ```

   ![image-20201127174405543](https://pictures.huazai.fun/uPic/image-20201127174405543.png)

   

   + ##### 修改 tools.sh 文件来修改tools启动时的JVM内存

   ```shell
   [uxun@localhost bin]$ vim tools.sh
   ```

   ![image-20201127175312652](https://pictures.huazai.fun/uPic/image-20201127175312652.png)







```shell
./configure --prefix=/app/nginx --with-pcre=/app/source/pcre-8.40 --with-openssl=/app/source/openssl-1.0.2p --with-stream --with-zlib=/app/source/zlib-1.2.11 --with-http_ssl_module
```

