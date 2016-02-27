---
layout: post
title: 浅谈设计模式 － 原型模式
category: PHP
tags: 设计模式,php,原型模式
keywords: 设计模式,php,原型模式
description: 
---

之前在学设计模式的时候，总会去查找相关的博客文章，但发现很多博主只写了几个模式就没写下去了，当初很是不明白，为什么不写完呢，现在自己来记录这些的时候，才体会到，常用的设计模式就那么几个，例如工厂模式、策略模式、单例模式，而且现在互联网资源那么多，完全没必要自己再去码字纪录，再后来也就造成了大家都是写那么几个就不写的情况。

有始有终是最重要的，既然开始写了，就坚持下去吧，进入正题，今天要写的是原型模式，就是克隆［复制］，这个也是不常用的设计模式，常用于需要实例化多遍，结构多变的场景中，如下：

        <?php
        /**
         * 原型抽象类
         */
        abstract class Prototype 
        {
            abstract public function copy(); // 克隆的抽象方法
        }
        
        /** 
         * 原型抽象实体 
         */ 
        class ConcretePrototype extends Prototype 
        {
            public $value;
        
            /**
             * 构造方法，用于储存传递过来的值
             *
             * @param [type] $value [description]
             */
            public function __construct($value)
            {
                $this->value = $value;
            }
        
            /**
             * 获取传递进来的值
             *
             * @return $value
             */
            public function getValue()
            {
                return $this->value;
            }
        
            /**
             * 设置值
             *
             * @param $value 
             */
            public function setValue($value)
            {
                $this->value = $value;
            }
        
            /**
             * 克隆方法
             *
             * @return object $this
             */
            public function copy()
            {
                return clone $this;
            }
        }
        
        /** 
         * 客户端 
         */ 
        class Client {
        
            public static function main()
            {
                $obj = new ConcretePrototype('test');
        
                $cloneObj = $obj->copy();
        
                $cloneObj->setValue('test1');
        
                print_r($obj->getValue() . "    ");
        
                print_r($cloneObj->getValue());
            }
        }
        
        Client::main(); </code>


输出结果为：

        test
        test1


看着是很普通的一段代码，精髓在于copy方法里面的

return clone $this;

如果少了clone，输出结果就会是

        test1
        test1


这是为什么呢？

直接返回自身对象是返回了该对象的指针，将自身对象引用到另一个变量，两者使用的内存地址是一样的，一处改变则全部都该改变，所以直接变量返回是起不了复制的效果的，为此 PHP5提供了一个专门用于复制对象的操作，也就是 clone 。这就是对象复制的由来。

**Prototype模式优点**


1.  可以在运行时刻增加和删除产品 
2.  可以改变值以指定新对象 
3.  可以改变结构以指定新对象 
4.  减少子类的构造 
5.  用类动态配置应用 


**Prototype模式的缺点**


* Prototype模式的最主要缺点就是每一个类必须配备一个克隆方法。 而且这个克隆方法需要对类的功能进行通盘考虑，这对全新的类来说不是很难，但对已有的类进行改造时，不一定是件容易的事。 


**适用性**


1.  当一个系统应该独立于它的产品创建、构成和表示时，要使用Prototype模式 
2.  当要实例化的类是在运行时刻指定时，例如动态加载 
3.  为了避免创建一个与产品类层次平等的工厂类层次时； 
4.  当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些


