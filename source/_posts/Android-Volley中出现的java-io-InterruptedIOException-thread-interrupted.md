---
title: Android-'Volley中出现的java.io.InterruptedIOException: thread interrupted'
date: 2017-04-08 21:22:45
tags: [Android,BUG修复]
categories: Android
---

最近碰到一个很奇怪的现象，就是我们的app在启东的时候第一次请求接口的时候很容易出现请求失败的现象，尤其是在清理掉缓存重新安装的时候。由于这个接口是访问的localhost，而这个localhost是我们自己在本地启用的一个服务。所以导致了我一开始就走进了一个死牛角尖，怀疑是不是这个服务没有运行起来，导致无法访问本地服务器，钻了死角。

后面通过查找资料发现，Volley在使用的时候通过RequestQueue添加一个Request是后如果再次调用了RequestQueue的start()方法就会出现这种问题。[参考资料](http://stackoverflow.com/questions/31158858/random-com-android-volley-noconnection-error-java-io-interruptedioexception-st)，来自stackoverflow。

<!-- more -->


	public static RequestQueue newRequestQueue(Context context, HttpStack stack) {
	    File cacheDir = new File(context.getCacheDir(), DEFAULT_CACHE_DIR);
	
	    String userAgent = "volley/0";
	    try {
	        String packageName = context.getPackageName();
	        PackageInfo info = context.getPackageManager().getPackageInfo(packageName, 0);
	        userAgent = packageName + "/" + info.versionCode;
	    } catch (NameNotFoundException e) {
	    }
	
	    if (stack == null) {
	        if (Build.VERSION.SDK_INT >= 9) {
	            stack = new HurlStack();
	        } else {
	            // Prior to Gingerbread, HttpUrlConnection was unreliable.
	            // See: http://android-developers.blogspot.com/2011/09/androids-http-clients.html
	            stack = new HttpClientStack(AndroidHttpClient.newInstance(userAgent));
	        }
	    }
	
	    Network network = new BasicNetwork(stack);
	
	    RequestQueue queue = new RequestQueue(new DiskBasedCache(cacheDir), network);
	    queue.start();
	
	    return queue;	
	}


​	
当使用RequestQueue的Start()方法的时候，Volley会调用stop方法确保所有的请求都是停止状态。
​		
	public void stop() {
	    if (mCacheDispatcher != null) {
	        mCacheDispatcher.quit();
	    }
	    for (int i = 0; i < mDispatchers.length; i++) {
	        if (mDispatchers[i] != null) {
	            mDispatchers[i].quit();
	        }
	    }
	}		


​	
在stop()方法中调用了quit()方法：

	public void stop() {
	    if (mCacheDispatcher != null) {
	        mCacheDispatcher.quit();
	    }
	    for (int i = 0; i < mDispatchers.length; i++) {
	        if (mDispatchers[i] != null) {
	            mDispatchers[i].quit();
	        }
	    }
	}	


​	
最终我们来到了这里，这就是我们为什么会出现thread被interrupte的原因，所以我们在使用RequestQueue的时候是不能调用Start()方法的。

	public void interrupt() {
	    // Interrupt this thread before running actions so that other
	    // threads that observe the interrupt as a result of an action
	    // will see that this thread is in the interrupted state.
	    nativeInterrupt();
	
	    synchronized (interruptActions) {
	        for (int i = interruptActions.size() - 1; i >= 0; i--) {
	            interruptActions.get(i).run();
	        }
	    }
	}	