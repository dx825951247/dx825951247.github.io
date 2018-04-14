---
title: 两种方式生成树形菜单
date: 2018-01-04 16:40:07
tags: [树形菜单]
categories: 递归算法
---
## 一、Java递归算法生成树形菜单   
<!--more-->
```java
//合并前菜单
List<TMenu> rootMenuList = xxxMapper.selectByxxx(params);
List<TMenu> menuList = new ArrayList<TMenu>();
if (null != rootMenuList && rootMenuList.size() > 0) {
　　// 一级菜单
　　for (TMenu menu : rootMenuList) {
　　　//顶级菜单父级ID为0
　　　if (menu.getParAuthId() == 0) {
　　　　menuList.add(menu);
　　　}
　　}
　　//递归查询顶级菜单子菜单
　　if (null != menuList && menuList.size() > 0) {
　　　　for (TMenu menu : menuList) {
　　　　　　menu.setList(getChildMenuList(menu.getAuthId(),rootMenuList));
　　　　}
　　}
}

/**
* 查询子菜单
* @param authId
* @param rootMenuList
* @return
*/
private List<TMenu> getChildMenuList(Integer authId, List<TMenu> rootMenuList) {
　　List<TMenu> list = new ArrayList<TMenu>();
　　　　for (TMenu menu : rootMenuList) {
　　　　　　if (menu.getParAuthId() == authId) {
　　　　　　　　list.add(menu);
　　　　　　}
　　　　}
　　if (null != list && list.size() > 0) {
　　　　for (TMenu menu : list) {
　　　　　　menu.setList(getChildMenuList(menu.getAuthId(), rootMenuList));
　　　　}
　　}
　　if (null != list && list.size() == 0) {
　　　　return null;
　　}
　　return list;
}
```

## 二、MyBatis collection 集合
``` xml
　　<resultMap id="NextTreeMap" type="Menu">
　　　　<result column="id" property="id"/>
　　　　<result column="name" property="name"/>
　　　　<collection column="id" property="next" javaType="java.util.ArrayList"
　　　　　　ofType="Menu" select="getNextNodeTree"/>
　　</resultMap>

　　<select id="getNextNodeTree" resultMap="NextTreeMap">
　　　　SELECT
　　　　　id,
　　　　　name
　　　　FROM node
　　　　WHERE par_auth_id = #{id}
</select>
```
　　这种方式当数据量大的时候，会产生大量的SELECT语句，效率低下，因此不推荐数据量大的树形菜单。