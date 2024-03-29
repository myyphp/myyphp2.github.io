---
layout: post
title: linux 环境下 php 扩展安装通用方法
tags:  Linux、PHP
categories: php
---

#linux 环境下 php 扩展安装通用方法
(本案例拿 php的mbstring 来做演示)
1. 首先进入到php源码包中的 ext 目录下 
    
    > /usr/local/src/php-5.3.28/ext/mbstring

2. 在mbstring 目录下(/usr/local/src/php-5.3.28/ext/mbstring 目录下)产生configure文件，执行如下命令

    > /usr/local/php/bin/phpize

3. 配置编译(/usr/local/src/php-5.3.28/ext/mbstring 目录下) 

    > ./configure --with-php-config=/usr/local/php/bin/php-config 

    > make && make install 

4. 在执行上面命令后会生成一个目录DIR(/usr/local/php/lib/php/extensions/no-debug-non-zts-20090626)，里面包含 mbstring.so 文件

5. 配置 php.ini 文件中的extension_dir=DIR ，然后加入一行  extension=mbstring.so

    > extension_dir="/usr/local/php/lib/php/extensions/no-debug-non-zts-20090626"

    > extension=mbstring.so

6. 使用 phpinfo() 函数测试 

注意：安装php的mcrypt时候出现问题？
    
问题描述：在php对mcrypt的扩展时候，出现mcrypt.h not found. Please reinstall libmcrypt

解决方案：
- 使用第三方的yum源来解决
    
> wget http://www.atomicorp.com/installers/atomic

>  sh ./atomic  

>  yum  install  php-mcrypt  libmcrypt  libmcrypt-devel

- 使用php mcrypt 前必须先安装libmcrypt

libmcrypt源码安装方法：

> cd /usr/local/src

> wget http://softlayer.dl.sourceforge.net/sourceforge/mcrypt/libmcrypt-2.5.8.tar.gz

> tar -zxvf libmcrypt-2.5.8.tar.gz

> cd /usr/local/src/libmcrypt-2.5.8

> ./configure --prefix=/usr/local

> make

> make install