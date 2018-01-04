---
title: Mybatis返回结果集Map or 实体类
date: 2017-10-18 11:31:56
tags: Mybatis
categories: Mybatis
---

#### 观点1：
{% note primary %}
一般的配置项表的结构不太会变化，服务层还有业务操作，使用实体类好些，如果返回结果是多层组合，返回结构字段可能经常变化，多表联合查询，使用Map好些。
{% endnote %}
<!--more-->
#### 观点2：
{% note success %}
使用Map可读性较差，当你前端用Map接收传递参数和mybatis返回用Map接收和传递参数，如果回头阅读代码，还得去看前端请求或者具体sql返回结果。如果是他人接手你的项目，得从头到尾得读一遍你的代码。如果你返回的是一个对象实体，那他就可以看到你返回的是什么，别人也就不需要再去看你代码了，提高了开发效率。
{% endnote %}
#### 观点3：
{% note info %}
MyBatis也是ORM(Object Relational Mapping)框架的一员，使用Map从业界准则来看，不符合面向对象思想，这是一个代码规范问题。
{% endnote %}
#### 观点4：
{% note warning %}
采用实体类比Map更耗费系统资源，如下图所示：
{% endnote %}
<img src="/images/select.png">  