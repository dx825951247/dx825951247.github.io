---
title: Nginx不转发某些http header问题
date: 2017-12-27 15:51:00
tags: [Nginx]
categories: Nginx
---
当项目中使用自定义的请求头时，一定要注意请求头名称不能带有下划线，用Nginx做http代理时，像`token_id`这样的请求头，Nginx默认不转发，因为Nginx会默认过滤带有下划线的请求头。
解决办法：
1. 配置中http部分 增加`underscores_in_headers on;`配置。
2. 用减号`-`替代下划线符号`_`，避免这种变态问题。或者不使用含有下划线的请求头。

[nginx 做proxy 不转发 http header问题解决](http://blog.csdn.net/wx_mdq/article/details/10466891)