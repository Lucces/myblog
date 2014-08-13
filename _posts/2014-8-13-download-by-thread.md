---
layout: post
title: "Download by java Thread"
description: "Download by java Thread."
tags: [sample post, technology]
modified: 2014-08-13
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true
---


## 利用java多线程进行下载
### 创建一个利用多线程下载的方法类
{% highlight java %}
	package com.mingcanit.test;
	 
	import java.io.File;
	import java.io.FileNotFoundException;
	import java.io.InputStream;
	import java.io.RandomAccessFile;
	import java.net.HttpURLConnection;
	import java.net.URL;
 
	public class DownLoadThred extends Thread{
	 	private URL url;
		private long start;
		private long end;
		private int threadNum;
		private RandomAccessFile raf;
		
		public DownLoadThred(URL url,long start,long end,int threadNum,File file) {
			this.url = url;
			this.start = start;
			this.threadNum = threadNum;
			this.end = end;
			try {
				raf = new RandomAccessFile(file, "rw");
			} catch (FileNotFoundException e) {
				e.printStackTrace();
			}
		}
		
		@Override
		public void run() {
			try {
				System.out.println("第" + threadNum + "个线程开始下载");
				System.out.println(threadNum + " start:" + start + "\tend:" + end);
				HttpURLConnection conn = (HttpURLConnection) url.openConnection();
				
				conn.setRequestProperty("RANGE", "bytes="+start+"-"+end);
				conn.setRequestProperty("User-Agent", "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.13 (KHTML, like Gecko) Chrome/24.0.1290.1 Safari/537.13");
				raf.seek(start);
				
				InputStream stream = conn.getInputStream();
				byte[] buffer = new byte[1024];
				int len = -1;
				while((len = stream.read(buffer)) != -1) {
					raf.write(buffer, 0, len);
				}
				raf.close();
				stream.close();
				
				System.out.println("第" + threadNum + "个线程下载完毕");
			} catch (Exception e) {
				e.printStackTrace();
			}
			
		}
	}
{% endhighlight %}
### 在test类中的main函数的测试代码为：
{% highlight java %}
	URL url = new URL("http://dl_dir.qq.com/qqfile/qq/QQ2012/QPlusDesktop4.2.exe");
	 
	URLConnection conn = url.openConnection();
	 
	System.out.println("size:" + conn.getContentLength());
	 
	File file = new File("C:/qqplus.exe");
	 
	int threadNum = 10;
	 
	 
	//每个线程平均下载字节数
	long threadDownloadSize = conn.getContentLength() / threadNum ;
	 
	 
	for (int i = 0; i < threadNum; i++) {
		long end;
		if(i == threadNum - 1){
			end = threadDownloadSize + conn.getContentLength() % threadNum;
		} else {
			end = threadDownloadSize;
		}
		DownLoadThred dt = new DownLoadThred(url,i*threadDownloadSize,i*threadDownloadSize+end,i,file);
		dt.start();
	}
{% endhighlight %}