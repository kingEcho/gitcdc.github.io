---
layout: post
title: 利用Google PageSpeed模块优化网页加载速度
category: 服务器
tags: PageSpeed,Google,优化
keywords: PageSpeed,Google,优化
description: 
---

PageSpeed可以有效缩短网页加载的时间，减少网站服务器的带宽使用量。他里面有众多的重写"过滤器"，每个过滤器都可以选择性地开启/关闭，从而自动进行各种优化（比如，减小文档大小、减少HTTP请求数据、减少HTTP往返次数以及缩短DNS解析时间）。

下面是ngx_pagespeed支持的其中一些过滤器。想了解支持的全部过滤器，请参阅[官方文档][1]。


* Collapse Whitespace（压缩空白）：通过把HTML网页中的多处连续空白换成一处空白，减少带宽使用量。
* Canonicalize JavaScript Libraries（规范化转换JavaScript库）：通过自动把流行的JavaScript库换成免费托管的JavaScript库（比如由谷歌托管），减少带宽使用量。
* Combine CSS（合并CSS）：通过把多个CSS文件合并成一个CSS文件，减少HTTP请求数量。
* Combine JavaScript（合并JavaScript）：通过把多个JavaScript文件合并成一个JavaScript文件，减少HTTP请求数量。
* Elide Attributes（省略属性）：通过删除由默认属性指定的标签，缩小文档大小。
* Extend Cache（扩展缓存）：通过优化网页资源的可缓存性，减少带宽使用量。
* Flatten CSS Imports（精简CSS导入）：通过删除CSS文件中的@import，减少HTTP请求往返次数。
* Lazyload Images（延时加载图片）：延时加载在客户端浏览器上看不见的图片。
* Minify JavaScript（缩小JavaScript）：通过缩小JavaScript，减少带宽使用量。
* Optimize Images（优化图片）：通过引入更多的内嵌图片、压缩图片，或者将GIF图片转换成PNG图片，优化图片分发。
* Pre-Resolve DNS（预解析DNS）：通过预解析DNS，缩短DNS解析时间。
* Prioritize Critical CSS（优化加载关键CSS规则）：重写CSS文件，以便首先加载渲染页面的CSS规则。


我的服务器系统是Ubuntu，所以下面介绍的都是基于Ubuntu的安装方式

下载安装所需的依赖库

        $ sudo apt-get install build-essential zlib1g-dev libpcre3-dev


下载并安装ngx_pagespeed源代码，如下所示。`ngx_pagespeed`会被解压缩到`/usr/local/nginx/modules/ngx_pagespeed-1.7.30.3-beta`，我的所有安装包都放在`/usr/local/src`目录下

        $ cd /usr/local/src/
        
        $ wget https://github.com/pagespeed/ngx_pagespeed/archive/v1.7.30.3-beta.tar.gz
        
        $ sudo tar xvfvz v1.7.30.3-beta.tar.gz


下载预构建的PSOL（PageSpeed优化库，https://developers.google.com/speed/pagespeed/psol）到/usr/local/src/，并将它安装到ngx_pagespeed目录

        $ wget https://dl.google.com/dl/page-speed/psol/1.7.30.3.tar.gz
        
        $ sudo tar xvfvz 1.7.30.3.tar.gz -C /usr/local/src/ngx_pagespeed-1.7.30.3-beta --no-same-owner
        
        $ sudo find /usr/local/src/ngx_pagespeed-1.7.30.3-beta/ -type d -exec chmod +rx {} \;
        
        $ sudo find /usr/local/src/ngx_pagespeed-1.7.30.3-beta/ -type f -exec chmod +r {} \;


Nginx不像Apache那样可以直接引入模块，Nginx需要重新编译，我们先查看装好了的Nginx的编译配置

        $ /usr/local/nginx/nginx -V
        
        nginx version: nginx/1.4.4
        
        built by gcc 4.8.2 20131212 (Red Hat 4.8.2-7) (GCC)
        
        configure arguments: ......


接着下载同版本的Nginx（已经下载过的直接进入nginx安装包目录）

        $ wget http://nginx.org/download/nginx-1.4.4.tar.gz


最后，在ngx_pagespeed模块启用的情况下，（也就是把--add-module=....ngx_pagespeed放最前面，我试过放在后面，make的时候报错），编译Nginx(把上面查到的nginx编译配置copy下来放在ngx_pagespeed后面)，并安装它，如下所示。

        $ tar xvfvz nginx-1.4.4.tar.gz
        
        $ cd nginx-1.4.4
        
        $ ./configure --add-module=/usr/local/nginx/modules/ngx_pagespeed-1.7.30.3-beta --prefix=/usr/local/nginx --sbin-path=/usr/local/sbin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/run/nginx.pid --lock-path=/run/lock/subsys/nginx --user=nginx --group=nginx 
        
        $ make
        
        $ sudo make install


确认下，ngx_pagespeed模块是否已添加到安装的Nginx系统上，如下所示。

        $ /usr/local/nginx/nginx -V
        
        nginx version: nginx/1.4.4
        
        built by gcc 4.8.2 20131212 (Red Hat 4.8.2-7) (GCC)
        
        configure arguments: --add-module=/usr/local/nginx/modules/ngx_pagespeed-1.7.30.3-beta . . . .


接着在nginx.conf配置PageSpeed模块，PageSpeed有两种不同的级别可供你选择：CoreFilters和PassThrough。除非有所指定，否则默认情况下使用CoreFilters，新手可直接使用默认的CoreFilters。如下所示：

        server {
        
        # 侦听的端口
        
        listen 80;
        
        # 服务器名称
        
        server_name gitdc.com www.gitdc.com;
        
        # 记下根目录
        
        root /var/www;
        
        # 访问日志
        
        access_log /var/log/nginx/access.log main;
        
        # 启用ngx_pagespeed
        
        pagespeed on;
        
        pagespeed FileCachePath /var/ngx_pagespeed_cache;
        
        # 启用CoreFilters
        
        pagespeed RewriteLevel CoreFilters;
        
        # 禁用CoreFilters中的某些过滤器
        
        pagespeed DisableFilters rewrite_images;
        
        # 选择性地启用额外的过滤器
        
        pagespeed EnableFilters collapse_whitespace;
        
        pagespeed EnableFilters lazyload_images;
        
        pagespeed EnableFilters insert_dns_prefetch;
        
        }


我们这里选择PageThrough级别，可手动配置多种过滤器，配置如下：

        server {
        
        # 侦听的端口
        
        listen 80;
        
        # 服务器名称
        
        server_name gitdc.com www.gitdc.com;
        
        # 记下根目录
        
        root /var/www;
        
        # 访问日志
        
        access_log /var/log/nginx/access.log main;
        
        # 启用ngx_pagespeed
        
        pagespeed on;
        
        pagespeed FileCachePath /var/ngx_pagespeed_cache;
        
        # 禁用CoreFilters
        
        pagespeed RewriteLevel PassThrough;
        
        # 启用压缩空白过滤器
        
        pagespeed EnableFilters collapse_whitespace;
        
        # 启用JavaScript库卸载
        
        pagespeed EnableFilters canonicalize_javascript_libraries;
        
        # 把多个CSS文件合并成一个CSS文件
        
        pagespeed EnableFilters combine_css;
        
        # 把多个JavaScript文件合并成一个JavaScript文件
        
        pagespeed EnableFilters combine_javascript;
        
        # 删除带默认属性的标签
        
        pagespeed EnableFilters elide_attributes;
        
        # 改善资源的可缓存性
        
        pagespeed EnableFilters extend_cache;
        
        # 更换被导入文件的@import，精简CSS文件
        
        pagespeed EnableFilters flatten_css_imports;
        
        pagespeed CssFlattenMaxBytes 5120;
        
        # 延时加载客户端看不见的图片
        
        pagespeed EnableFilters lazyload_images;
        
        # 启用JavaScript缩小机制
        
        pagespeed EnableFilters rewrite_javascript;
        
        # 启用图片优化机制
        
        pagespeed EnableFilters rewrite_images;
        
        # 预解析DNS查询
        
        pagespeed EnableFilters insert_dns_prefetch;
        
        # 重写CSS，首先加载渲染页面的CSS规则
        
        pagespeed EnableFilters prioritize_critical_css;
        
        }


另外的配置步骤：

创建将由Nginx写入的一个文件缓存目录。

        $ sudo mkdir /var/ngx_pagespeed_cache
        
        $ sudo chown nginx:nginx /var/ngx_pagespeed_cache


如果希望代码简洁，可以写到别的配置文件，然后在nginx.conf里面引入，这里我就不演示了。

最后重启nginx

        $ /usr/local/nginx/nginx -s reload


 


[1]: http://ngxpagespeed.com/ngx_pagespeed_example/
