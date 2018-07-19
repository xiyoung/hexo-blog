---
title: Nginx--基础篇
date: 2018-07-18 17:40:22
comments: true #是否可评论
toc: true #是否显示文章目录
categories: server #分类
top: 
tags:   #标签
    - nginx
copyright: true #显示版权信息
---

- 什么是Nginx

  Nginx("engine x")是一款是由俄罗斯的程序设计师Igor Sysoev所开发高性能的 **[Web](https://baike.baidu.com/item/WEB%E6%9C%8D%E5%8A%A1%E5%99%A8/8390210)**和 **[反向代理](https://www.cnblogs.com/Anker/p/6056540.html)** 服务器 。

- 安装

  Centos6.5为例，这里我们选择用压缩包安装

  1. 准备安装环境：
<!--more-->

     1.1安装gcc  

     安装Ngnix需要将官网下载的源码进行编译，而Nginx是c开发的，所以需要gcc的编译环境，注意切换到root用户。

     ```
     [root@localhost ~]# yum install gcc-c++
     ```

     1.2安装PCRE 

     Ngnix的http模块需要使用pcre来解析正则表达式 。

     ```
     [root@localhost ~]# yum install -y pcre-devel                             在询问是否下载时输入y
     ```

     1.3安装zlib 

     Ngnix使用zlib对http包的内容进行gzip 。

     ```
     [root@localhost ~]# yum install -y zlib zlib-devel
     ```

     1.4安装OpenSSl 

     OpenSSL是一个强大的安全套接字层密码库，囊括主要的密码算法、常见的密钥、证书封装管理功能及SSL协议。这是为nginx的https服务提供支持的。

     ```
     [root@localhost ~]# yum install -y openssl openssl-devel
     ```

  2.  下载nginx压缩包：这里使用的是nginx-1.10.2.tar.gz，在[nginx官网](http://nginx.org/en/download.html)找到当前稳定版。

     ```javascript
      //进入用户目录下载程序
      [root@localhost ~]# cd /usr/local/src
      //下载压缩包
      [root@localhost src]# wget http://nginx.org/download/nginx-1.10.2.tar.gz
     ```

  3. 安装Nginx

     ```javascript
     //解压
     [root@localhost src]# tar zxvf nginx-1.10.2.tar.gz
     //编译
     [root@localhost src]# cd nginx-1.10.2
     [root@localhost nginx-1.10.2]# ./configure && make && make install	
     ```

  4. 启动Nginx

     ```javascript
     //先找一下Nginx安装在了什么位置
     [root@localhost nginx-1.10.2]# whereis nginx 一般会出现 
     nignx /usr/local/nginx
     //进入/usr/local/nginx
     [root@localhost nginx-1.10.2]# cd /usr/local/nginx
     //执行启动命令
     [root@localhost nginx]# sbin/nginx
     //在浏览器输入服务器外网ip地址，出现 
     Welcome to nginx! 表示安装成功
     ```

  5. Nginx常用命令

     ```
     启动
     [root@localhost ~]# /usr/local/src/nginx/sbin/nginx
     同先进入/usr/local/nginx文件夹再执行sbin/nginx
     停止
     [root@localhost ~]# /usr/local/src/nginx/sbin/nginx -s stop
     验证配置文件是否正确
     [root@localhost ~]# /usr/local/src/nginx/sbin/nginx -t
     编辑配置文件
     [root@localhost ~]# vim /usr/local/src/nginx/conf/nginx.conf
     配置文件修改后需要重启才能生效
     [root@localhost ~]# /usr/local/src/nginx/sbin/nginx -s reload
     查看Nginx进程信息
     [root@localhost ~]# ps -aux | grep ngnix
     ```

- 目录结构

  ```
  [root@localhost ~]# tree /usr/local/src/nginx
  //如果提示tree命令不存在先安装 yum install tree
  
  /usr/local/nginx
  ├── client_body_temp
  ├── conf                             # Nginx所有配置文件的目录
  │   ├── fastcgi.conf                 # fastcgi相关参数的配置文件
  │   ├── fastcgi.conf.default         # fastcgi.conf的原始备份文件
  │   ├── fastcgi_params               # fastcgi的参数文件
  │   ├── fastcgi_params.default       # 所有结尾为default的文件都是备份文件
  │   ├── koi-utf
  │   ├── koi-win
  │   ├── mime.types                   # 媒体类型
  │   ├── mime.types.default
  │   ├── nginx.conf                   # Nginx主配置文件
  │   ├── nginx.conf.default
  │   ├── scgi_params                  # scgi相关参数文件
  │   ├── scgi_params.default  
  │   ├── uwsgi_params                 # uwsgi相关参数文件
  │   ├── uwsgi_params.default
  │   └── win-utf
  ├── fastcgi_temp                     # fastcgi临时数据目录
  ├── html                             # Nginx默认站点目录
  │   ├── 50x.html                     # 错误页面优雅替代显示文件，例如当出现502错误时会调用此页面
  │   └── index.html                   # 默认的首页文件
  ├── logs                             # Nginx日志目录
  │   ├── access.log                   # 访问日志文件
  │   ├── error.log                    # 错误日志文件
  │   └── nginx.pid                    # pid文件，Nginx进程启动后，会把所有进程的ID号写到此文件
  ├── proxy_temp                       # 临时目录
  ├── sbin                             # Nginx命令目录
  │   └── nginx                        # Nginx的启动命令
  ├── scgi_temp                        # 临时目录
  └── uwsgi_temp                       # 临时目录
  ```

- 日志类型

  1. 包括了：error.log和access.log
  2. 通过nginx.conf配置文件中log_format来定义要记录的变量格式来记录日志
  3. 可以被记录到日志中的变量 
     - HTTP请求变量 
       - arg_PARAMETER：request请求的参数
       - http_HEADER：request请求的header
       - sent_http_HEADER：服务端返回的header 
     - 内置变量 
       - Nginx内置
     - 自定义变量

- 配置文件

  ```
  ########### 每个指令必须有分号结束。#################
  #user administrator administrators;  #配置用户或者组，默认为nobody nobody。
  #worker_processes 2;  #允许生成的进程数，默认为1
  #pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
  error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别依次为：debug|info|notice|warn|error|crit|alert|emerg
  events {
      accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
      multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
      #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
      worker_connections  1024;    #最大连接数，默认为512
  }
  http {
      include       mime.types;   #文件扩展名与文件类型映射表
      default_type  application/octet-stream; #默认文件类型，默认为text/plain
      #access_log off; #取消服务日志    
      log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
      access_log log/access.log myFormat;  #combined为日志格式的默认值
      sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
      sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
      keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。
  
      upstream mysvr {   
        server 127.0.0.1:7878;
        server 192.168.10.121:3333 backup;  #热备
      }
      error_page 404 https://www.baidu.com; #错误页
      server {
          keepalive_requests 120; #单连接请求上限次数。
          listen       4545;   #监听端口
          server_name  127.0.0.1;   #监听地址       
          location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
             #root path;  #根目录
             #index vv.txt;  #设置默认页
             proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
             deny 127.0.0.1;  #拒绝的ip
             allow 172.18.5.54; #允许的ip           
          } 
      }
  }
  ```

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

- 

  

  

  

  

  

  

  

  

  

  



- 


