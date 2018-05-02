---
title: Mybatis嵌套查询两种方式
date: 2017-08-17 17:14:31
tags: [Oracle, Mybatis, 通用]
categories: Mybatis
---

# resultMap

* `constructor` - 类在实例化时,用来注入结果到构造方法中
    + `dArg` - ID 参数;标记结果作为 ID 可以帮助提高整体效能
    + `arg` - 注入到构造方法的一个普通结果
<!--more-->
* `id` – 一个 ID 结果;标记结果作为 ID 可以帮助提高整体效能
* `result` – 注入到字段或 JavaBean 属性的普通结果
* `association` – 一个复杂的类型关联;许多结果将包成这种类型
	嵌入结果映射 – 结果映射自身的关联,或者参考一个
* `collection` – 复杂类型的集
　　+ `javaType` - 集合类型
　　+ `ofType` - 子对象的Java数据类型
　　+ `autoMapping` - 开启自动映射
	嵌套嵌入结果映射, 结果映射自身的集,或者参考一个
>`collection`标签中的`select`属性，通过这个属性，通过ID引用另一个加载复杂类型的映射语句。
从指定列属性中返回的值，将作为参数设置给目标`select` 语句。
注意：在处理组合键时，也就是传入多个参数时，可以使用`column=”{pro1=col1,pro2=col2}”`这样的语法，这就会把`prop1`和`prop2`设置到目标嵌套语句的参数对象中，在子查询中就可以作为参数使用`#{pro1}`,`#{pro2}`。

* `discriminator` – 使用结果值来决定使用哪个结果映射
    + `case` – 基于某些值的结果映射
    嵌入结果映射 – 这种情形结果也映射它本身,因此可以包含很多相 同的元素,或者它可以参照一个外部的结果映射。

# 第一种：子查询
``` xml
<select id="getTest" parameterType="Test" resultMap="getTestMap">
　select 
　　AAA, 
　　BBB, 
　　CCC
　from test 
　where 
　　'1' = '1'
　and 
　　AAA = #{aaa,jdbcType=VARCHAR}
</select>

<resultMap id="getTestMap" type="Test">
　<id column="AAA" property="aaa" jdbcType="VARCHAR" />
　<result column="BBB" property="bbb" jdbcType="VARCHAR" />
　<result column="CCC" property="ccc" jdbcType="VARCHAR" />
　<collection property="valueList" column="{aaa=AAA,bbb=BBB}" ofType="TestValue" javaType="ArrayList" select="selectTestValues"/>
</resultMap>

<select id="selectTestValues" resultMap="selectTestValuesMap">
　select 
　　DDD, 
　　EEE
　from test_value
　where 
　　AAA = #{aaa,jdbcType=VARCHAR}
　and 
　　BBB = #{bbb,jdbcType=VARCHAR}
</select>

<resultMap id="selectTestValuesMap" type="TestValue" >
　<result column="DDD" property="ddd" jdbcType="VARCHAR" />
　<result column="EEE" property="eee" jdbcType="VARCHAR" />
</resultMap>
```
# 第二种
多表连接把所有结果查询出来，resultMap开启开启自动映射或着在resultMap中填写需要合并的字段，这种方式由于是先查出所有结果，然后利用resultMap对结果合并，所以分页很难做。
``` xml
<resultMap id="getTestMap" type="Test">
　<id column="AAA" property="aaa" jdbcType="VARCHAR" />
　<result column="BBB" property="bbb" jdbcType="VARCHAR" />
　<result column="CCC" property="ccc" jdbcType="VARCHAR" />
　<collection property="valueList" ofType="TestValue" javaType="ArrayList" autoMapping="true"/>
</resultMap>
```
或者：
``` xml
<resultMap id="getTestMap" type="Test">
　<id column="AAA" property="aaa" jdbcType="VARCHAR" />
　<result column="BBB" property="bbb" jdbcType="VARCHAR" />
　<result column="CCC" property="ccc" jdbcType="VARCHAR" />
　<collection property="valueList" ofType="TestValue" javaType="ArrayList">
    <result column="DDD" property="ddd" jdbcType="VARCHAR" />
　  <result column="EEE" property="eee" jdbcType="VARCHAR" />
  </collection>	
</resultMap>
```