---
title: Java时间比较
date: 2017-02-13 21:58:02
tags: Android
---

在java中比较两个时间的大小: 

	
		DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
        Date start = df.parse("1991-12-25");
        Date end = df.parse("1990-12-23");
        int result = start.compareTo(end);
        

如果result > 0 说明 start > end; 如果result < 0，则 start < end;如果result = 0,则 start = end;    