---
title: 悼念日大学官网样式，及我的第一篇博客
date: 2020-04-06 01:58:31
categories:
- [日常随笔]
- [前端]
tags:
- css
- 前端
- 心情
---

# 第一篇博客

​		这是第一篇博客，之前有搭建过WordPress，但是有段时间服务器被删库，还没做备份，后来腾讯云服务器到期没续费，索性放弃了这个想法。正好也没写几篇，乘着疫情期间空闲时间比较多，所以搭建了这个基于Hexo和Github Pages的静态博客，之前的学过的内容和遇见的问题，如果再次遇到，会慢慢的补充在博客上。

# 关于2020年4月4日哀悼日

​		没啥说的，清明期间悼念烈士和同胞。有网站要改成黑白灰色调的要求，因为我是大学的官网的制作人之一（当然是基于站群系统的，我只做了前端），所以应领导要求改样式。一开始我是想着单独写一个CSS，把图片都ps处理之后替换，还专门让老师把文章的插图也换了灰色，但后来想想太麻烦了，网上果然有现成的例子。

# filter（滤镜）

~~~css
html
{
    filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);
    filter: grayscale(100%);
    -webkit-filter: grayscale(100%);
    -moz-filter: grayscale(100%);
    -ms-filter: grayscale(100%);
    -o-filter: grayscale(100%);
    filter: progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);
    filter: gray;
    -webkit-filter: grayscale(1); 
}
~~~

​		原理就一个[filter](https://www.runoob.com/cssref/css3-pr-filter.html)，就是滤镜。这么多行也是为了兼容不同浏览器，grayscale(100%)是css3的新标准，用于定义网站的灰度，剩下的代码都是用于ie兼容，但只能兼容到ie6-9，这是一个挺麻烦的问题，ie10和11无法生效。

​		也有考虑过用grayscale.js处理，但是这个js速度太慢，学校官网数据挺多，一卡就是三分钟，完全不行，最后只能手写了一部分样式，让小伙伴处理了几个背景图，然后这天过了再回滚回之前彩色的样子。

​		ie10和11虽然比起ie6789有了很大提升，但是依旧不尽人意，毕竟是多年前的产物，但学校领导们用的人不少，给学校打个工总能遇到ie678的奇葩，国产浏览器也总喜欢用兼容模式，属实给人增添烦恼。

> 新事物之所以必然战胜旧事物，从根本上说是由于新事物较旧事物更符合事物发展的必然趋势。

