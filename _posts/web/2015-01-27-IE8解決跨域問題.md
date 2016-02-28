---
layout: post
title: IE8解決跨域問題
category: web前端
tags: IE8,跨域
keywords: IE8,跨域
description: 
---

使用ajax跨域請求時，在chrome、firefox等兼容性較好的瀏覽器中，可以看到返回結果、

但在IE中，ajax會自動跳到error方法中，打開f12調試，返回錯誤為（No Transport），苦惱了我很久

[<img class="alignnone wp-image-36 size-full" src="http://blog.gitdc.com/wp-content/uploads/2015/01/1.png" alt="" width="669" height="173" />][1]

解決方法：

在ajax前面添加 jQuery.support.cors = true;然後神奇的事情就發生了，可以跨域了！！！

[<img class="alignnone size-medium wp-image-35" src="http://blog.gitdc.com/wp-content/uploads/2015/01/RTX截圖未命名2-300x193.jpg" alt="RTX截圖未命名" width="300" height="193" />][2]

---

后来发现是自己的ie启用了跨域设置才可以使用这条语句，只能无奈的用jsonp了

 


[1]: http://blog.gitdc.com/wp-content/uploads/2015/01/1.png
[2]: http://blog.gitdc.com/wp-content/uploads/2015/01/RTX截圖未命名2.jpg
