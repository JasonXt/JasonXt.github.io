---
layout: post
title: "Laravel appetizer"
date: 2016-02-01 16:03:56 +0800
comments: true
categories: PHP
---

Laravel开胃菜
==========================
#### 教程手册

1. [laravel-5-fundamentals](https://laracasts.com/series/laravel-5-fundamentals)
2. [国内Enda袁超Laravel视频教程](http://www.phpyc.com/search/tag/44)
3. [最适合中国人的 Laravel 教程](https://lvwenhan.com/laravel/432.html)  
4. [Laravel博客开发教程](http://laravelcoding.com/blog)
5. [Laravel手册](http://cheats.jesse-obrien.ca/)
6. [Laravel 官方文档](http://laravel.com/)   

就推荐这几个，其实最重要的还是官方文档，个人建议看英文，当然也有中文翻译[Laravel 中文文档](http://www.golaravel.com/)，这个自行选择。当然，如果你只是看文档，想深入很难，多看框架的实现代码才是王道！

#### 环境配置相关
Laravel本身提供[Homestead](https://laravel.com/docs/5.2/homestead)集成环境,很方便。
安装教程:[Windows Homestead Install](http://www.kyomini.com/content_16.html) / [Mac Homestead Install](http://www.kyomini.com/content_11.html)。当然如果你觉得都很麻烦，用[XAMPP](https://www.apachefriends.org/zh_cn/index.html)/[MNPP](https://www.mamp.info/en/)这类的也是可以的。

开发工具:PHPStorm必选无疑（新版的要激活了,好人做到底-->[ACTIVATION](http://www.xutao.win/blog/2015/11/22/phpstorm-activation/)）

断点调试：[Homstead+PHPStorm+XDebug](https://laracasts.com/forum/?p=1648-phpstorm-homestead-xdebug/0)

当然还有一些辅助调试的工具包，如:[barryvdh/laravel-debugbar](https://github.com/barryvdh/laravel-debugbar),可自行选择。

最后还有个头疼的[Composer](https://getcomposer.org/) 为什么头疼?GFW啊。。。。国内太慢，Laravel install 一下能半天。。。上梯子[Packagist / Composer 中国全量镜像](http://pkg.phpcomposer.com/) 怎么设置里面有说明。

####框架基础
如果你之前没都没有接触过框架，不懂框架是个什么样的概念，最好自行补课，至少得明白MVC框架（Laravel是MVC框架）是个什么意思。

Laravel的几个必知概念点:

- 模型（Model->Eloquent） 
- 视图（View->Blade Template）
- 控制器（Controller->[Route,MiddlerWare,Request,Controller,Response]）

这里我把Router、MiddleWare等也划入了Controller这个层面，我觉得这样比较好理解。

######模型
模型基本上就是用来和数据库打交道的。但也有人喜欢在Model里封装一些相关的方法，个人不建议这么的，当然这里的小技巧一会再说。
	
	<?php

	/**
	* Class Pad
	* @property string brand
	* @property integer RAM
	* @property integer size
	* @property float price
	* @property \Carbon\Carbon $created_at
	* @property \Carbon\Carbon $updated_at
	*/
	class Pad extends Eloquent
	{	
		protected $table = 'pads';
	}

这里建议把你模型的一些属性或者方法（基本就是你的数据库字段）以注释的形式写到模型类里，这样方便IDE识别或者提示。在模型类的命名空间方面需要注意的是,框架并不限制你把模型放到哪里（默认在app
目录下,5.1的命名空间为App）,这里建议是在app同级目录新建一个models目录，将这个目录用Conposer自动加载到根命名空间下。这里需要解释的是，我之所以去掉App命名空间是应为我的项目是L4.2开始的，在处理Polymorphic Relations的时候，xxxable_type里是存的根命名空间的模型类（如：User），所以这里为了省事，直接放到了根命名空间（当然还有其他方法）。

	"autoload": {
        "classmap": [
            "database"
        ],
        "psr-4": {
            "App\\": "app/",
            "":"models/"
        }
    }


#####视图
视图这个其实没什么好说的，在Laravel自带的Blade模板引擎用起来还是蛮顺手的，新版的PHPStorm直接支持的比较方便
#####控制器
控制器这里我把Route,MiddlerWare,Request,Controller,Response都划分了在一起，原因是应为他们结合在一起完成了请求的处理调度响应的控制流程，可能不太科学，但个人觉得这样比较适合新人理解。

Route在Laravel中起到了调度请求的一个作用，就像一条条马路一样，这条道到哪里，那条到哪里。当然，路由的调度，不一定要转接给控制器，路由也可以直接返回响应(闭包)。这里有个注意点，就是在使用Laravel的 resource controller的时候，如果你需要增加自定义的路由入口到该controller，请务必写在resource 导向之前。

	Route::get('photos/popular', 'PhotoController@method');
	Route::resource('photos', 'PhotoController');

MiddlerWare在Laravel中扮演的是一个“交警”的角色，它可以用来进行请求的一些处理前过滤，或者做请求处理后的善后工作，用法大家自行看文档吧。

Request在Laravel中相当于大路上行驶的车吧。他对用户的请求做了一下封装，我们可以通过facade或者依赖注入来获取Request实例，从而取得请求的参数等信息。

Controller是Laravel处理用户请求的处理中心，我们可以适当的把一些简单业务逻辑写在controller中，然后做出相应相应。但是我为什么说只是做一些简单的业务逻辑呢？ 当你业务逻辑太过于复杂的时候，你的代码就会相应的变成，那一个控制器的话会整个变得很长，不方便阅读;其次,这些复杂的业务逻辑如果都写在控制器的方法中，那么你很难做到复用。那这里就要用到Laravel的Repositories(5.1中是有默认文件夹的，5.2中好像是移除了默认文件夹，如果没有可自行在app目录下创建，注意遵循PSR4)，把你的业务逻辑写到Repositories中，然后在Controller中new或者依赖注入。
	
	<?php
	/**
	* Created by PhpStorm.
	* User: xutao
	* Date: 15/8/3
	* Time: 09:17
	*/

	namespace App\Repositories;

	use Carbon\Carbon;
	use Product;
	use WxPayUnifiedOrder;
	use WxPayOrderQuery;
	use WxPayApi;
	use NativePay;

	class WeChatPayRepository
	{
    	private $user;

    	public function __construct()
    	{
        	$this->user = \Auth::user();
    	}

    	/**
     	*  Create Inner Order
     	* @param Product|Eloquent/Model $product
     	* @param $amount
     	* @return array
    	*/
    	public function createOrder($product, $amount)
    	{
			//a lot of things for creating a order 
		}
	}
	
	
	namespace App\Http\Controllers;
	
	use App\Repositories\WeChatPayRepository;
	
	class DepositController extends Controller
	{	
	  	/**
     	* Create order and display the qrcode
     	* @param WeChatPayRepository $repository
     	* @param Request $request
     	* @return \Illuminate\Http\Response
     	*/
    	public function store(WeChatPayRepository $repository, Request $request)
    	{
        	$product = \Product::findOrFail(1);
        	$amount = $request->get('points', 0);
        	$trade = $repository->createOrder($product, $amount);
        	if (!$trade) {
        	    //生成订单出错
        	}
        	$url = route('qrcode', ['url' => $trade->url]);
        	return \Redirect::route('more.deposit.show', [$trade->id, 'url' => $url]);
    	}
	}


Response是最后一个点了。Response呢是Laravel对返回的一些封装，可以通过Response对象设置返回的header头，设置返回的数据等等。但是在web项目中，我们大多数返回的是视图（View），其实View渲染，然后封装成Response对象。

####框架进阶

######IoC容器和Facades
IoC容器和Facades是我们在Laravel框架中常用到的一些东西，可能新手不是太了解这个东西，如果要详细讲解起来会长，比较占篇幅，这里我给个写的不错的博文，大家可自行查看。[laravel 学习笔记 —— 神奇的服务容器](https://www.insp.top/article/learn-laravel-container)。

1. Container相当于一个箱子
2. Laravel在boot的时候通过ServiceProvider向箱子里面加东西，注明什么东西放在箱子的哪个地方
3. 当你需要什么东西的时候，通过Facade（放入时的注明）去取得这个东西（LazyLoad|如果放入的时候已经实例化了那取得的就是实例）。

######Artisan命令行
Laravel提供的Artisan方便了PHP的命令行方式的交互。目前在我们项目当中Artisan用得最多的是定时的计划任务、数据的批量处理等。注意:命令行模式做数据处理，请做好最全面的异常处理或者进度保存。

定时计划任务:需要结合Linux的Crontab来做定时。
	
	sudo crontab -e

	# automatic statistic 每小时的第10分钟运行一次artisan的statistic命令
	10 * * * * /usr/bin/php /var/www/admin/artisan statistic
	

	
#####Queue
Laravel的队列支持database, Beanstalkd, Amazon SQS, Redis和synchronous。最后一个是本地的文件方式，默认选项，可以当做开发使用，如果是在线上部署的话，我还是推荐Beanstalkd。理由是在国内的话Amazon、SQS等就不用考虑了，Database肯定不太适合（查询效率），那就剩下Beanstalkd和Redis，二者的话性能方面应该是差不多的，但是Redis没有一个可视化的管理界面，我们不能清楚的了解队列的状态，而这点Beanstalkd却提供有可视化界面[Tools](https://github.com/kr/beanstalkd/wiki/Tools)。这里推荐：[benstalkd_console](https://github.com/ptrofimov/beanstalk_console),都有现成的Docker Image Pull/Run就搞定了。


####总结
总的来说Laravel我也用了一年多了，感觉还是顺手的（可能我接触的框架比较少，至少时候Laravel甩Think3.X几条街吧，我木有鄙视国产的意思--|），IoC容器、DI、facade等用起来也是相当爽的，扩展性也是杠杠的，需要是什么扩展包去packagist或者git上找一找，require一下就OK了，所以在开发上会省很多造轮子的时间。

值得一提的是最近出了[Lumen5.2](https://lumen.laravel.com/)(Laravel的精简版，最新版本基本上以API为目标了)，那用Laravel做API也是66的了，大家可以去尝试下。（之前我是用的L4.2+[dingo API](https://github.com/dingo/api)做的）


Any way ，先说这么多等以后有想到什么再来补充吧。








	

