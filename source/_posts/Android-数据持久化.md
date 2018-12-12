---
title: Android-数据持久化
date: 2017-01-19 16:18:48
tags: Android
categories: Android
---

今天晚上Android项目组的小伙伴一起聊了一下Android数据持久化的几种方式外加一些项目中的总结吧.突然发现一些不怎么用的东西都已经快忘光了,比如说ContentProvider + LoadManger + URIMatcher + CursorAdapter,今天突然提到LoadManager感觉像是一个没接触过的东西,被小伙伴提了一下才记得有这个类的存在,更别提ContentObserver了.有点跑题了还是聊一下Android的数据持久化策略吧!
<!-- more -->
##Android数据持久化的方式
总所周知,Android共有五种数据持久化的方式,这里是[官方文档](https://developer.android.com/guide/topics/data/data-storage.html).通过阅读API Guidance可知,这其中方式分别如下:
###1. SharePreference
通常用于存储一些本地化的配置文件,主要分为读和取,操作如下:

1.写入操作
​		
		// We need an Editor object to make preference changes.
  	    // All objects are from android.context.Context
​		SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
​	    SharedPreferences.Editor editor = settings.edit();
​	    editor.putBoolean("silentMode", mSilentMode);
​	    // Commit the edits!
​	    editor.commit();   	    
​	   	 //finally we can see these file in data/data/packagename/shared_preference if your device has been rooted;   
2.读取操作
​    
		// Restore preferences
		SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
		//false stands for the default value, you can customize yourself
		boolean silent = settings.getBoolean("silentMode", false);

###2. Internal Storage	
第一点提到的SharePreference存储的方式最终存放的位置就是在Internal Storage中

		String FILENAME = "hello_file";
		String string = "hello world!";
		//finally the file path is data/data/packagename/files/hello_file
		//and there are kinds of MODE(MODE_PRIVATE,MODE_APPEND,MODE_WORLD_READABLE,MODE_WORLD_WRITABLE)
		FileOutputStream fos = openFileOutput(FILENAME, Context.MODE_PRIVATE);
		fos.write(string.getBytes());
		fos.close();					


​		
值得注意的是从API 17开MODE\_WORLD\_READABLE跟MODE\_WORLD_WRITABLE已经是deprecated的状态.从API 23开始,由于谷歌收紧了Android系统的权限(Android越来越封闭,苹果越来越开放,目的都是为了体验越来越好!),如果还使用这两个属性会直接抛出异常SecurityException.因此如果你的应用的TargetVersion在API 23,那么如果你想共享应用的内部数据则只能通过主动分享的方式发起共享,详见[参考文档](https://developer.android.com/training/secure-file-sharing/index.html).

正如官方文档所提,如果想要存储静态文件,比如说应用的铃声之类的文件,可以放在项目的res/raw/路径下,通过openRawResource(R.raw.fileId)获取对应的资源文件.

####保存Cache文件
通过getCacheDir()可以返回app内部的cache文件,该目录即data/data/packagename/cache由系统维护,但是谷歌的官方建议是自行维护1MB左右,该目录会随着应用卸载而被清理掉.其他诸如getFileDir(),getDir()会在后文有个总结性的说明.

###3. External Storage
Android的外部存储可以分为可卸载的存储例如SD卡,以及不可卸载的内部存储(Internal Storage).通过USB文件传输模式连接的时候,用户可以对外部存储进行操作.

当用户挂载外部存储或者移除外部存储的时候,应用就无法操作外部存储了.同时外部存储的全局可读特性也决定了它本身并没有安全性可言.

####获取访问外部存储的权限
如果你的应用需要读写外部存储则需要在manifest文件中配置如下权限:
​		
		<manifest ...>
		    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
		    ...
		</manifest>		

如果只需要读取权限只需申明READ\_EXTERNAL\_STORAGE权限即可;如果需要读写权限只需要申明WRITE\_EXTERNAL\_STORAGE权限即可,因为系统会同时声明读取权限.

注意:从Android4.4开始,如果只需要读写app内部的文件,则无需申明读写权限.

####检查是否具备读写权限
无论在什么地方进行外部存储操作的时候你都应该检查外部存储是否处于可用状态.

		/*Ckecks if external storage is available for read and write\*/
		public boolean isExternalStorageAvailable(){
			String state = Environment.getExternalStorageState();
			return Environment.MEDIA_MOUNTED.equals();
		}

通过Environment.getExternalStorageState()可以获得外部存储的状态,如连接到电脑,彻底移除,不恰当的移除等等;此时你可以通过判断外部存储的状态来决定是否需要访问外部存储的媒体文件.如下所示为外置存储的不同状态:


	    public static final String MEDIA_BAD_REMOVAL = "bad_removal";
	    public static final String MEDIA_CHECKING = "checking";
	    public static final String MEDIA_EJECTING = "ejecting";
	    public static final String MEDIA_MOUNTED = "mounted";
	    public static final String MEDIA_MOUNTED_READ_ONLY = "mounted_ro";
	    public static final String MEDIA_NOFS = "nofs";
	    public static final String MEDIA_REMOVED = "removed";
	    public static final String MEDIA_SHARED = "shared";
	    public static final String MEDIA_UNKNOWN = "unknown";
	    public static final String MEDIA_UNMOUNTABLE = "unmountable";
	    public static final String MEDIA_UNMOUNTED = "unmounted";

####保存文件共享给其他应用
主要是公共的多媒体库,例如MUSIC,PICTURE等等,通过以下方式获得

		Environment.getExternalPublicDirectory(String type)

其中type可分为如下几种,对应到外部存储的不同位置.

	    public static String DIRECTORY_ALARMS;
	    public static String DIRECTORY_DCIM;
	    public static String DIRECTORY_DOCUMENTS;
	    public static String DIRECTORY_DOWNLOADS;
	    public static String DIRECTORY_MOVIES;
	    public static String DIRECTORY_MUSIC;
	    public static String DIRECTORY_NOTIFICATIONS;
	    public static String DIRECTORY_PICTURES;
	    public static String DIRECTORY_PODCASTS;
	    public static String DIRECTORY_RINGTONES;


​	    
####保存应用私有文件
如果你的应用有一些私有文件,如音效文件,此时可以在外部存储中创建一个私有的文件目录:
​	
		//if you pass null as the type return the root directory
		//storage/emulated/0/Android/data/packagename/
		//of course you can pass non null type to create a subdirectory
		Context.getExternalFilesDir(String type)    

从Android 4.4开始,读写应用的私有空间的文件并不需要读写权限,因此如果其他地方无需读写权限的情况下可以通过一下方式申明:

		<manifest ...>
			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
					         android:maxSdkVersion="18" />
			...
		</manifest>

这里有个事情需要说明的是,这个应用的私有目录会随着应用的卸载而删除.同时,在媒体库中并不会显示应用的私有文件,因此,如果在你的app中属于用户的文件是不能保存到这个目录的,例如用户购买的音乐.	

有些手机会把内置存储划出来一部分做为外置存储使用(我们现在使用的多数都是这种方式),但是的手机还提供了SD卡卡槽.对于Android 4.3及以下的设备,通过getExternalFilesDir()只能获得内置存储中划出来的那一部分,也就是说并不能获得SD卡部分.从Android4.4开始通过getExternalFilesDir()返回的是一个数组.只有在内置存储划分的外置存储不可用或者占用满的情况下才选择使用SD卡做为应用的私有存储空间.如果想要在Android4.3及以下的设备中获取该路径则通过兼容包ContextCompact.getExternalFilesDirs()获取.

需要注意的是,尽管对于媒体中心(MediaStore)而言,应用外部存储的私有空间是不可见的,但是对于其他具有读取或者写入存储权限的软件而言,放在外部存储的文件是可读可写的.因此,如果你不想你的文件被别的软件使用或者更改的话,就只能放到应用的内部空间了.

####保存缓存文件
通过如下方式获得缓存文件的保存目录,缓存目录会随着应用的卸载而被删除:
​	
	getExternalCacheDir();

跟上面提到的类似,你也可以通过ContextCompact.getExternalCacheDirs()获得SD卡之类的外置存储的缓存路径.

在代码开发的时候我们应该特别注意缓存文件的维护,防止缓存文件占用过多的空间.比如说我们使用一些第三方的框架的时候会让我们配置缓存空间大小.

##Using Databases
Android支持SQLite的所有功能,在应用里面创建的数据库能够被应用的任何一个类访问,但是不能在应用外访问.通常可以配合URIMatcher + ContentProvider + SQLiteOpenHelper进行数据库数据共享,列入联系人的获取.

##Using NetWork Connection
即数据存储在服务端,通过网络获取持久化数据.

##总结
前面基本上是对于官方文档的一种翻译和理解,后面来说一下自己的总结,首先观察一下现象

	 			///storage/emulated/0/Android/data/com.max.testuninstall/cache
	            Log.d(TAG, "onCreate: 外部缓存存储" + this.getExternalCacheDir().toString());
	            //storage/emulated/0/Android/data/com.max.testuninstall/files
	            Log.d(TAG, "onCreate: 外部私有存储" + this.getExternalFilesDir(null).toString());
	            ///storage/emulated/0
	            Log.d(TAG, "onCreate: 外部共用存储" + Environment.getExternalStorageDirectory().toString());
	            ///data/user/0/com.max.testuninstall/cache
	            Log.d(TAG, "onCreate: 内部缓存存储" + this.getCacheDir().toString());
	            ///data/user/0/com.max.testuninstall/files
	            Log.d(TAG, "onCreate: 内部文件存储" + this.getFilesDir().toString());
	            ///data/user/0/com.max.testuninstall/app_null
	            Log.d(TAG, "onCreate: 内部文件存储" + this.getDir(null, MODE_PRIVATE).toString());
	            ///data/user/0/com.max.testuninstall/app_hello_world
	            Log.d(TAG, "onCreate: 内部文件存储" + this.getDir("hello_world", MODE_PRIVATE).toString());
	            ///data/user/0/com.max.testuninstall/files(List)
	            Log.d(TAG, "onCreate: 内部文件存储,返回目录下所有文件" + this.fileList().toString());
	            String FILENAME = "hello_file";
	            String string = "hello world!";
	            //data/data/packagename/files/hello_file
	            FileOutputStream fos = this.openFileOutput(FILENAME, Context.MODE_PRIVATE);
	            fos.write(string.getBytes());
	            fos.close();


​	
以上对应前文所说的通过不同方式获取内部,外部文件及缓存路径.
###读写权限
Internal Storage本身无需声明任何权限即可进行读写操作.External Storage
从Android4.4开始读写应用私有空间无需声明读写权限,Android4.3及以下需要声明读写权限.Android系统中写入External Storage权限包含读取External Storage权限.

###应用卸载	
通过上面对应的日志结合实际操作发现,Context获取的路径在应用卸载的时候会被同时删除,其实这也是可以理解的,毕竟是属于应用私有的文件.通过Environment获得的路径属于外界环境的,所以不会跟随应用的卸载而被删除掉.

###多用户	
在/storage/emulated/目录下面会有 0(默认用户),如果新建一个用户则是10,再新建则是11依此类推.