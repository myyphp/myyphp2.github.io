---
layout: post
title: lamp环境的搭建
tags:  Lamp
categories: Linux
---

#lamp环境的搭建

lamp源码包：[http://yunpan.cn/cHqsvIUPRgEgN](http://yunpan.cn/cHqsvIUPRgEgN)  访问密码 ca46 <br/>
linux操作系统：[http://yunpan.cn/cHqsNzJpiSfQf](http://yunpan.cn/cHqsNzJpiSfQf)  访问密码 9412 <br/>
mysql源码：[http://mirrors.sohu.com/mysql/](http://mirrors.sohu.com/mysql/)<br/>
httpd源码：[http://apache.fayea.com/httpd/](http://mirrors.sohu.com/mysql/)<br/>
PHP源码：[http://php.net/releases/](http://php.net/releases/)<br/>

##系统环境查看


	> [root@asion ~]#   lsb_release  -a
	LSB-Version:core-4.0-ia32:core-4.0-noarch:graphics-4.0-ia32:gr  aphics-4.0-noarch:printing-4.0-ia32:printing-4.0-noarch
	Distributor ID:  CentOS
	Description:    CentOS release 5.5 (Final)
	Release:        5.5
	Codename:      Final


1. 编译环境的准备 (在编译之前，先安装好相应的编译器和库文件等)
    > yum -y install make apr* autoconf automake curl-devel gcc gcc-c++ zlib-devel openssl openssl-devel pcre-devel gd kernel keyutils patch perl kernel-headers compat* mpfr cpp glibc libgomp libstdc++-devel ppl cloog-ppl keyutils-libs-devel libcom_err-devel libsepol-devel libselinux-devel krb5-devel zlib-devel libXpm* freetype libjpeg* libpng* php-common php-gd ncurses* libtool* libxml2 libxml2-devel patch cmake

2. ftp环境的搭建（使用  非root  用户后，在ftp客户端上传相关的源码）
	    > yum install -y vsftpd
	    > useradd asion
	    > service vsftpd status
    
3. 下载相关的源码包(将源码cp到/usr/local/src/下，并进入)
	    > cd /usr/local/src/
	    > pwd

4.  解压相关源码文件
    	> tar -zxvf xxx.tar.gz

5. 进入解压后的文件夹
    	> cd xxx
    	> ./configure --help
    	> make
    	> make install
    
6. 安装成功后，在网站目录创建index.php文件，写入phpinfo()测试
    	> touch index.php
    	> echo '<?php phpinfo();>'  > index.php

7. 具体安装
    
    a. libxml2 安装（xml和html文件相关依赖的库）
        > tar -zxvf libxml2-2.6.30
        > cd libxml2-2.6.30
        > ./configure --prefix=/usr/local/libxml2
        > make && make install
    
    b. libmcrypt-2.5.8 安装（加密库）
        > cd /lamp/libmcrypt-2.5.8
        > ./configure --prefix=/usr/local/libmcrypt/
        > make 
        > make install
        
        == 进入libmcrypt-2.5.8文件夹内的 libltdl
        > cd ./libmcrypt-2.5.8/libltdl
        > ./configure --enable-ltdl-install
        > make
        > make install

    c. zlib库安装（不需要指定安装路径）
        > ./configure
        > make
        > make install 

    d. png图片库安装
        > ./configure --prefix=/usr/local/libpng/
        > make
        > make install

    e. jpeg图片库安装（需要自己创建jpeg6）
        > mkdir /usr/local/jpeg6
        > mkdir /usr/local/jpeg6/bin
        > mkdir /usr/local/jpeg6/lib
        > mkdir /usr/local/jpeg6/include
        > mkdir -p /usr/local/jpeg6/man/man1
        > cd /lamp/jpeg-6b
        > ./configure --prefix=/usr/local/jpeg6/ --enable-shared --enable-static
        > make
        > make install

    f. freetype字体库安装
        > ./configure --prefix=/usr/local/freetype/
        > make
        > make install

    g. autoconfig生成makefile安装（不需要指定安装路径）
        > ./configure
        > make 
        > make install

    h. GD 库的安装
        > ./configure --prefix=/usr/local/gd2/ --with-jpeg=/usr/local/jpeg6/ --with-freetype=/usr/local/freetype/ --enable-m4_pattern_allow 
        > make
        > make install
    
        注意：当make的时候，出现以下错误 
        configure.ac:64: error: possibly undefined macro: AM_ICONV 
        If this token and others are legitimate, please use m4_pattern_allow. 
        See the Autoconf documentation. 
        make: *** [configure] Error 1 
        
        解决方案：
        解决办法 ，编译加m4_pattern_allow参数 
        即：./configure --enable-m4_pattern_allow 
        便能顺利编译安装

    i. apache安装
       > ./configure --prefix=/usr/local/apache2  --sysconfdir=/etc/httpd/ --enable-rewrite --enable-so --enable-headers --enable-expires  --enable-modules=most --enable-deflate

    j. ncurses安装(Mysql安装前必须安装这个字符库)源码安装才需要)

    k. 安装mysql通用二进制包
        >  groupadd mysql
        >  useradd -g mysql mysql
        
        >  cd /usr/local/mysql-5.5.44-linux2.6-i686/
        >  chown -R msyql.mysql  .
        >  scripts/mysql_install_db --basedir=/usr/local/mysql  --user=mysql
        >  chown -R root .
        >  cp support-files/mysql.server /etc/init.d/mysqld
        
        >  chkconfig --add mysqld
        >  chkconfig mysqld on
        >  cp support-files/my-medium.cnf /etc/my.cnf
        
        >  service mysqld start
        >  ps aux | grep mysqld  
        
        注意：/usr/local/mysql/bin/mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory

        解决方案：libaio.i386 0:0.3.106-5
        > yum install libaio
        
        注意： selinux 必须关闭
        > sestatus -v
        > getenforce
        
        > 临时关闭
        > setenforce 0
        
        > 修改配置文件需要重启机器：
        > 修改/etc/selinux/config 文件
        > 将SELINUX=enforcing改为SELINUX=disabled
        > 重启机器即可 
        
        注意：
        [root@localhost mysql]# service mysql start
        Starting MySQL.. ERROR! The server quit without updating PID file (/var/lib/mysql/localhost.localdomain.pid).

        解决方案：
            1. 注释/etc/my.cnf里的skip-federated注释掉即#skip-federated；
            2. my.cnf文件配置过高，重新定义其中的参数（根据服务器情况定义）；
            3. 杀掉mysql_safe和mysqld进程，然后再重启；
            4. 当前日志文件过大，超出了my.cnf中定义的大小（默认为64M），删除日志文件再重启；
            5. 其他情况，查看日志文件（本人是/usr/local/data/bogon.error，具体因人而异），然后具体分析；

	l. 安装PHP
	      > cd /usr/local/src/php-5.3.28
	      > ./configure --prefix=/usr/local/php/ --with-config-file-path=/usr/local/php/etc/ --with-apxs2=/usr/local/apache2/bin/apxs --with-mysql=/usr/local/mysql/ --with-libxml-dir=/usr/local/libxml2/ --with-jpeg-dir=/usr/local/jpeg6/ --with-freetype-dir=/usr/local/freetype/ --with-gd=/usr/local/gd2/ --with-mcrypt=/usr/local/libmcrypt/ --with-mysqli=/usr/local/mysql/bin/mysql_config --enable-soap --enable-mbstring=all --enable-sockets 
	      >  make
	      >  make install
	      >  cp php.ini-dist /usr/local/php/etc/php.ini
    
    m. 打开Apache的配置文件（添加AddType这两行）
      > cd /etc/httpd/
      > vim httpd.conf
      > AddType application/x-httpd-php .php
      > AddType application/x-httpd-source .phps
    
    n. 更改php.ini里面的时区（设置为亚洲/上海）
        > cd /usr/local/php/etc/
        > vim php.ini
        > date.timezone = Asia/Shanghai
    
    o. 在网站根目录建立测试文件
        > cd /var/www/html
        > vim index.php
        > phpinfo()
    
    p. 在浏览器地址栏输入 http://localhost/index.php
