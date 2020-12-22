### 一、用户查询

+ 查询当前用户

```shell
show user;
select user from dual;
```



+ 查询所有用户

```shell
select * from all_users;
```



### 二、表查询

+ 表数量查询

```shell
select count(1) from tabs;
select count(1) from user_tables;
```





### 三、数据库导入与导出

1. 原生指令

+ 导入

```shell
imp username/password file=导入文件路径 full=yes
```

+ 导出

```shell
exp username/password file=导出路径
```

