---
layout: post
title: LNMP配合php-fpm安装memcache
category: PHP
tags: LNMP,Memcache
keywords: LNMP,Memcache
description: 
---

实现数据缓存目前我知道的有Memcache和Redis，这次来讲解怎么安装Memcache并使用，而在Nginx+php-fpm驱动php环境的情况下，利用apt-get安装Memcache好像不管用，所以我们直接在php的bin目录下安装Memcache

首先打开php的bin目录（我的是/usr/local/php/bin/）：

$ cd /usr/local/php/bin/

利用pecl安装memcache

$ ./pecl install memcache

这时候可能会出现以下提示

        downloading memcache-2.2.6.tgz …
        Starting to download memcache-2.2.6.tgz (35,957 bytes)
        ……….done: 35,957 bytes
        11 source files, building
        WARNING: php_bin /usr/local/php/bin/php appears to have a suffix -cgi/bin/php, but config variable php_suffix does not match
        running: phpize
        Configuring for:
        PHP Api Version: 20100412
        Zend Module Api No: 20100525
        Zend Extension Api No: 220100525
        Cannot find autoconf. Please check your autoconf installation and the
        $PHP_AUTOCONF environment variable. Then, rerun this script.
        
        ERROR: `phpize’ failed


出现这个的原因是autoconf没安装，接下来安装autoconf

        sudo apt-get install autoconf


接着重新安装Memcache，安装指令看上面

如果出现以下提示则表示成功

        Build process completed successfully
        Installing ‘/usr/local/php/lib/php/extensions/no-debug-non-zts-20100525/memcache.so’
        install ok: channel://pecl.php.net/memcache-2.2.6
        configuration option “php_ini” is not set to php.ini location
        You should add “extension=memcache.so” to php.ini


这时候你需要在`php.ini`添加：

extension=memcache.so

添加之后重启下php-fpm

        $ killall php-fpm
        
        $ /usr/local/php/sbin/php-fpm


这样Memcache就安装好了，我们来测试下

将以下代码写入php文件，在web端打开

        <?php
        $mem = new Memcache;
        $mem->connect('127.0.0.1', 11211);
        $mem->set('key','Hello World!', 0, 60);
        
        $val = $mem->get('key');
        echo $val;
        ?>  


如果有出现Hello World!就表示安装Memcache成功了。

