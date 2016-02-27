---
layout: post
title: Nginx开启Gzip加快网站运行速度
category: 服务器
tags: 服务器,Nginx,Gzip
keywords: 服务器,Nginx,Gzip
description: 
---

有过网站优化经验的都知道，减少请求的页面大小可让网站速度变更快，这里我们可以利用Nginx自带的Gzip模块来实现优化

Gzip(GNU-ZIP)是一种压缩技术。经过Gzip压缩后页面大小可以变为原来的30%甚至更小，这样，用户浏览页面的时候速度会块得多。Gzip的压缩页面需要浏览器和服务器双方都支持，实际上就是服务器端压缩，传到浏览器后浏览器解压并解析。浏览器那里不需要我们担心，因为目前的巨大多数浏览器都支持解析gzip过的页面。 Nginx的压缩输出有一组gzip压缩指令来实现。相关指令位于nginx.conf 的http{….}两个大括号之间。

代码如下：

        gzip on;
        gzip_min_length  5k;
        gzip_buffers     4 16k;
        gzip_http_version 1.0;
        gzip_comp_level 3;
        gzip_types       text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
        gzip_vary on;


配置解释：

`gzip on;`  **这个指令用于开启或关闭Gzip模块（ on/off ）**

---

`gzip_min_length 1k;`  **设置语序压缩的页面最小字节数，建议设置成大于1k的字节数，小于1k可能会越压越大。**

---

`gzip_buffers 4 16k;`  **设置系统获取几个单位的缓存用于存储gzip的压缩结果数据流。4 16k代表以16k为单位，安装原始数据大小以16k为单位的4倍申请内存。**

---

`gzip_http_version 1.1;`  **识别http的协议版本(1.0/1.1)，这个可不写，默认设置为1.1。**

---

`gzip_comp_level 4;`  **Gzip压缩比，级数越高，页面就会被压缩得越小，当然，压缩时间也就越长，这里推荐设置成4。**

---

`gzip_types text/plain application/x-javascript text/css application/xml;`  **匹配mime类型进行压缩，无论是否指定,”text/html”类型总是会被压缩的。**

---

`gzip_vary on;`  **和http头有关系，加个vary头，给代理服务器用的，有的浏览器支持压缩，有的不支持，所以避免浪费不支持的也压缩，所以根据客户端的HTTP头来判断，是否需要压缩。**

注意是写在nginx.conf里的http{}里，不需要写在servel{}里面。

