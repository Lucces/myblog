---
layout: post
title: "page number"
description: "put the number of current page in the center."
tags: [sample post, technology]
modified: 2014-08-13
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true
---

##分页页码居中方法 ##

###将当前页码在页面的中居中显示，如 11 12 13 15 16 其中的13是当前页码 ##

{% highlight java %} 
    	public List<Integer> getSlider(int count,int pageNum) {
			int halfSize = count / 2;
			int totalPage = getTotalPages();
	 
			int startPageNo = Math.max(pageNum - halfSize, 1);
			int endPageNo = Math.min(startPageNo + count - 1, totalPage);
	 
			if (endPageNo - startPageNo < count) {
				startPageNo = Math.max(endPageNo - count, 1);
			}
	 
			List<Integer> result = new ArrayList<Integer>;
			for (int i = startPageNo; i <= endPageNo; i++) {
				result.add(i);
			}
			return result;
		}
{% endhighlight %}