### 1. entity

```java
##导入宏定义
$!define
##保存文件（宏定义）
#save("/entity", ".java")
##包路径（宏定义）
#setPackageSuffix("entity")
##自动导入包（全局变量）
$!autoImport

import java.io.Serializable;

##表注释（宏定义）
/**
 * $!{tableInfo.comment}$!{tableInfo.name}表实体类
 * 
 * @author liam
 * @date $!time.currTime("yyyy/MM/dd HH:mm")
 */
@EqualsAndHashCode(callSuper = false)
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
@ApiModel(description = "")
public class $!{tableInfo.name} implements Serializable {

private static final long serialVersionUID = $!tool.serial();

#foreach($column in $tableInfo.fullColumn)
    #if(${column.comment})/**
    * ${column.comment}
    */#end
    #if(${column.comment})@ApiModelProperty(value = "${column.comment}")#end
    #if($column.name.equals('id'))@TableId(type = IdType.AUTO)#end
    private $!{tool.getClsNameByFullName($column.type)} $!{column.name};
    
#end
}
```



### 2. dao

```java
##定义初始变量
#set($tableName = $tool.append($tableInfo.name, "Mapper"))
##设置回调
$!callback.setFileName($tool.append($tableName, ".java"))
$!callback.setSavePath($tool.append($tableInfo.savePath, "/mapper"))

##拿到主键
#if(!$tableInfo.pkColumn.isEmpty())
    #set($pk = $tableInfo.pkColumn.get(0))
#end

#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}mapper;

import $!{tableInfo.savePackageName}.entity.$!{tableInfo.name};
import com.baomidou.mybatisplus.core.mapper.BaseMapper;

/**
 * $!{tableInfo.comment}$!{tableInfo.name}表数据库访问层
 *
 * @author liam
 * @date $!time.currTime("yyyy/MM/dd HH:mm")
 */
public interface $!{tableName} extends BaseMapper<$!{tableInfo.name}>{

}
```



### 3. service

```java
##定义初始变量
#set($tableName = $tool.append($tableInfo.name, "Service"))
##设置回调
$!callback.setFileName($tool.append($tableName, ".java"))
$!callback.setSavePath($tool.append($tableInfo.savePath, "/service"))

##拿到主键
#if(!$tableInfo.pkColumn.isEmpty())
    #set($pk = $tableInfo.pkColumn.get(0))
#end

#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}service;

import com.baomidou.mybatisplus.extension.service.IService;
import $!{tableInfo.savePackageName}.entity.$!{tableInfo.name};

/**
 * $!{tableInfo.comment}$!{tableInfo.name}表服务接口层
 *
 * @author liam
 * @date $!time.currTime("yyyy/MM/dd HH:mm")
 */
public interface $!{tableInfo.name}Service extends IService<$!{tableInfo.name}>{

}
```



### 4. serviceImpl

```java
##导入宏定义
$!define

##设置表后缀（宏定义）
#setTableSuffix("ServiceImpl")

##保存文件（宏定义）
#save("/service/impl", "ServiceImpl.java")

##包路径（宏定义）
#setPackageSuffix("service.impl")

import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import $!{tableInfo.savePackageName}.mapper.$!{tableInfo.name}Mapper;
import $!{tableInfo.savePackageName}.entity.$!{tableInfo.name};
import $!{tableInfo.savePackageName}.service.$!{tableInfo.name}Service;
import org.springframework.stereotype.Service;

##表注释（宏定义）
##tableComment("表服务实现类")
/**
 * $!{tableInfo.comment}$!{tableInfo.name}表服务实现类
 *
 * @author liam
 * @date $!time.currTime("yyyy/MM/dd HH:mm")
 */
@Service
public class $!{tableInfo.name}ServiceImpl extends ServiceImpl<$!{tableInfo.name}Mapper, $!{tableInfo.name}> implements $!{tableInfo.name}Service {

}
```



### 5. controller

```java
##导入宏定义
$!define
##设置表后缀（宏定义）
#setTableSuffix("Controller")
##保存文件（宏定义）
#save("/controller", "Controller.java")
##包路径（宏定义）
#setPackageSuffix("controller")
##定义服务名
#set($serviceName = $!tool.append($!tool.firstLowerCase($!tableInfo.name), "Service"))
##定义实体对象名
#set($entityName = $!tool.firstLowerCase($!tableInfo.name))
import $!{tableInfo.savePackageName}.entity.$!tableInfo.name;
import $!{tableInfo.savePackageName}.service.$!{tableInfo.name}Service;

import io.swagger.annotations.ApiOperation;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.baomidou.mybatisplus.extension.api.R;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.io.Serializable;
import java.util.List;

##表注释（宏定义）
/**
 * $!{tableInfo.comment}$!{tableInfo.name}表服务接口层
 *
 * @author liam
 * @date $!time.currTime("yyyy/MM/dd HH:mm")
 */
@RestController
public class $!{tableName} {
  
    /**
     * 服务对象
     */
    @Autowired
    $!{tableInfo.name}Service $!{serviceName};   
  
   /**
     * 分页查询所有数据
     *
     * @param page 分页对象
     * @param $!entityName 查询实体
     * @return 所有数据
     */
    @ApiOperation("分页查询所有数据")
    @GetMapping
    public R<IPage<$!tableInfo.name>>  selectAll(Page<$!tableInfo.name> page, $!tableInfo.name $!entityName) {
        return R.ok ($!{serviceName}.page(page, new QueryWrapper<>($!entityName)));
    }

    /**
     * 通过主键查询单条数据
     *
     * @param id 主键
     * @return 单条数据
     */
    @ApiOperation("通过主键查询单条数据")
    @GetMapping("{id}")
    public R<$!tableInfo.name> selectOne(@PathVariable Serializable id) {
        return R.ok($!{serviceName}.getById(id));
    }

    /**
     * 新增数据
     *
     * @param $!entityName 实体对象
     * @return 新增结果
     */
    @ApiOperation("新增数据")
    @PostMapping
    public R<Integer> insert(@RequestBody $!tableInfo.name $!entityName) {
        boolean rs = $!{serviceName}.save($!entityName);
        return R.ok(rs?$!{entityName}.getId():0);
    }

    /**
     * 修改数据
     *
     * @param $!entityName 实体对象
     * @return 修改结果
     */
    @ApiOperation("修改数据")
    @PutMapping
    public R<Boolean>  update(@RequestBody $!tableInfo.name $!entityName) {
        return R.ok($!{serviceName}.updateById($!entityName));
    }

    /**
     * 单条/批量删除数据
     *
     * @param idList 主键集合
     * @return 删除结果
     */
    @ApiOperation("单条/批量删除数据")
    @DeleteMapping
    public R<Boolean> delete(@RequestParam("idList") List<Long> idList) {
        return R.ok($!{serviceName}.removeByIds(idList));
    }
}
```



### 6. mapper.xml

```xml
##引入mybatis支持
$!mybatisSupport

##设置保存名称与保存位置
$!callback.setFileName($tool.append($!{tableInfo.name}, "Mapper.xml"))
$!callback.setSavePath($tool.append($modulePath, "/src/main/resources/mapper"))

##拿到主键
#if(!$tableInfo.pkColumn.isEmpty())
    #set($pk = $tableInfo.pkColumn.get(0))
#end

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="$!{tableInfo.savePackageName}.mapper.$!{tableInfo.name}Mapper">

    <resultMap type="$!{tableInfo.savePackageName}.entity.$!{tableInfo.name}" id="$!{tableInfo.name}Map">
#foreach($column in $tableInfo.fullColumn)
        <result property="$!column.name" column="$!column.obj.name" jdbcType="$!column.ext.jdbcType"/>
#end
    </resultMap>

</mapper>
```

