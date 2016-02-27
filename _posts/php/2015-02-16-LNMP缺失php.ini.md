---
layout: post
title: LNMP缺失php.ini
category: PHP
tags: LNMP,php.ini
keywords: LNMP,php.ini
description: 
---

在安装`Memcache`的时候需要在`php.ini`添加`Memcache`扩展，搜了php所在目录居然没有`php.ini`

        $ find /usr/local/php/ -name php.ini


继续搜整个服务器目录

        $ find / -name php.ini


发现`php.ini`在`/etc/php5/cli`目录里面

添加`memcache.so`到`php.ini`重启`php-fpm`，输出`phpinfo();`查看,居然没加载`Memcache`

<table width="600" cellpadding="3">
  <tbody>
    <tr>
      <td class="e">
        Configuration File (php.ini) Path
      </td>
      
      <td class="v">
        /usr/local/php-5.3.8/lib
      </td>
    </tr>
    
    <tr>
      <td class="e">
        Loaded Configuration File
      </td>
      
      <td class="v">
        (none)
      </td>
    </tr>
  </tbody>
</table>

再检查到`php.ini`默认加载目录是`/usr/local/php-5.3.8/lib`，而且`Loaded Configuration File`为空，就是说没有加载`php.ini`，没有加载`php.ini`网站居然能运行很让我不解，接着我把`php.ini`移到`phpinfo()`显示的默认地址

        $ mv /etc/php5/cli/php.ini /usr/local/php/lib/


重启php-fpm

        $ killall php-fpm
        
        $ /usr/local/php/sbin/php-fpm


再在`phpinfo()`查看，`Memcache`就已经加载到了。

问题总结：

问题可能造成原因：安装编译时没有指定php.ini路径

解决方法：

1、把php.ini移动到/usr/local/php-5.3.8/lib下，重启php-fpm（这里我也重启了Nginx）

2、在编译时添加编译参数--with-config-file-path=/usr/local/php-5.3.8/etc/php.ini

