---
layout: post
title: 浅谈面向对象 － 多态
category: PHP
tags: 面向对象,多态
keywords: 面向对象,多态
description: 
---

从字面来解释，多态就是事物的多种表现形式；从面向对象角度来解释的话，多态就是不同对象中同种行为的不同实现方式。举个例子：比如人吃饭，不同的人吃饭方式都是不同的，比如你可能是委婉型的，小口小口慢慢地吃，我是大口大口豪放型的吃。这就是吃饭这个行为的不同表现形式，统称「多态」。

编程中多态是很常见的，我们在不同的抽象（类）中用相同的方法名来命名同种行为，然后再实现不同的操作，这样做的优点是


* 易扩展；
* 调用方便；


但很多人会搞不清楚多态的概念，就会造成不同行为的操作也使用多态来实现。


* 概念易混淆；
* 乱用多态来实现不同的行为；


下面我们就用PHP代码来实现多态，例子：程序员在敲代码，球员在打球。

不用多态

        <?php
        class Coder 
        {
            public function playCode()
            {
                print_r("程序员在敲代码！    ");
            }
        }
        
        class Player
        {
            public function playBall()
            {
                print_r("球员在打球！    ");
            }
        }
        
        $coder = new Coder();
        $player = new Player();
        $coder->playCode();
        $player->playBall();


上面的代码可以看出两种职业人在工作，这是相同的行为，不同的内部操作，可使用多态来实现：

        <?php
        abstract class Professional
        {
            abstract public function play();
        }
        
        class Coder extends Professional
        {
            public function play()
            {
                print_r("程序员在敲代码！    ");
            }
        }
        
        class Player extends Professional
        {
            public function play()
            {
                print_r("球员在打球！    ");
            }
        }
        
        class Run
        {
            private $profess;
        
            public function __construct($profess)
            {
                $this->profess = $profess;
            }
        
            public function play()
            {
                if ($this->profess instanceof Professional) {
                    $this->profess->play();
                }
            }
        }
        
        $coder = new Run(new Coder);
        $coder->play();
        $player = new Run(new Player);
        $player->play();


上面的代码就是多态实现，是不是有点像策略模式；这样的好处就是再有这样的职业人添加进来只需要继承职业人类就可以实现相关的行为，那什么情况不能使用多态呢？

<blockquote>
  不同行为不可使用多态
</blockquote>

比如程序员在吃饭、球员在玩游戏，这两种行为抽象不出来他们的相同点，不能勉强进行多态化

**总结**

<blockquote>
  不要为了多态而多态
</blockquote>

