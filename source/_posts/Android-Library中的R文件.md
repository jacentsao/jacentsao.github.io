---
title: Android Library中的R文件不是常量
date: 2017-01-18 19:59:40
tags:
categories: Android
---
前段时间跟项目老大提了个请求,就是将[Butterknife](https://github.com/JakeWharton/butterknife)加入到我们的项目中,结合[android-butterknife-zelezny](https://github.com/avast/android-butterknife-zelezny)使用,这样子不仅代码简洁,也可以减少一些findViewById的繁琐过程,提升我们的开发效率.效果如下图,是不是特别简洁.

![MacDown Screenshot](https://github.com/avast/android-butterknife-zelezny/raw/master/img/zelezny_animated.gif)
<!-- more -->
经过老大的同意以后,我开始加依赖,下载自动注解插件,进行自动生成代码,一气呵成,神清气爽.But问题立马就暴露出来了,代码如下:

	  
		  @OnClick({R2.id.m_button_pattern, R2.id.m_button_password})
	   	   public void onClick(View view) {
	        Log.d("xx", "========onClick=========");
	        Intent intent = new Intent();
	        switch (view.getId()) {
	            case R2.id.m_button_pattern:
	                intent.setClass(getApplicationContext(),MainActivity.class);
	                Log.d("xx", "========onClick====MainActivity=====");
	                break;
	            case R2.id.m_button_password:
	                intent.setClass(getApplicationContext(),SetPasswordLockActivity.class);
	                Log.d("xx", "========onClick=====SetPasswordLockActivity====");
	                break;
	        }
	        this.startActivity(intent);
	    }
    
然后问题就出现了.由于我们的项目是在Library中开发(蛋疼得不行,很多限制),问题就是出在这里,请看下面的代码:

			//Library中生成的R文件
			public static int activity_open_exit=0x7f04000d;
			//Module中生成的R文件
			public static final int abc_fade_out=0x7f050001;
	
所以在Library中无法使用switch语句进而影响到了Butterknife的使用.

至于为什么在Library中无法使用参考[谷歌文档](http://tools.android.com/tips/non-constant-fields).经过阅读我们知道从ADT14开始Library中的R文件才从静态常量变为非常量.因为如果在多个Library中可能出现id冲突的问题.在ADT14以前则采用的是将所有的资源文件和相关的代码重新随着主项目一起重新编译,导致编译速度过慢.因此,从ADT14开始就变成了非常量的id了:


			int id = view.getId();
			switch (id) {
			    case R.id.button1:
			        action1();
			        break;
			    case R.id.button2:
			        action2();
			        break;
			    case R.id.button3:
			        action3();
			        break;
			}

Android Studio也提供了一键转换的快捷方式如下图:

![ConvertSwitch](http://tools.android.com/_/rsrc/1319062860174/tips/non-constant-fields/convert2.png)

然后就变成了如下所示的代码了.


			int id = view.getId();
			if (id == R.id.button1) {
			    action1();
			} else if (id == R.id.button2) {
			    action2();
			} else if (id == R.id.button3) {
			    action3();
			}

 因为找资料的原因又去看了下Github的Butterknife, JakeWharton大神终于在大家的强烈请求下更新了支持Library, [更新日志](https://github.com/JakeWharton/butterknife/blob/master/CHANGELOG.md);
 
 
		Version 8.2.0 (2016-07-10)
		
		New: Support for library projects. Requires application of a Butter Knife Gradle plugin. See README for details.
		New: Generated code now emits R references instead of raw integer IDs.
		Fix: @OnPageChange listener binding now uses the 'add'/'remove' methods on ViewPager instead of 'set'.

算是意料之外的惊喜吧,下周一就把Butterknife加入我们的项目!