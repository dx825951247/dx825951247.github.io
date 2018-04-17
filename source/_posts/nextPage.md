---
title: Next博客链接与常用Markdown语法
date: 2017-10-18 17:01:39
tags: [Next, Markdown]
---
[量子广告](http://blog.ynxiu.com/2016/hexo-next-theme-optimize.html)
[务虚笔记](http://www.wuxubj.cn/)
[小桥流水人家](https://ehlxr.me/)
[Jing’s Blog](http://www.iamlj.com/)
[Doublemine](https://notes.wanghao.work/)
[岁月如歌](http://lovenight.github.io/)
[胡闹的日子](http://hunao.info/)
[Never_yu’s Blog ](https://neveryu.github.io/)

[SEO优化博客](http://blog.csdn.net/sunshine940326/article/details/70936988)
<!--more-->

Bootstrap Callout 
{% note default %} Content (md partial supported) {% endnote %}
{% note primary %} Content (md partial supported) {% endnote %}
{% note success %} Content (md partial supported) {% endnote %}
{% note info %} Content (md partial supported) {% endnote %}
{% note warning %} Content (md partial supported) {% endnote %}
{% note danger %} Content (md partial supported) {% endnote %}

将博客源文件上传至好hexo分支
deploy:
  - type: git
    repo: https://github.com/dx825951247/dx825951247.github.io.git
    branch: master
  - type: git
    repo: https://github.com/dx825951247/dx825951247.github.io.git
    branch: hexo
    extend_dirs: /
    ignore_hidden: false
    ignore_pattern:
        public: .
  - type: baidu_url_submitter

Next标签插件
Code Block
{% codeblock %}
alert('Hello World!');
{% endcodeblock %}

Pull Quote
{% pullquote [class] %}
content
{% endpullquote %}
