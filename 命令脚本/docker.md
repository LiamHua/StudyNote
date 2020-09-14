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

