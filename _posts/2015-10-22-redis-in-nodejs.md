---
layout: post
title: “redis in nodejs”
description: “关于redis的个人总结”
tags: [sample post, code]
modified: 2015-10-22
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true
---
## redis in nodejs

### nodejs开发中使用redis作为缓存方案，下面我就介绍一下自己最近的在node项目中使用redis的经验
#### 第一，创建一个redis-factory.js模块，类似java中的工厂类，用于创建redisClient，具体代码如下：
	{% highlight javascript %}
	var exp = module.exports;

	var redis = require("redis");//引入redis模块
	var config = require("../conf/redisconf.json");//引入配置文件
	
	exp.createStoreClient = function(opts){
		if (!opts) {
			opts = {};
		}
		opts['auth_pass'] = config.store_pass;
	
		//调用redis.createClient(port, host, opts)方法获得createClient
		return redis.createClient(config.store_port, config.store_host, opts);
	};
	
	//同上
	exp.createPublishClient = function(opts){
		if (!opts) {
			opts = {};
		}
		opts['auth_pass'] = config.publish_pass;
	
		return redis.createClient(config.publish_port, config.publish_host, opts);
	};
	{% endhighlight %}
	
#### 第二，创建redis-pubsub.js和redis-store.js模块分别用于处理`发布/订阅`和`存储`逻辑，如redis-pubsub.js中：
	
	{% highlight javascript %}
	var exp = module.exports;

	var factory = require("./redis-factory");

	var pubClient = factory.createPublishClient();
	
	var TEST_CHANNEL = "test_channel";
	var TEST_PUBLISH_TO = "test_publish_to_";
	
	exp.publishMeta = function(testId, event) {
		pubClient.publish(TEST_PUBLISH_TO + testId, event);
	};
	
	exp.addAdminSubListener = function(listener) {
		var subClient = factory.createPublishClient();
		subClient.on('message', listener);
		subClient.subscribe(LIVE_ADMIN_CHANNEL);
	};
	
	//可以跟随自己的需求自定义方法
	//....
	{% endhighlight%}