---
layout: post
title: 利用Nginx＋lua验证Laravel路由
category: PHP
tags: Nginx,lua,Laravel
keywords: Nginx,lua,Laravel
description: 
---

前阵子重构部门业务框架，大部分代码转移到了Laravel，小部分代码遗留在旧框架中。在转移的过程中Nginx是无法通过通用配置来进行两个框架的Url重写切换的，Nginx需要对每个Module，甚至每个Action都进行配置，导致Nginx配置冗余巨大，参与的同事每上线一个功能就需要上服务器增加或修改Nginx配置，苦不堪言。

近来重构接近尾声，也需要对Nginx进行整顿，我利用Lua的性能优势，代理验证Laravel中的路由来进行框架跳转。以下是代碼：

**Nginx**

        location ~ ^/(.*) {
            rewrite_by_lua_file lua/verify_route.lua; #路由驗證lua
        }
        
        # 路由驗證
        location = /checkroute {
            proxy_pass http://$newcar_proxy;
        }
        
        location @new {
            add_header 'X-ROUTE-STATUS' 'new'; #记录路由验证状态：new
            proxy_pass http://$newcar_proxy; #新框架proxy
        }
        
        location @old {
            add_header 'X-ROUTE-STATUS' 'old'; #记录路由验证状态：old
            proxy_pass http://app_server; #旧框架proxy
        }


**verify_route.lua**

        ngx.req.set_header("X-ORG-REQUEST-URI", ngx.var.uri)
        ngx.req.set_header("X-ORG-REQUEST-METHOD", ngx.var.request_method)
        res = ngx.location.capture("/checkroute")
        
        if res.status == 404 then
            ngx.exec("@old")
        end
        
        ngx.exec("@new")


**Laravel Route**

        // 路由驗證
        Route::get('checkroute', function(Illuminate\Http\Request $request){
        
            $realReq = new \Illuminate\Http\Request([],[],[],[],[],[
                'REQUEST_URI'=>$request->server('HTTP_X_ORG_REQUEST_URI'), 
                'REQUEST_METHOD'=>$request->server('HTTP_X_ORG_REQUEST_METHOD')
            ]);
        
            $route = Route::getRoutes()->match($realReq);
        
            if ($route instanceof \Symfony\Component\HttpKernel\Exception\NotFoundHttpException) {
                abort(404);
            }
        
            abort(200);
        
        });


整個原理就是，通過lua請求Laravel設置的驗證路由方法，方法中通過lua定義的頭部信息實例了新的Request，再去匹配路由表，返回結果，無結果則跳回舊框架，有則跳轉到新框架（Laravel）。

