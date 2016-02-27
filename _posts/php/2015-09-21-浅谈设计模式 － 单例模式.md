---
layout: post
title: 浅谈设计模式 － 单例模式
category: PHP
tags: 设计模式,php,单例模式
keywords: 设计模式,php,单例模式
description: 
---

每个第三季度都是最忙的，工程师活动，部门事务，终于得以闲下来打理自己的网站了，之后会恢复发布文章的频率（每周至少一篇），有什么聊什么，也沉淀下自己的文笔。

PS：博客没怎么去推广，就当作自己跟自己的谈话吧。

今天来浅谈下设计模式中的单例模式。单例模式是最常用到的设计模式之一，有时候在单线程中需要多次调用到同一个类对象，每实例化一次，内存君就多创建一个内存地址，这样就造成了资源浪费，严重的冗余。利用单例模式则可以有效的减少这种资源浪费。

        <?php 
        /**
         * 单例模式
         *
         * @author Derek Chan <dchan0831@gmail.com>
         * @version 2015-09-28
         */
        class Single {
        
            private static $model;
        
            /**
             * 構造函數 － 私有化
             * 不讓外部實例化
             */
            private function __construct(){}
        
            /**
             * 內部實例化方法
             *
             * @return object $model
             */
            public static function getInstance(){
        
                if (is_null(self::$model)) {
                    self::$model = new static;
                }
        
                return self::$model;
        
            }
        
            public function test(){
                echo 'Hello World!';
            }
        }
        
        $model = Single::getInstance(); 
        $model->test();  


如果再追求完美點，可以將__clone方法給私有化防止克隆來保證單例的特性。 以上

