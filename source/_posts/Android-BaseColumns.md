---
title: Android-BaseColumns
date: 2017-02-14 22:44:07
tags: Android
categories: Android
---

准备开始看Google官方的Android Architecture系列了，一方面是提升代码质量，另一方面是学习一下新的app架构。由于目前项目的代码基本上技术可以归属到上古遗迹之中，所以很有必要学习一下新的东西为未来做准备。

<!-- more -->
首先看到的就是在数据库操作方面的一个接口，如下所示：


		package android.provider;

		public interface BaseColumns
		{
		    /**
		     * The unique ID for a row.
		     * <P>Type: INTEGER (long)</P>
		     */
		    public static final String _ID = "_id";
		
		    /**
		     * The count of rows in a directory.
		     * <P>Type: INTEGER</P>
		     */
		    public static final String _COUNT = "_count";
		}
		
		
这个类主要是配合SQLiteOpenHelper进行Android的数据库管理，_ID做为主键，而_COUNT可以返回数据库行数。典型应用如下所示：

	public final class TasksPersistenceContract {

    // To prevent someone from accidentally instantiating the contract class,
    // give it an empty constructor.
    private TasksPersistenceContract() {}

    /* Inner class that defines the table contents */
    public static abstract class TaskEntry implements BaseColumns {
        public static final String TABLE_NAME = "task";
        public static final String COLUMN_NAME_ENTRY_ID = "entryid";
        public static final String COLUMN_NAME_TITLE = "title";
        public static final String COLUMN_NAME_DESCRIPTION = "description";
        public static final String COLUMN_NAME_COMPLETED = "completed";
    }
	}
	
	
	//在SQLiteOpenHelper中创建表的语句
	   private static final String SQL_CREATE_ENTRIES =
            "CREATE TABLE " + TasksPersistenceContract.TaskEntry.TABLE_NAME + " (" +
                    TasksPersistenceContract.TaskEntry._ID + TEXT_TYPE + " PRIMARY KEY," +
                    TasksPersistenceContract.TaskEntry.COLUMN_NAME_ENTRY_ID + TEXT_TYPE + COMMA_SEP +
                    TasksPersistenceContract.TaskEntry.COLUMN_NAME_TITLE + TEXT_TYPE + COMMA_SEP +
                    TasksPersistenceContract.TaskEntry.COLUMN_NAME_DESCRIPTION + TEXT_TYPE + COMMA_SEP +
                    TasksPersistenceContract.TaskEntry.COLUMN_NAME_COMPLETED + BOOLEAN_TYPE +
            " )";
	