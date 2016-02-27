---
layout: post
title: 站在巨鲸上玩耍——Docker
category: 服务器
tags: Docker
keywords: Docker
description: 
---

Docker是一种新兴的虚拟化技术，与传统的虚拟机不同，Docker能最大化利用物理机资源，并且快速、方便，除了这个，Docker还为团队运维自动化、组件化提供了很好的解决方案，可以想象下，以前我们在部署新服务器的环境时，需要每个服务都重新装一遍，而现在部署新服务器时只需要把对应的Docker镜像Down下来就可用，这无疑大大减少了运维成本。

而我在Mac上面使用Docker，当然是为了能多几台Web服务器，做多服务器串接实验。本文介绍了Docker安装，其他暂时不介绍。

今时今日，Docker生态已经很完善了，国外的[Docker Hub][1]官方镜像库，国内的[DaoCloud][2]镜像库涵盖了绝大部份镜像，如果在使用中没有你想要的镜像也可以自己制作，上传到这两个镜像库，贡献给社区。

<span style="line-height: 1.5;">由于Docker底层是基于Linux的，所以在OS X（Mac）上安装 Docker要依赖虚拟机来完成，所以首先要安装个虚拟机：VirtualBox，怎么安装自行Google。</span>

另外还需安装两个


1.  docker（OSX Docker服务）
2.  docker-machine（用来管理虚拟机的，早期Docker可以用boot2docker来管理，现在已经合并到这里面了）


docker我是用Homebrew安装的

        brew install docker docker-machine


用Homebrew安装后，出了点问题，当时也没怎么去深入研究，就改用官网给的方法安装。

        $ curl –L https://github.com/docker/machine/releases/download/v0.5.3/docker-machine_darwin-amd64 >/usr/local/bin/docker-machine &amp;&amp; \ chmod +x /usr/local/bin/docker-machine


这句话的意思应该都会明白吧，下载docker-machine到/usr/local/bin目录并设置权限，安装好之后就可以使用docker-machine来创建虚拟机了：

        docker-machine create --driver virtualbox dev


这里要注意，你打开VirtualBox是用你的mac用户打开的，当你用root来docker-machine 创建虚拟机的时候，你打开VirtualBox是看不到你创建的虚拟机的，我就被坑了好久。用root帐号打开VirtualBox可以解决这个问题，具体方法后面的文章再讲。创建完之后会显示用docker-machine env dev 可以查看刚刚创建的dev的信息：

        MacBook-Pro:~ root# docker-machine env dev
        export DOCKER_TLS_VERIFY="1"
        export DOCKER_HOST="tcp://192.168.99.100:2376"
        export DOCKER_CERT_PATH="/var/root/.docker/machine/machines/dev"
        export DOCKER_MACHINE_NAME="dev"
        # Run this command to configure your shell: 
        # eval "$(docker-machine env dev)"


接着将 dev 信息添加到环境变量中，这些环境变量将被 docker 使用。

        eval "$(docker-machine env dev)


运行镜像

        docker run helloworld


如果docker找不到本地有helloworld镜像，就回去远端镜像仓库搜寻。

docker就这样安装完成了，至于怎么使用docker-machine，docker请查看[Docker官方文档][3]。


[1]: https://hub.docker.com/
[2]: http://www.daocloud.io/
[3]: https://docs.docker.com/
