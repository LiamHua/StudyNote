## 一、安装

1. 使用docker拉取镜像 `jenkins/jenkins`	,不要使用`jenkinsci/blueocean`镜像，该镜像存在依赖问题。

拉取时可选择版本 lts长期支持版或latest最新版，默认为latest

```shell
sudo docker pull jenkins/jenkins
```



2. 运行该镜像

```shell
# 以下指令详细解释请参考官网
sudo docker run \
  -u root \
  --rm \
  -d \
  -p 8080:8080 \ # 这里第一个端口为外部访问端口，按实际修改，注意在服务器安全组内开放
  -p 50000:50000 \
  -v jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkins/jenkins
```



3. 访问，在浏览器中输入 ip:port 或者 域名（使用nginx反向代理到该端口），第一次访问要求输入密码，密码位置在容器内 /var/jenkins_home/secrets/initialAdminPassword

```shell
# 进入容器
sudo docker exec -it 容器id bash

# 查询密码
cat /var/jenkins_home/secrets/initialAdminPassword
```

![image-20200714104059734](https://pictures.huazai.fun/uPic/image-20200714104059734.png)



4. 将查询的密码输入，进入下一步，点击 选择插件来安装，等待几秒后进入下一步

![image-20200714104303003](https://pictures.huazai.fun/uPic/image-20200714104303003.png)



5. 点击 “无” 取消所有选择的项目，翻到最底下，选中中文插件，点击 安装

![image-20200714104518491](https://pictures.huazai.fun/uPic/image-20200714104518491.png)



6. 创建用户，输入用户名和密码，基本安装结束
7. 在访问地址后面追加 restart 来重启Jenkins（中文插件需要重启才能显示完全）

8. 修改镜像源，点击 系统管理 --> 插件管理 --> 高级，点击左下角 Jenkins 中文社区，复制地址，点击使用，再点击设置地址，进入原页面将升级站点中URL修改为刚刚复制的地址

![image-20200714105407997](https://pictures.huazai.fun/uPic/image-20200714105407997.png)

![image-20200714110017414](https://pictures.huazai.fun/uPic/image-20200714110017414.png)

![image-20200714110213223](https://pictures.huazai.fun/uPic/image-20200714110213223.png)



9. 修改镜像源可能出现的问题

   + 点击获取后出现一大堆红色错误说明没有安装证书
   + 点击获取后出现一行错误，从提示中可以看出来是文件未找到，进入该仓库中发现没有对应版本的仓库，这个问题一般会出现在安装latest最新版时，可以将URL地址改为该仓库中的通用仓库地址，https://cdn.jsdelivr.net/gh/jenkins-zh/update-center-mirror@master/tsinghua/update-center.json

   ![image-20200714110625305](https://pictures.huazai.fun/uPic/image-20200714110625305.png)

![image-20200714110948846](https://pictures.huazai.fun/uPic/image-20200714110948846.png)



10. 自由的安装需要的插件吧。。。一般有git  ,  maven,  nodejs,  blueocean等

