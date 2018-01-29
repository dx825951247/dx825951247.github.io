---
title: Mybatis数据类型值为0时非空判断问题
date: 2017-10-27 17:07:32
tags: Mybatis
categories: Mybatis
---
``` xml
<if test="age != null and age != ''">
　　#{age,jdbcType=NUMERIC}
</if>
```
<!--more-->
当age为0时，此判断不成立，sql可能报错。导致错误原因是，0被视为false，`0 != ''`自然也不会成立，数字类型进行非空字符判断本身也不合理。解决此问题办法，如下去掉非空判断：
``` xml
<if test="age != null">
　　#{age,jdbcType=NUMERIC}
</if>
```