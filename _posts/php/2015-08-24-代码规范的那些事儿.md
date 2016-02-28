---
layout: post
title: 代码规范的那些事儿
category: PHP
tags: php,代码规范
keywords: php,代码规范
description: 
---

作为一名程序员，在平常工作中，经常要维护别人的代码，也要看很多框架的源码，且不论代码优劣，单单代码规范就有很大差异了，其中，有看过几千行代码的类，也看过“糊在”一起的代码块，更有些代码杂乱且没有注释，理解起来难于登天最后只能重写。当然也看过很多人的代码很赏心悦目，阅读起来就很享受，看多了发现代码阅读性跟代码人的资历是成正比的，所以每次看框架源码，也会去学习他们的代码规范，这里我就简单分享下我以前的代码规范和现在的代码规范。每个人都有一套自己的代码规范，没有标准可言，只是分享下这种慢慢在进步的过程，勿喷^^。

**变量命名篇**

最开始受了很多同事的影响，导致我的代码里有各种变量命名方式，有中间下划线的、前面下划线的、驼峰型命名的，环境很容易影响编码模式。具体可以看下下面的代码。

        //取得帖子的廠牌跟車況數組
        $bk_arr = $this->_photoExper->getBkArr();
        $left_nav = $this->getLeftNav($_photo_obj,$bk_arr);&amp;lt;br />
        $this->Tmpl['brandCacheData'] = $left_nav['brandCacheData'];
        $this->Tmpl['brandCountry'] = $left_nav['brand_class'];
        //取得標籤分組的數據統計
        $cat_count = $this->_photoExper->getCatCount();
        $this->Tmpl['cat_count'] = $cat_count;


现在改成统一使用驼峰型变量命名法，这里科普下驼峰型变量命名法又分为两种，一种为小驼峰，即为首字母小写，一种为大驼峰，即首字母大写，我用的是小驼峰。统一变量命名方式后，代码整洁度提高了不少，我之所以舍弃掉下划线的写法是因为下划线看来很累赘，而且整体整洁度不高。具体如下：

        $guessResult = $this->autoModelGuess->hasGuess($mId, $modelId, 1);
        
        $modelImgList = $this->autoModelMap->getModelImgList($myid, $modelId, $guessResult); // 車模圖片
        
        if ($guessResult) {
        
            $pid = $this->autoModelMap->getModelImgPid($myid, $modelId);
        
            return redirect()->route('bigPicture', ['k' => $kindId, 'myid' => $myid, 'pid' => $pid]);
        
        }
        
        $modelOtherImgList = $this->autoModelMap->getModelOtherImgList($myid, $modelId); // 車模其他圖片
        
        $recommendList = $this->autoModel->getRecommendImgList($modelId, 6); // 其他車模圖片 


至于是使用下划钱还是驼峰型的命名方式就见仁见智了，无论哪种，我建议还是统一用一种就好。

**代码空行篇**

从上面的两段代码可以看到，以前的代码很少去换行，堆在一起，虽然没有完全看不懂，但阅读性也不咋样。现在的代码用空行来区分，阅读性比改变量命名还更有效果，不过并不是所有的代码都每行都空一行，我采用的是同个逻辑段的不空行，不同逻辑段的加空行，空行的好处就是阅读性提高之后，调试性也增强不少，我们在断点Debug的时候是不是经常要敲回车才输出，完了还要删除空行，用这种空行的形式就可以避免这种情况。

**空格篇**

这里的空格是指运算符和分隔符的空格，先来看未改变的代码：

        $array=array('apple','blue','city','double','ella');
        $filters=array();
        foreach($array as $key=>$value){
            if($value=='ella')continue;
            if($value=='double')$filters[$key]='F';
            $filters[$key]=$value;
        }
        
        $sql="SELECT * FROM a";
        $list= $db->fetch($sql);
        $sql="SELECT * FROM b";
        $bList=$db->fetch($sql);
        foreach($list as $key=>$value){
            if($value==$bList[$key]){
                unset($list[$key]);
                continue;
            }
        }


再来看看这段代码加了空格之后：

        $array = array('apple', 'blue', 'city', 'double', 'ella');
        $filters = array();
        
        foreach ($array as $key => $value) {
            if($value == 'ella') continue;
            if($value == 'double') $filters[$key] = 'F';
            $filters[$key]=$value; 
        }
        
        $sql = "SELECT * FROM a"; 
        $list = $db->fetch($sql);
        
        $sql = "SELECT * FROM b"; 
        $bList = $db->fetch($sql);
        
        foreach ($list as $key => $value) { 
            if ($value == $bList[$key]) { 
                unset($list[$key]);
                continue;
            }
        } 


相比两者，后者阅读性明显提高了，论空格的重要性是比空行还要重要的，我比较喜欢的空格方式是除去分隔符的所有符号是左右各一个空格（比如 &amp;&amp; 、 == 、=、+、%等等），分隔符则是后面空一格，常见于数组里面的逗号

        $array = array('a', 'b');


**方法命名篇**

上面讲到了变量的命名，其实方法的命名也很重要，这里不是讲要使用哪种方式命名，而是讲怎么取名字，讲个例子，如果现在要取数据库的数据，封装在一个方法中，有些人可能会这样：

        public function getData(){}


也有些人会这样：

        public function getList(){}


这两个方法命名是不是经常见到，我在公司平台上面也见过不少这样的命名方式，如果没有注释的话，其他人看到这个命名，是不是不知道什么东西，要去看方法里面的代码才会知道是什么意义，而且搜寻这个方法的时候也会跳出一大堆的索引方法，这比变量取名data还要愚蠢，那这个方法命名究竟是不是错的呢，这样的方法命名虽然没有错，但不是好的命名方式，我们应该根据具体的功能来命名方法，比如要取出类型为1的文章列表我们可以这样命名：`getArticleListByType`，这样在维护的时候或者其他人阅读的时候就能一目了然。

**注释篇**

与方法命名相辅相成的算是注释了，方法注释和必要的代码注释都很重要，一个几千行甚至上万行的代码库里如果没有注释的话，就算代码规范再好，也是极难阅读的，估计看到都会发晕。这里我用的代码注释用两种，直接上代码了：

        /**
         * 構造函數
         *
         * @param ArticleRepository $article 文章業務邏輯
         * @param RatingRepository $rating 評價業務邏輯
         * @param MovieRepository $movie 视频业务逻辑
         *
         * @return response
         */
        public function __construct(ArticleRepository $article, RatingRepository $rating, MovieRepository $movie)
        {
            $this->article = $article;
            $this->rating = $rating;
            $this->movie = $movie;
            parent::__construct();
        }


这个是方法注释，写明标题和参数和返回值。

        $searchResult  = $this->search();
        $artTotal = $this->article->getArticleTotal();
        $newMovie = $this->movie->getNewMovieList(5); // 最新影片
        $hotArticle = $this->article->getHot(10); // 熱門新聞
        $tag = $this->article->getArticleTag(); // 新聞標籤


这个是代码注释，如果注释偏长就在代码上一行，偏短就在代码后面写，并不是所有代码都注释，我是只在必要的逻辑代码才注释。

这些代码规范都是参考PSR-1，PSR-2标准，关于这两个标准可以在<a href="https://github.com/PizzaLiu/PHP-FIG" title="代码规范中文版">Github</a>上查看。

代码规范就分享到这里啦，写得不好的地方请勿喷～天天学习，天天向上～（完）

