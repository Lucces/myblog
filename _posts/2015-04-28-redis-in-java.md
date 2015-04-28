---
layout: post
title: “Practice advanced about redis”
description: “关于redis的个人总结”
tags: [sample post, code]
modified: 2015-04-28
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true
---
## redis in java
#### 最近自己玩了一下jedis，看了些资料，jedis是redis团队推荐使用的一款应用在java项目中的轻量级库，它可以将redis的命令映射到java方法为此我新建了一个工厂类并在其中添加了初始化方法（代码↓）。就我个人用到的功能大概可以分为两类：一、存储一些固定的信息，我喜欢称之为storeMsg，在初始化时我创建了一个用于存储数据的对象池，二、需要发布和订阅的消息，可以称之为：pubSubMsg，在初始化时我创建了一个用于发布/订阅数据的对象池.
	
	{% highlight xml %}
		public static void initialize() {
		//利用Apache commons提供的pool库实例对象池配置
		GenericObjectPoolConfig poolConfig = new GenericObjectPoolConfig();
		//最大连接数
		poolConfig.setMaxTotal(Config.jedis_max_total);
		//池中保留的最大空闲连接数   （大于则销毁）
		poolConfig.setMaxIdle(Config.jedis_max_idle);
		//池中保留的最小空闲连接数   （小于则创建）
		poolConfig.setMinIdle(Config.jedis_min_idle);
		
		//实例话存储对象池，需要存储主机名和端口号
		storePool = new JedisPool(poolConfig, Config.redis_store_host, Config.redis_store_port);
		//实例话发布对象池，需要主机名和端口号
		publishPool = new JedisPool(poolConfig, Config.redis_publish_host, Config.redis_publish_port);
	}
	{% endhighlight %}
#### 在工厂类中添加获取对象的方法，方便在外部获取storePool或者publishPool，其实这两个方法的实现方式是一模一样的，只是我们约定的后续使用功能不同而已，下面只贴其中一个（代码↓）。
	
	{% highlight xml %}
	public static Jedis getStoreRes() {
		if (storePool == null) {
			throw new JedisException(“plaeas initialize your Jedis first!”);
		}
		//在对象池中获取一个真真正正的jedis，然后把进行一下密码验证，so easy!
		Jedis jedis = storePool.getResource();
		jedis.auth(Config.redis_store_password);
		
		return jedis;
	}
	{% endhighlight %}

#### 对了，还有一个方法记着要加上，那就是对象池用完后要释放（代码↓）
	
	{% highlight xml %}
	public static void returnStoreRes(Jedis jedis) {
		//释放对象池，不然的话，你懂得！
		storePool.returnResource(jedis);
	}
	{% endhighlight %}

#### 接下来就简单的玩一下吧，简单思路：存储storeMsg，然后取出来。在这里我把storeMsg都存到了一个set中。
	
	{% highlight xml %}
	public static void addTest(String storeMsg) throws JedisException {
		Jedis client = null;
		try {
			client = RedisFactory.getStoreRes();
			//STORE_TEST_001是我定义的一个静态字符串常量，其实就是定义一个key值
			client.sadd(STORE_TEST, storeMsg);
		} catch (JedisException e) {
			throw e;
		} finally {
			//拜托，一定要记着释放，释放，释放！
			RedisFactory.returnStoreRes(client);
		}
	}

	//取出的方法我不想写了，为了让看文章的你可以立刻动起手来，看看你能不能把我的storeMsg都打印出来，可以提示一下用到的方法：
	client.smembers(“STORE_TEST”);
	{% endhighlight %}
	
#### OK，发布/定义消息的功能实现代码会稍后在放上来......


