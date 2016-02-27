---
layout: post
title: 学习Docker遇到的坑
category: 服务器
tags: Docker
keywords: Docker
description: 
---

学习任何东西的过程中，难免会遇到会遇到很棘手的问题，这些问题短则耗费个把小时，长则耗费好几天，折磨到你是里外不是人，当然这些很棘手的问题最后都被消灭得无影无踪，在学习Docker时也遇到了几个，记录如下：

1、早期Docker用的boot2docker来做承载的环境，后来官方出了docker-machine来做载体，也就渐渐抛弃了boot2docker，也不能算是抛弃，而是整合进docker-machine里面了，而我在网上找的安装文章又是很久之前的，导致一堆问题，后来看了官方文档解决了。教训就是网上的教程请谨慎对待，最好看官方文档。

2、挂载文件：如果想在Docker容器当中挂载物理机中的Volumes（USB、移动硬盘、镜像等）文件夹，都必须要在docker-machine中的虚拟机先挂载，因为虚拟机中默认是只挂载物理机的用户文件夹的。

3、下载镜像慢：因为天朝某墙的原因，导致下载镜像很慢，换成国内镜像库会好点。

