---
layout: post
title: "在ubuntu上安装PHP7.0"
date: 2015-12-16 21:45:01 +0800
comments: true
categories: PHP
---

PHP7.0 发布啦! 等了几天,今天来尝尝鲜,哈哈!

这玩意据说比HHVM还快了啊.下面我们就来装个体验下.


卸载PHP5

`sudo apt-get remove php5* && apt-get purge php5-fpm && apt-get --purge autoremove && `

添加 PHP7的PPA源

`apt-get install python-software-properties software-properties-common`
`sudo add-apt-repository ppa:ondrej/php-7.0`

安装
`apt-get update && apt-get install php7.0-fpm php7.0-mysql`

如果你安装的是Apache 这样

`apt-get install libapache2-mod-php7.0`
`a2enmod php7.0`
`service apache2 restart`

好了,大工告成.当然,还可以做一些优化:
[鸟叔大神的帖子](http://www.laruence.com/2015/12/04/3086.html "让PHP7达到最高性能的几个Tips")  让PHP7达到最高性能的几个Tips.






