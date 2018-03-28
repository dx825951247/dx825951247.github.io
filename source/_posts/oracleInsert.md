---
title: Oracle数据库批量插入、批量更新
date: 2017-08-08 21:24:17
tags: [Oracle,Mybatis]
categories: Oracle
top: 100
---
# 一.xml中批量插入三种方式
>## 1.使用union all

- sql中没有VALUES
- <foreach>中的select...插入数据...from dual
<!--more-->
``` xml
2017/10/18
INSERT INTO STUDENT
　(
　　NAME，
　　AGE
　)
<foreach collection="templateDetial" item="item" index="index" separator="union all" >
　SELECT 
　　#{item.name}, 
　　#{item.age}
　FROM DUAL	
</foreach>
```
******

>## 2.insert all

- sql中有`VALUES`
- 分隔符是空格,这种方式可以返回插入总条数
``` xml
INSERT ALL
　<foreach collection="list" item="item" index="index" separator=" ">
　　INTO STUDENT
　　　(
　　　　NAME，
　　　　AGE
　　　)
　　　　VALUES(
	#{item.name}, 
　　　　#{item.age}
　　　)
　</foreach>
SELECT 1 FROM DUAL
```

- 批量插入多表
``` xml
INSERT ALL
　<foreach collection="carouselList" item="item" index="index" separator=" ">
　　INTO STUDENT
　　　(
　　　　NAME，
　　　　AGE
　　　)
　　　　VALUES(
	#{item.name}, 
　　　　#{item.age}
　　　)
    INTO STUDENT2
　　　(
　　　　NAME2，
　　　　AGE2
　　　)
　　　　VALUES(
	#{item.name}, 
　　　　#{item.age}
　　　)
　</foreach>
SELECT 1 FROM DUAL
```
******

>## 3.begin end模式

- 分隔符是 ;插入成功，返回插入总条数始终未-1
``` xml
<foreach collection="list" item="item" index="index" open="begin" close=";end;" separator=";">
　INSERT INTO STUDENT
　　(
　　　NAME，
　　　AGE
　　)
　VALUES(
　　　#{item.name}, 
　　　#{item.age}
　　)
</foreach>
```
***
# 二.在代码中使用batch模式,效率最高
Mybatis有三种执行器：
- SIMPLE是普通的执行器，相当于JDBC的`stmt.execute(sql)`
- REUSE执行器会重用预处理语句(`prepared statements`)，相当于JDBC重用一条sql，再通过stmt传入多项参数值，然后执行`stmt.executeUpdate()`或`stmt.executeBatch()`
- BATCH执行器将重用语句并执行批量更新，相当于JDBC语句的`stmt.addBatch(sql)`，即仅仅是将执行SQL加入到批量计划。当插入主键时不会抛出主键冲突等运行时异常，而只有临近`commit`前执行`stmt.execteBatch()`后才会抛出异常。
``` java
public void test() {
　//新获取一个模式为BATCH，自动提交为false的session
　//如果自动提交设置为true,将无法控制提交的条数，改为最后统一提交，可能导致内存溢出
　SqlSession session = sqlSessionTemplate.getSqlSessionFactory().openSession(ExecutorType.BATCH, false);
　//通过新的session获取mapper
　testMapper = session.getMapper(testMapper.class);
　int size = 10000;
　try {
　　for (int i = 0; i < size; i++) {
　　　Test test = new Test();
　　　test.setName(String.valueOf(System.currentTimeMillis()));
　　　testMapper.insert(test);
　　　if (i % 1000 == 0 || i == size - 1) {
　　　　//手动每1000个一提交，提交后无法回滚
　　　　session.commit();
　　　　//清理缓存，防止溢出
　　　　session.clearCache();
　　　}
　　}
　} catch (Exception e) {
　　//没有提交的数据可以回滚
　　session.rollback();
　} finally {
　　session.close();
　}
}
```
***
# 三.批量更新
``` xml
<update id="updateStudent" parameterType="java.util.List">
　<foreach collection="list" item="item" index="index" open="begin" close=";end;" separator=";">
　　UPDATE 
　　　STUDENT
　　SET 
　　　AGE = '0'
　　WHERE
　　　NAME = #{item.name} 
　</foreach>
</update>
```
返回结果为 -1

参考链接
[MyBatis的几种批量操作](http://www.cnblogs.com/duanxz/p/3838352.html)
[关于Mybatis的Batch模式性能测试及结论](http://www.blogjava.net/diggbag/articles/mybatis.html)