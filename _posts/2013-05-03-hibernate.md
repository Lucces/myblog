---
layout: post
title: "Simply entry about hibernate"
description: "Some simply entry about hibernate "
tags: [sample post, technology]
modified: 2013-05-03
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true
---

##Hibernate 复习回顾
1.Hibernate工作流程：应用程序会首先调用configuration类，然后通过读取hibernate.cfg.xml和xxx.hbm.xml文件生成一个sessionfactory对象，它代表表Hibernate所有java类到数据库映射的集合。利用sessionfactory对象可以生成一个session。在这个session中可以调用beginTranscation()方法创建一个业务，在这个业务中再利用session对象的load(),get(),save(),update(),delete(),saveorupdate()对数据库进行操作。最后调用session的fetTranscation().commit()方法将操作进行提交。

![](http://img.my.csdn.net/uploads/201008/6/0_12811082048b7J.gif)

2.Hibernate的缓存机制：hibernate缓存的主要作用是缓解数据库的访问压力，提高运行程序的性能。hibernate缓存分为两类：一级缓存为hibernate自带的，不可卸载，存在于session中，一旦session关闭，一级缓存也将失效。二级缓存在整个sessionfactory中实现，可应用于sessionfactory创建的所有session实例中，存在于整个sessionfactory中，Hibernate并没自带二级缓存，他只是提供了一些第三方二级缓存组件的接口，例如（EHcache）

如何开启二级缓存：在hibernate.cfg.xml中配置如下：

{% highlight xml %}
	<property name="hibernate.cache.provider_class">org.hibernate.cache.EhCacheProvider</property>
{% endhighlight %}
如何将特定的对象从二级缓存中清除：在session中配置如下：
{% highlight java %}
	Cache cache = HibernateUtil.getSessionFactory().getCache();	
	cache.evictEntityRegion(User.class);	
{% endhighlight %}
####基于注释的Hibernate (hibernate annotation)

**1.在pojo中**：

	@Entity @Table(name="") @Cacahe(Usage=...)

**一对多**：
{% highlight java %}
	user.java
		@OneToMany(MappedBy="user")
		public Address getAddress(){
			return this.address;
		} 
	address.java
		@ManyToOne
		@JoinColumn(name="user_id")
		public User getUser(){
			return this.user;
		}
{% endhighlight %}
**多对多**：
{% highlight java %}
	student.java
		@ManyToMany(MappedBy="student")
		public Teacher getTeacher(){
			return this.teacher;
		}
	teacher.java
		@ManyToMany
		@JoinTable(name="t_student_teacher",
					Joincolumns=@Joincolumn(name="student_id"),
					inverseJoincolums=@Joincolumn(name="teacher_id"))
		public Student getStudent(){
			return this.student;
		}
{% endhighlight %}
**一对一**：
{% highlight java %}
	person.java
		@OneToOne
		@PrimaryKeyJoinColumn
		public Card getCard(){
			return this.card;
		}
	card.java        					//这里的card不需要配置
		public Person getPerson(){
			return this.person;
		}
{% endhighlight %}
		
