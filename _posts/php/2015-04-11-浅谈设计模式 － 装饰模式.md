---
layout: post
title: 浅谈设计模式 － 装饰模式
category: PHP
tags: 设计模式,php,装饰模式
keywords: 设计模式,php,装饰模式
description: 
---

在开发项目的时候，常常会利用继承来实现子父类的功能传递。比如，我们开发一个餐厅点餐系统，点餐系统现在有饭类，面类这两种主食可以选择，搭配主食的有饮料，小食。

起初的架构是这样的：

[<img class="alignnone wp-image-254" src="http://blog.gitdc.com/wp-content/uploads/2015/04/QQ截图20150410231004.png" alt="QQ截图20150410231004" width="660" height="354" />][1]

当改变很少的时候，这种框架勉强还能用，但是当需求开始改变的时候就出问题了。比如我点了饭，还需要加杯饮料，再加点小食，这个时候就要去改动所有的相关类，这样就违反了设计模式里面——开放封闭原则，即对扩展开放，对修改关闭，并且造成整个框架的耦合度变高，后续不好维护。

在这个项目里面我们可以发现，饭类，面类跟其他类是可以累加的，而且每次的累加顺序有可能不同。刚好，装饰模式就符合这样的场景。

此外，还有另外一种设计模式——建造者模式。建造者模式跟装饰模式很相近，之所以不用建造者模式是因为装饰模式跟建造者模式不同的地方在于建造者模式的构造是相对稳定的，而装饰模式的构造却是变化的，所以在这里我们运用装饰模式更为合适。

[<img class="alignnone wp-image-255" src="http://blog.gitdc.com/wp-content/uploads/2015/04/23-1024x623.png" alt="23" width="660" height="402" />][2]

MealClass（饭类）和NoodleClass（面类）作为被装饰者（主食），DrinkClass（饮料类）和SockClass（小食类）作为装饰者装饰主食，我们加入一个AgentClass（代理类）来作为装饰者的中介与被装饰者（主食）之间的通讯，下面是代码实现：

        <?php
        /**
         * 装饰模式基类 （餐厅）
         */
        abstract class HallClass
        {
            abstract public function cuisine();
        }
        
        /**
         * 被装饰者类 (主食 - 饭类)
         */
        class MealClass extends HallClass
        {
        
            /**
             * 烹饪输出
             */
            public function cuisine()
            {
                return '饭';
            } 
        
        }
        
        /**
         * 被装饰者类 (主食 - 面类)
         */
        class NoodleClass extends HallClass
        {
            /**
             * 烹饪输出
             * @return str
             */
            public function cuisine()
            {
                return '面';
            } 
        }
        
        /** 
         * 装饰模式代理类 
         */
        class AgentClass extends HallClass
        {
            public $food; 
        
            /**
             * 构造方法 - 用于接受传递过来的食物对象
             */
            public function __construct($food)
            {
                if ($food instanceOf HallClass) {
                    $this->food = $food;
                } else {
                    die('无效');
                }
            }
        
            public function cuisine(){} 
        }
        
        /** 
         * 装饰者类 （副食 - 饮料）
         */
        class DrinkClass extends AgentClass
        {
            /**
             * 烹饪输出
             * @return str
             */
            public function cuisine()
            {
                return $this->food->cuisine() . " + 饮料";
            }
        
        
        }
        
        /**
         * 装饰者类 （副食 - 小食） 
         */ 
        class SockClass extends AgentClass
        {
            /**
             * 烹饪输出
             * @return str
             */
            public function cuisine()
            {
                return $this->food->cuisine() . " + 小食";
            }
        }
        
        //面 + 小食 
        $noodle = new NoodleClass(); 
        $sock = new SockClass($noodle); 
        echo $sock->cuisine() . "\t\n";
        
        //饭 + 饮料 + 小食
        $meal = new MealClass(); 
        $drink = new DrinkClass($meal); 
        $sock = new SockClass($drink); 
        echo $sock->cuisine(); 


输出结果： 面 + 小食 饭 + 饮料 + 小食

最后总结：装饰模式感觉有点像无限递归，它可应用的场景主要是需求经常改变，有层次的项目，但并非所有场景都用到，可别为了设计模式而设计模式。~_~


[1]: http://blog.gitdc.com/wp-content/uploads/2015/04/QQ截图20150410231004.png
[2]: http://blog.gitdc.com/wp-content/uploads/2015/04/23.png
