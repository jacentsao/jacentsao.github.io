---
title: Android-拷贝文件Buff-Size最佳实践
date: 2017-01-18 22:25:42
tags: Android
categories: Android
---
因为在我们的项目里面有一个大小超过11M的assets[可执行文件](http://stackoverflow.com/questions/5583487/hosting-an-executable-within-android-application),所以在app初次安装或者版本升级的时候需要全新拷贝或者升级拷贝.在实际使用中发现在app覆盖安装的时候重新拷贝assets文件很容易出现拷贝不完整的问题,这会导致这个可执行文件运行出现错误,最直观的表现就是界面数据展示异常,且只能通过卸载重装或者清除数据重新生成这个可执行文件解决异常,所以后来我就进行了一些优化.
<!-- more -->

##### 1.加入文件完整性校验
最开始的时候首先想到的是对文件进行完整性校验,即通过文件的MD5值进行对比,观察生成的完整的可执行文件的MD5值是否发生了改变来进行判断.如果MD5值出现改变则提示用户初始化失败,强制关闭app并且重新生成可执行文件.这里有一点补充的是之所以有这个分割成1M文件是因为**在Android2.3之前单个assets文件大小不能超过1M**,[参考资料](http://ponystyle.com/blog/2010/03/26/dealing-with-asset-compression-in-android-apps/)看这里. 拷贝代码例子如下,当然在我们的项目中还应该有一个分割和合并的过程,具体的代码就不再这里详细的写出来了: 

	File Path = Ctxt.getDir("Data", 0);
	File DBFile = new File(Path, "database.db");
	
	if(!DBFile.exists() || DatabaseNeedsUpgrade)  //Need to copy...
	    CopyDatabase(Ctxt, DBFile);


​	
	static private void CopyDatabase(Context Ctxt, File DBFile) throws IOException
	{
	    AssetManager assets = Ctxt.getAssets();
	    OutputStream outstream = new FileOutputStream(DBFile);
	    DBFile.createNewFile();
	    byte []b = new byte[1024];
	    int i, r;
	    String []assetfiles = assets.list("");
	    Arrays.sort(assetfiles);
	    for(i=1;i<10;i++) //I have definitely less than 10 files; you might have more
	    {
	        String partname = String.format("%d.db", i);
	        if(Arrays.binarySearch(assetfiles, partname) < 0) //No such file in assets - time to quit the loop
	            break;
	        InputStream instream = assets.open(partname);
	        while((r = instream.read(b)) != -1)
	            outstream.write(b, 0, r);
	        instream.close();
	    }
	    outstream.close();
	}

##### 2.考虑不分割文件
前面有提到其实只有在Android2.3之前才有assets单个文件大小不能超过1M的限制,而拷贝过程通常都是在进行到其中的某个文件时就失败了,所以考虑不分割文件直接进行拷贝.在实际测试中发现并没有什么大的改善,甚至于拷贝速度还下降了.

##### 3.从拷贝的过程入手
通常我们拷贝文件的时候都会使用int数组进行缓存加速拷贝过程,但是这个缓存的大小究竟要设置为多大比较合适可能大家都没怎么想过.正常来说在设备允许的情况下当然是越大越好,但实际使用过程中,我们也要考虑增大缓存带来的收益.通过测试和查阅[资料](http://stackoverflow.com/questions/10143731/android-optimal-buffer-size)发现,在使用32K缓存大小的情况下收益是最好的.大幅度的提升了拷贝速度,后面我们基本上没有遇到过由于文件拷贝不完整导致的启动异常问题.如下表所示,对应不同缓存拷贝20M文件的耗时:

​		

                   128     256     512     1K      2K      4K      8K      16K     32K     64K     128K    256K    512K    1M      2M      4M      8M      16M
    Galaxy S       4047    3060    269     155     100     65      64      52      51      45      47      50      49      43      44      46      45      58
    Optimus LTE    1178    617     322     172     101     65      47      42      41      35      36      39      44      61      56      51      72      60
    HTC EVO        3971    1884    941     480     251     141     95      69      56      50      48      55      50      49      48      48      48      47         
    Galaxy S2      750     383     210     123     74      50      41      37      35      34      34      37      39      44      46      44      45      44
    Galaxy Nexus   2272    1216    659     341     187     108     70      52      41      38      38      45      44      54      56      66      68      58
    Galaxy Note    1549    799     404     220     127     75      58      54      52      56      52      45      44      62      43      39      44      46

可以发现,32K缓存拷贝文件的收益是极高的,对手机内存的压力也比较小.