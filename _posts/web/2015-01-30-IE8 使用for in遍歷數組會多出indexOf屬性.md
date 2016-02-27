---
layout: post
title: IE8 使用for in遍歷數組會多出indexOf屬性
category: web前端
tags: IE8,for in
keywords: IE8,for in
description: 
---

IE8在上傳圖片時會莫名其妙多出個框，

[<img class="alignnone size-full wp-image-50" src="http://blog.gitdc.com/wp-content/uploads/2015/01/RTX截圖未命名.png" alt="RTX截圖未命名" width="490" height="244" />][1]

在提交處理的for in 方法裡面打印出key值發現多了indexOf

[<img class="alignnone size-full wp-image-46" src="http://blog.gitdc.com/wp-content/uploads/2015/01/6E58A58E-9CE7-4496-A516-FE864D323DD0.png" alt="{6E58A58E-9CE7-4496-A516-FE864D323DD0}" width="214" height="159" />][2]  [<img class="alignnone size-medium wp-image-47" src="http://blog.gitdc.com/wp-content/uploads/2015/01/2898BC36-82B7-4F02-AB89-2DB1362C2905-300x202.jpg" alt="{2898BC36-82B7-4F02-AB89-2DB1362C2905}" width="300" height="202" />][3]

經查，發現页面里其它的js文件扩展了Array.prototype的属性與for in衝突到，改成其他遍歷方法即可


[1]: http://blog.gitdc.com/wp-content/uploads/2015/01/RTX截圖未命名.png
[2]: http://blog.gitdc.com/wp-content/uploads/2015/01/2898BC36-82B7-4F02-AB89-2DB1362C2905.png
[3]: http://blog.gitdc.com/wp-content/uploads/2015/01/6E58A58E-9CE7-4496-A516-FE864D323DD0.png
