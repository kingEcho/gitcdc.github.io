---
layout: post
title: Mac安装PHP+Nginx+MySQL+Memcache(详细版)
category: 服务器
tags: Mac,Nginx,Memcache,php,Mysql
keywords: Mac,Nginx,Memcache,php,Mysql
description: 
---

看过很多网上安装PHP环境的文章，发现不是写得太散，就是很简略，导致我自己安装的时候遇到很多问题都无从解决（也有可能是自己笨，但我是不会承认的）。只能自己写篇博文记录下来，以后再遇到类似的问题可以看下，也希望能帮助到其他人。

### 1、安装<span style="color: #2980b9;">Brew</span>

首先要先为Mac安装Brew，Brew全称是HomeBrew，它在Mac的作用就相当于Ubuntu的apt-get，CentOs的Yum一样，重要性不言而喻。没有安装Brew请在终端输入以下指令（下面的指令都是终端执行），安装过的同学请跳过。

        ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"


**Brew的常用选项**

        brew install xxx （安装）
        
        brew uninstall xxx （卸载）
        
        brew list （已安装程序列表）
        
        brew update xxx （更新程序）


没有Root权限的话，请在前面加上sudo，（sudo brew install xxx）。

### 2、安装<span style="color: #2980b9;">Nginx</span>

使用指令

        brew install nginx


友情提醒：如遇到权限问题请在前面加上sudo。

安装成功后配置Nginx

**配置Nginx**

        vi /usr/local/etc/nginx/nginx.conf


在http{ ... }里面的最后一行添加：

        include /usr/local/etc/nginx/conf.d/*.conf;


记住是花括号里面的最后一行，不是外面。我这里使用的绝对路径，也可以使用相对路径来引入配置：

        include conf.d/*.conf;


接着来修改自定义配置

        mkdir /usr/local/etc/nginx/conf.d
        
        vi /usr/local/etc/nginx/conf.d/default.conf


增加一个监听端口

        server {
            listen       80;
            server_name  localhost;
        
            root /Users/username/Sites/; # 该项要修改为你准备存放相关网页的路径
        
            location / { 
                index index.php;
                autoindex on; 
            }   
        
            #proxy the php scripts to php-fpm  
            location ~ \.php$ {
                include /usr/local/etc/nginx/fastcgi.conf;
                fastcgi_intercept_errors on; 
                fastcgi_pass   127.0.0.1:9000; 
            }   
        }


这个时候还不能访问php站点，因为还没有开启php-fpm。

虽然mac 10.9自带了php-fpm，但是由于我们使用了最新的PHP，PHP中自带php-fpm，所以使用PHP中的php-fpm可以保证版本的一致。

下面是nginx的常用命令，我们安装php-fpm再来启动nginx

        nginx （开启nginx）
        
        nginx -s reload （重启nginx）
        
        nginx -s stop （关闭nginx）
        
        nginx -t （检测nginx配置是否有语法错误）


如果安装之后不能直接使用nginx，请把nginx程序复制到/usr/local/bin目录（用Brew安装的程序会放在/usr/local/Cellar目录下）：

        cp /usr/local/Cellar/nginx/1.6.2/bin/nginx /usr/local/bin/


这样就可以全局使用nginx命令了

### 3、安装<span style="color: #3498db;">PHP</span>

        brew update
        brew tap homebrew/dupes 
        brew tap homebrew/versions 
        brew tap homebrew/php
        brew install php54 --with-fpm


我是安装php5.4，可以根据自己需求修改版本，安装成功之后默认是不能全局使用PHP命令的，我们要先把php-fpm添加到/usr/local/bin目录下

        cp /usr/local/sbin/php-fpm /usr/local/bin/


接着就可以启动php-fpm

        php-fpm -D （-D的意思是后台运行的意思）
        nginx （这次启动nginx）


### 4、安装<span style="color: #3498db;">MySQL</span>

        brew install mysql
        unset TMPDIR
        mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp
        sudo chown -R your_user /usr/local/var/mysql


安装完成后启动mysql

        mysql.server start


最好给mysql设个密码

        mysqladmin -u root password '***'


如果想修改mysql的配置，在`/usr/local/etc`下建立一个`my.cnf`，例如增加log

        [mysqld]
        general-log
        general_log_file = /usr/local/var/log/mysqld.log


### 5、安装<span style="color: #3498db;">Memcache</span>

在网上找了好多资料，几乎都不行，后来发现很简单

先安装memcached服务

        brew install memcached


然后启动memcached

        memcached -d -m 2048 -p 11211


这里的-d跟启动php-fpm一样是后台运行，然后是-m：分配内存，这里是分配2G内存，-p是端口

然后安装php的memcache扩展，每个php版本都有对应的memcache版本，我在这里被坑了很多次

        brew install php54-memcache


这个扩展也是安装在/usr/local/Cellar目录下面，把对应目录下的memcache.so移到php的扩展目录下，然后引用，再重启php-fpm就可以了

        cp /usr/local/Cellar/php54-memcache/2.2.7/memcache.so /usr/local/etc/php/5.4/ext/


然后在`php.ini（/usr/local/etc/php/5.4/php.ini）`加入

        extension=memcache.so


再重启php-fpm即可运行memcache

        killall php-fpm
        php-fpm -D


### 完成

这样就完成整个环境部署了。有不懂的可以在下面留言

