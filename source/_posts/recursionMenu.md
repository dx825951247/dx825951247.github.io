---
title: Java递归算法生成树形菜单
date: 2018-01-04 16:40:07
tags: [树形菜单]
categories: 递归算法
---
## Java递归算法生成树形菜单   
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