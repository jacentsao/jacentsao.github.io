---
title: Android - 性能优化第五季
date: 2017-09-12 22:13:01
tags: [Android, Performance]
categories: Android
---

#### Android Performance Pattern Season 5

##### 1. Thread Performance

The main process(UI Thread) does this:

* System Events
* Input Events
* Application
* Service
* Alarm
* UI Drawing

If there is a large chunk of work between an input event and its callback, then the user will have to wait a longer than expected to see results. If render is not finished in 16ms(60frame/s), and it will cause dropped frame. 

So android provide lot of solution to this heavy task:

* AsyncTask support serial single progress and parallel on a pool of threads managed by AsyncTask
* HandlerThread
* ThreadPool