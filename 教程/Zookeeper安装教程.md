## Zookeeper安装教程

### 一、介绍



### 二、安装

1. ##### 使用 uxun 用户登陆服务器

   

2. ##### 在 uxun 用户目录下创建 app目录，app目录是各个中间件的运行目录；在app目录下创建 utils 目录用于存储所需要用到中间件安装包，如果已经存在该文件夹则不需要再创建

```shell
[uxun@localhost ~]$ pwd
/home/uxun
[uxun@localhost ~]$ mkdir app app/utils
```



3. ##### 将 zookeeper 安装包 zookeeper-3.4.10.tar.gz 上传到 utils 目录中并解压到app目录中

```shell
[uxun@localhost utils]$ tar -xf zookeeper-3.4.10.tar.gz -C ..
```



4. ##### 安装前请确保服务器上有jdk1.7 及以上环境

```shell
[uxun@localhost utils]$ java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
```



5. ##### 进入 zookeeper 的conf 目录，将 zoo_sample.cfg 配置文件复制一份并命名为 zoo.cfg，这将会是 zookeeper 的默认配置文件 

```shell
cd zookeeper-3.4.10/conf
cp zoo_sample.cfg zoo.cfg
```



6. ##### 在 zookeeper 目录下新建 data 目录，这将会是 zookeeper 的数据存放目录

![image-20201127151558295](https://pictures.huazai.fun/uPic/image-20201127151558295.png)



7. ##### 修改配置文件，编辑刚才的 zoo.cfg 配置文件，将 dataDir 的值修改为 data 目录地址

![image-20201127151919665](https://pictures.huazai.fun/uPic/image-20201127151919665.png)



8. ##### Zookeeper 启动与停止操作，进入 zookeeper 的 bin 目录，执行以下命令

```shell
./zkServer.sh start      // 启动zookeeper
./zkServer.sh restart    // 重启zookeeper
./zkServer.sh stop       // 停止zookeeper
./zkServer.sh status     // 查看zookeeper状态
```

