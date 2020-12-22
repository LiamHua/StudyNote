### 1. mysql

```shell
sudo docker run -p 3306:3306 --name mysql \
-v ~/environment/mysql/data:/var/lib/mysql \
-v ~/environment/mysql/conf:/etc/mysql \
-v ~/environment/mysql-files:/var/lib/mysql-files \
-e MYSQL_ROOT_PASSWORD=321$098 \
-d mysql
```



### 2. redis

```shell
sudo docker run -p 6379:6379 --name redis \
-v ~/environment/redis/conf/redis.conf:/etc/redis/redis.conf \
-v ~/environment/redis/data:/data \
-d redis redis-server /etc/redis/redis.conf
```



### 3. nacos

```shell
sudo docker run -d \
-e MODE=standalone \
-e SPRING_DATASOURCE_PLATFORM=mysql \
-e MYSQL_SERVICE_HOST=127.0.0.1 \
-e MYSQL_SERVICE_PORT=3306 \
-e MYSQL_SERVICE_USER=liam \
-e MYSQL_SERVICE_PASSWORD=321$098 \
-e MYSQL_SERVICE_DB_NAME=pgnc_nacos_config \
-p 8848:8848 \
--restart=always \
--name mynacos \
nacos/nacos-server


```

