---
layout: post
title: "MAC下解决PHPSTORM的Case-Sensitivity"
date: 2015-11-16 17:35:38 +0800
comments: true
categories: PHP
---

由于最近重装了MAC的系统,在安装时选择了区分大小写的存储格式,在打开idea 这类的IDE的时候会出现警告.这里提供一个解决的方法：

以PHPSOTRM为例：

 * Finder 中打开应用程序,找到PHPSTORM，右击显示包内容，找到
 `Contents/bin/idea.properties`文件，用编辑器打开；
 * 在最后加上`idea.case.sensitive.fs=true`,然后保存。
 重启PHPSTORM，警告就消失了。



