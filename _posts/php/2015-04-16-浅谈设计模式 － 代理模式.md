---
layout: post
title: 浅谈设计模式 － 代理模式
category: PHP
tags: 设计模式,php
keywords: 设计模式,php
description: 
---

《大话设计模式》里面，有这样一则小故事：小菜帮修过电脑的女同学（娇娇）找他吃饭，他借机问娇娇有没男朋友，娇娇却很残忍的告诉他已经有男朋友（戴励），小菜很好奇地想知道娇娇跟戴励的恋爱故事，之后两人就陷入了“回忆”当中。

“娇娇同学，这是有人送你的礼物。”一个男生手拿着一个芭比娃娃送到她的面前。

“戴励同学，这是什么意思？”娇娇望着同伴的这个男生，感觉很奇怪。

“是这样的，我的好朋友，隔壁三班的卓贾易同学，请我代他送你这个礼物的。”戴励有些脸红。

“为什么要送我礼物，我不认识他呀。”

“他说......他说......他说想和你交个朋友。”戴励脸更红了，右手抓后脑勺，说话吞吞吐吐。

“不用这样，我不需要礼物的。”娇娇显然想拒绝。

“别别别，他是我最好的朋友，他请我代他送礼物给你，也是下了很大决心的，你看在我之前市场帮你辅导数学习题的面子上，就接受下吧。”戴励有些着急。

“那好吧，今天我对解析几何的椭圆那里还是不太懂，你再给我讲讲。”娇娇提出条件后接过礼物。

“没问题，我们到教室去讲吧。”戴励松了口气。

。。。几天后

“娇娇这是卓贾易送你的花。”

。。。几天后

“娇娇，这是卓贾易送你的巧克力。”

“我不要他送的东西了，我也不想和他交朋友。我愿意...我愿意和你做朋友！”娇娇终于忍不住了，直接表白。

“啊！......我不是在做梦吧！.......”戴励喜从天降，不敢相信。

“呆子！”娇娇微笑的骂道。

这则小故事对戴励来说挺美好，但对卓贾易来说却是挺残酷的。然而，这也告诉我们，千万不要找人帮你代理谈恋爱，否则人财空，我是绝对不会这样做的~_~

扯了这么多，我们来讲讲这则故事涉及到的设计模式——代理模式

先用没有代理的代码来实现上面这则故事

        <?php
        /**
         * 没有代理的情况
         */
        
        /**
         * 追求者类
         */
        class pursuit
        {
            protected $girl;
        
            /**
             * 构造方法
             * @param $girl 被追求者对象
             */
            public function __construct($girl){
                $this-&amp;gt;girl = $girl;
            }
        
            /**
             *  送鲜花
             */
            public function giveFlower()
            {
                return "送了花给" . $this->girl->name;
            }
        
            /**
             *  送巧克力
             */
            public function giveChocolate()
            {
                return "送了巧克力给" . $this->girl->name;
            }
        }
        
        class girl
        {
            public $name;
        
            public function __construct($name)
            {
                $this->name = $name;
            }
        
        
        }
        
        $girl = new girl('娇娇');
        
        $pursuit = new pursuit($girl); 
        echo $pursuit->giveFlower() . "\t\n"; 
        echo $pursuit->giveChocolate();


这是没有代理的情况，显然不符合上面故事的逻辑，我们再来看看只有代理的代码

        <?php
        /**
         * 只有代理的情况
         */
        
        /**
         * 追求者类
         */
        class proxy
        {
            protected $girl;
        
            /**
             * 构造方法
             * @param $girl 被追求者对象
             */
            public function __construct($girl)
            {
                $this->girl = $girl;
            }
        
            /**
             *  送鲜花
             */
            public function giveFlower()
            {
                return "送了花给" . $this->girl->name;
            }
        
            /**
             *  送巧克力
             */
            public function giveChocolate()
            {
                return "送了巧克力给" . $this->girl->name;
            }
        
        }
        
        class girl
        {
        
            public $name;
        
            public function __construct($name)
            {
                $this->name = $name;
            }
        
        
        }
        
        $girl = new girl('娇娇');
        
        $proxy = new proxy($girl); 
        echo $proxy->giveFlower() . "\t\n"; 
        echo $proxy->giveChocolate();


这段代码只写了代理送东西给被追求者，还是与上面故事的逻辑不符合，我们再来看看上下两段代码，有没发现代理类跟追求者类除了类名其他都相同，看我怎么改写。

        <?php
        /**
         * 代理模式
         */
        
        /**
         * 送东西接口
         */
        interface giveGirl
        {
            public function giveFlower();
            public function giveChocolate();
        }
        
        /**
         * 追求者类
         */
        class pursuit implements giveGirl
        {
        
            protected $girl;
        
            /**
             * 构造方法
             * @param $girl 被追求者对象
             */
            public function __construct($girl)
            {
                $this->girl = $girl;
            }
        
            /**
             * 送花
             */
            public function giveFlower()
            {
                return "送花给" . $this->girl->name;
            }
        
            /**
             * 送巧克力
             */
            public function giveChocolate()
            {
                return "送巧克力给" . $this->girl->name;
            }
        
        }
        
        /** 
         * 代理类 
         */
        class proxy implements giveGirl
        {
            protected $pursuit;
        
            /**
             * 构造方法 － 实例化追求者类
             * @param $girl 被追求者
             */
            public function __construct($girl)
            {
                $this->pursuit = new pursuit($girl);
            }
        
            /**
             * 送花
             */
            public function giveFlower()
            {
                return $this->pursuit->giveFlower();
            }
        
            /**
             * 送巧克力
             */
            public function giveChocolate()
            {
                return $this->pursuit->giveChocolate();
            }
        
        
        }
        
        class girl
        {
            public $name;
        
            public function __construct($name)
            {
                $this->name = $name;
            }
        }
        
        $girl = new girl('娇娇');
        
        $proxy = new proxy($girl); 
        echo $proxy->giveFlower() . "\t\n"; 
        echo $proxy->giveChocolate();


代理模式的意义是为其他对象提供一种代理以控制对这个对象的访问。运用场景有接口实现，工具类调用等等，比如图片上传。

代理模式就讲到这里了，有什么问题可以在下面留言。

