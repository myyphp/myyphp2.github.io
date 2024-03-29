---
layout: post
title: Linux环境下redis的搭建
tags:  linux/redis
categories: Linux
---

#本文主要介绍Linux环境下redis的搭建


[redis 源码包](http://yunpan.cn/cHqjyAVnp8hps)  访问密码 e97d

- 操作系统环境


> [root@asion ~]#   lsb_release  -a
LSB-Version:core-4.0-ia32:core-4.0-noarch:graphics-4.0-ia32:gr  aphics-4.0-noarch:printing-4.0-ia32:printing-4.0-noarch
Distributor ID:  CentOS
Description:    CentOS release 5.5 (Final)
Release:        5.
Codename:      Final


- 编译环境准备
> yum -y install make apr* autoconf automake curl-devel gcc gcc-c++ zlib-devel openssl openssl-devel pcre-devel gd kernel keyutils patch perl kernel-headers compat* mpfr cpp glibc libgomp libstdc++-devel ppl cloog-ppl keyutils-libs-devel libcom_err-devel libsepol-devel libselinux-devel krb5-devel zlib-devel libXpm* freetype libjpeg* libpng* php-common php-gd ncurses* libtool* libxml2 libxml2-devel patch cmake

- 安装TCL，不然 redis 的 make test 不会通过。也可以先安装 redis 再回过头来装 TCL (这步可忽略)
[下载地址](http://sourceforge.net/projects/tcl/files/Tcl/) 
编译安装：
> tar xvzf tcl8.6.1-src.tar.gz
> cd tcl8.6.1/unix/
> ./configure
> make
> make test
> make install
    
- [下载redis源码](http://redis.io/)
> wget  http://download.redis.io/releases/redis-3.0.4.tar.gz
    
5. 解压下载的文件(无须configure，在make install 的时候指定安装地址，PREFIX 需要大写)
> tar -zxvf redis-3.0.4.tar.gz
    
- 编译安装
> cd redis-3.0.4
> make
> make PREFIX=/usr/local/redis install
> cp redis.conf /usr/local/redis/redis.conf 

- 注意：
如果在执行 make 的时候遇到以下错误(比如本人的 32 位的 CentOS5.5 就碰到了)：

> zmalloc.o: In function zmalloc_used_memory:
/home/defonds/redis/redis-2.8.10/src/zmalloc.c:223: undefined reference to `__sync_add_and_fetch_4'
collect2: ld returned 1 exit status
make[1]: *** [redis-server] Error 1
make[1]: Leaving directory /home/defonds/redis/redis-2.8.10/src
make: *** [all] Error 2

解决方案：在执行 make 时加上参数 CFLAGS="-march=i686" 就可以了：

> make CFLAGS="-march=i686"

- 修改redis.conf 配置文件（daemonize yes  后台启动）
> vim redis.conf
> daemonize yes 
> ./bin/redis-server ./redis.conf

> ps aux | grep redis
> ./bin/redis-cli INFO

- 可以对redis 进行检测
> cd /usr/local/redis/bin
> ./redis-benchmark -t GET,SET

当然，也可以添加 -q 对输出结果进行精简

> ./redis-benchmark -q -t GET,SET

- 编译安装完成

- 安装PHP对redis的扩展 （Cannot find config.m4. 解决方案：进入扩展目录）
    下载地址：http://pecl.php.net/package/redis

- 解压文件
    > cd phpredis-2.2.4
    > /usr/local/php/bin/phpize
    > ./configure --with-php-config=/usr/local/php/bin/php-config
    > make
    > make install

- 将生成的 memcache.so 动态库的目录添加到php.ini文件里面
    > vim /usr/local/php/etc/php.ini
    > extension_dir = "/usr/local/php/lib/php/extensions/no-debug-non-zts-20090626"
    > extension=redis.so

- 到网站根目录添加测试文件 redis.php
> cd /var/www/html
> vim redis.php

	{% highlight php startinline linenos %} 
    <?php
        $redis = new Redis;
        $redis->connect('localhost',6379);
        $redis->set('test', 'hello world');
        echo $redis->get('test');
    ?>
	{% endhighlight %}
- 在浏览器的地址栏输入 http://localhost/memcache.php输入上述地址后 浏览器页面 输出 hello world ，则成功