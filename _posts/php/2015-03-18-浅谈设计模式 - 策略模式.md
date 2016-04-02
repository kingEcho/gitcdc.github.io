---
layout: post
title: 浅谈设计模式 - 策略模式
category: PHP
tags: 设计模式,php,策略模式
keywords: 设计模式,php,策略模式
description: 
---

前面讲到了工厂模式，今天来讲讲策略模式，工厂模式只是单纯的封装了行为。使用策略模式能把算法对象分别封装起来，让它们之间可以互相替换，减少了各种算法类与使用算法类之间的耦合。

我们来看看策略模式是怎么工作的：

[<img class="alignnone size-full wp-image-226" src="http://www.gitdc.com/wp-content/uploads/2015/03/QQ截图20150318221303.png" alt="策略模式类图" width="836" height="366" />][1]

这里可以看到，具体的算法对象继承了策略抽象类或者接口，通过Context类来维护引用。

我自己写了个Demo（凑合着看看）：

策略抽象类

        <?php
        /**
         *  策略抽象类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-18 22:25
         */
        abstract class cashSuper 
        {
            abstract protected function acceptCash();
        }


具体算法类

        <?php
        /**
         *  正常收费子类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-18 22:25
         */
        class cashNormal extends cashSuper 
        {
            public function acceptCash($money)
            {
                return $money; // 返回正常金额
            }
        }
        
        
        <?php
        /**
         *  打折收费子类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-18 22:25
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
        
        
        <?php
        /**
         *  返利收费子类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-18 22:25
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


策略类

        <?php
        /**
         *  收费策略类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-18 22:25
         */
        class cashStrategy 
        {
            private $cashObj; //传递过来的对象
        
            /**
             *  收费策略方法
             *  @param $obj 算法对象
             */
            public function __construct($obj)
            {
                $this->cashObj = $obj;
            }
        
            /**
             *  取得算法结果
             *  @param $money 金钱
             *  @return $result 计算之后的金钱
             */
            public function getResult($money)
            {
                //判断有无算法对象
                if (empty($this->cashObj)) {
                    return false;
                }
        
                //取得算法结果并返回
                $result = $this->cashObj->acceptCash($money);
        
                return $result;
            }
        }


客户端代码:

        <?php
        /**
         *  收费客户端
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-18 22:25
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
                    case '0.8折':
                        $cashObj = new cashStrategy(new cashRebate('0.8'));
                        break;
                    case '满300返100':
                        $cashObj = new cashStrategy(new cashReturn(300,100));
                        break;
                    default:
                        $cashObj = new cashStrategy(new cashNormal());
                        break;
                }
        
                $return_money = $cashObj->getResult($money);
                echo $return_money;
            }
        }


以上就是策略模式的代码，会不会发现，客户端的代码还是很多，我们更进一步，结合策略模式和工厂模式优化下

策略类

        <?php
        /**
         *  收费策略类
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-18 22:25
         */
        class cashStrategy 
        {
            private $cashObj; //传递过来的对象
        
            /**
             *  收费策略方法
             *  @param $type 算法类型
             */
            public function __construct($type)
            {
                switch ($type) {
                    case '0.8折':
                        $this->cashObj = new cashStrategy(new cashRebate('0.8'));
                        break;
                    case '满300返100':
                        $this->cashObj = new cashStrategy(new cashReturn(300,100));
                        break;
                    default:
                        $this->cashObj = new cashStrategy(new cashNormal());
                        break;
                }
        
            }
        
            /**
             *  取得算法结果
             *  @param $money 金钱
             *  @return $result 计算之后的金钱
             */
            public function getResult($money)
            {
                //判断有无算法对象
                if (empty($this->cashObj)) {
                    return false;
                }
        
                //取得算法结果并返回
                $result = $this->cashObj->acceptCash($money);
        
                return $result;
            }
        }


客户端代码

        <?php
        /**
         *  收费客户端
         *  @author Derek Chan
         *  @email dchan0831@gmail.com
         *  @blog http://www.gitdc.com
         *  @logtime 2015-03-18 22:25
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
        
                $cashObj = new cashStrategy($type);
        
                $return_money = $cashObj->getResult($money);
                echo $return_money;
            }
        }


策略和工厂结合就能让客户端的代码变得更简洁，而且还有另外的好处

工厂模式：需要让客户端认识到工厂对象和算法对象

策略+工厂模式：只让客户端认识到策略对象

策略+工厂模式能完全隔离开客户端和算法对象，使得整个系统更加灵活更易扩展。

相关阅读：<a href="http://www.gitdc.com/archives/182" target="_blank" title="浅谈设计模式 – 工厂模式">浅谈设计模式 – 工厂模式</a>

PS：代码未经任何调试，如需使用，请自行调试。


[1]: http://www.gitdc.com/wp-content/uploads/2015/03/QQ截图20150318221303.png
