---
layout: post
title: 浅谈设计模式 - 工厂模式
category: PHP
tags: 设计模式,php
keywords: 设计模式,php
description: 
---

今天我来谈谈设计模式中常用的一个模式：工厂模式；

工厂模式的应用场景通常是需要根据不同参数来判断运行方法，为了避免逻辑处理代码的复杂度。我们可利用工厂模式，来减低逻辑处理的耦合度，提高代码可扩展性。

举个例子

商场里面的现金结算系统，有打8折和满500返50的功能，如果用一般的代码设计的话，就会是这样的：

        <?php
        /**
         *  收费客户端
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-16 20:05
         */
        class cashClient 
        {
            /**
             * 现金收费处理
             */
            public function doCashHandle()
            {
                $type = $_POST['type']; // 优惠类型
                $money = $_POST['money']; // 商品价格
        
                switch ($type) {
                    // 打八折处理
                    case '打8折':
                        echo $money * 0.8;
                        break;
        
                    // 满500返50处理
                    case '满500返50':
                        echo $money - floor($money / 500) * 50; 
                        break;
        
                    // 没有优惠处理
                    default:
                        echo $money;
                        break;
                 } 
            }
        }


这样写的话，如果需要添加其他算法的时候，客户端的代码就会越来越多，而且不容易维护，在这里我们引进工厂模式概念，利用工厂类来根据类型分发到各自的算法类中，达到客户端代码和算法

的隔离。

工厂模式类图 [<img class="alignnone size-full wp-image-203" src="http://www.gitdc.com/wp-content/uploads/2015/03/QQ截图20150316200927.png" alt="工厂模式类图" width="624" height="293" />][1]   代码如下：

简单工厂抽象类

        <?php
        /**
         *  简单工厂抽象类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-16 20:05
         */
        abstract class cashSuper 
        {
            abstract protected function acceptCash();
        }


正常收费算法类

        <?php
        /**
         *  正常收费子类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-16 20:05
         */
        class cashNormal extends cashSuper 
        {
            public function acceptCash($money)
            {
                return $money; // 返回正常金额
            }
        }


打折算法类

        <?php
        /**
         *  打折收费子类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-16 20:05
         */
        class cashRebate extends cashSuper 
        {
            protected $rebate; // 折扣
        
            /**
             *  构造方法
             *  @param rebate 折扣
             */
            public function __construct($rebate)
            {
                $this->rebate = $rebate; //实例化时，必须输入折扣（例如0.8）
            }
        
            public function acceptCash($money)
            {
                return $money * $this->rebate; // 返回打了折扣的金额;
            }
        }


返利算法类

        <?php
        /**
         *  返利收费子类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-16 20:05
         */
        class cashReturn extends cashSuper 
        {
            protected $condition; // 条件
            protected $return; // 返利金额
        
            /**
             *  构造方法
             *  @param rebate 折扣
             */
            public function __construct($condition, $return)
            {
                $this->rebate = $rebate; // 实例化时必须输入返利条件和返利金额
            }
        
        
            public function acceptCash($money)
            {
                return $money - floor($money / $condition) * $return; //返回返利之后的金额
            }
        }


算法工厂类

        <?php
        /**
         *  收费工厂类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-16 20:05
         */
        class cashFactory 
        {
            /**
             *  收费工厂方法
             */
            public function createCashFactory($type)
            {
                //通过传递过来的参数来判断进入哪个收费子类
                switch ($type) {
        
                    case '0.8折':
                        $cashObj = new cashRebate('0.8');
                        break;
        
                    case '满300返100':
                        $cashObj = new cashReturn(300,100);
                        break;
        
                    default:
                        $cashObj = new cashNormal();
                        break;
                }
                return $cashObj;
            }
        }


最后是客户端调用代码

        <?php
        /**
         *  收费客户端
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-16 20:05
         */
        class cashClient 
        {
            public function doAcceptCash()
            {
                $type = $_POST['type'];
                $money = $_POST['money'];</p>
        
                // 获取算法对象
                $cashObj = new cashFactory($type);
        
                // 取得返回金额
                $return_money = $cashObj->acceptCash($money);
        
                echo $return_money;
                }
        
        
            /**
             * 现金收费处理
             */
            public function doCashHandle()
            {
                $type = $_POST['type']; // 优惠类型
                $money = $_POST['money']; // 商品价格
        
                switch ($type) {
                    // 打八折处理
                    case '打8折':
                        echo $money * 0.8;
                        break;
        
                    // 满500返50处理
                    case '满500返50':
                        echo $money - floor($money / 500) * 50; 
                        break;
        
                    // 没有优惠处理
                    default:
                        echo $money;
                        break;
                } 
            }
        }


这样写扩展性就好很多，当你需要再扩展算法类的时候只需要再写多个子类，再在工厂类添加一个分支，这样就不用改动客户端的代码，使得整个系统更加灵活。

PS:这里的代码没经过编译，使用请自行调试。

end...


[1]: http://www.gitdc.com/wp-content/uploads/2015/03/QQ截图20150316200927.png
