---
layout: post
title: memcache实现高可用方案
category: php
tags: memcache
keywords: 
description: 
---

#memcache实现高可用方案



源码：[http://yunpan.cn/cHqfqdQagStBV](http://yunpan.cn/cHqfqdQagStBV)  访问密码 cb6f

**实现方案**：使用`repcached`实现高可用<br/>
repcached：全称 replication cached是由日本人发明的memcached的高可用性技术，简称`复制缓冲区技术`


**使用场景**：它是一个**单master** **单slave**的方案，但它的 master/slave都是可读写的，而且可以相互同步，如果 master 宕机， slave侦测后，它会自动 listen成为 master；而如果 slave坏掉， master也会侦测到连接断，它就会重新 listen等待新的 slave加入。



**实现步骤**：

1. 环境搭建
    `> yum -y install gcc gcc-c++ libevent-devel`

2. 下载 memcached-1.2.8-repcached-2.2.tar.gz
    ` > wget http://downloads.sourceforge.net/repcached/memcached-1.2.8-repcached-2.2.tar.gz`

3. 解压配置repcached
   ` > tar zxvf memcached-1.2.8-repcached-2.2.tar.gz`
   ` > ./configure --enable-replication --program-transform-name=s/memcached/repcached/ `

4. 编译安装 
   ` > make && make install`

5. 启动repcached (**注意不要使用 root 来启动服务**)
   - 启动主服务
   ` >  /usr/local/bin/repcached -p 11211 -v -d`
   - 启动从服务
    `>  /usr/local/bin/repcached -p 11212 -x localhost -v -d`
   - 注意：可以使用 `ps aux | grep repcached`  命令来查看进程
6. 参数解释
    -d ： 后台运行memcached进程
    -p ： 默认监听端口11211  11212
    -x ： 监听高可用机器，如果是监听默认端口不用写端口号，localhost就是监听本机,作用：第二个repcached监听第一个repcached如果有问题就接管

7. 测试
先链接 11211 这个端口的服务，设置name 的值是  asion
    `> telnet localhost 11211`
    `> set name 0 0 5`
    
**注意：在telnet 使用 quit 回车退出**

然后使用 11212 这个端口的服务
    `> telnet localhost 11212`
    `> get name`

同样的测试可以反之在进行

**实验完成!**
