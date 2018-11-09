---
title: RecyclerView学习笔记
date: 2017-04-26 19:26:26
tags:	[Android, 基础]
categories: Android
---


####自适应高度

继承LinearLayoutManager，重写onMeasure（）方法：

		if (getItemCount() == 0) {            setMeasuredDimension(0, 0);        } else {            View view = recycler.getViewForPosition(0);            if (view != null) {                measureChild(view, widthSpec, heightSpec);                int measuredWidth = View.MeasureSpec.getSize(widthSpec);                int measuredHeight = getItemCount() * view.getMeasuredHeight();                setMeasuredDimension(measuredWidth, measuredHeight);            }        }