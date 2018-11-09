---
title: Android里面的几种倒计时方式
date: 2017-02-24 21:40:24
tags: [Android]
categories: Android
---


Android常见的一些及时方式:TimeTask,CounterDownTimer,Handler.postDelay().a

1. TimeTask 

		
		
		final Handler handler = new Handler() {
	        public void handleMessage(Message msg) {
	            switch (msg.what) {
	                case 1:
	                    if (timerCount > 0) {
	                        btnGetVerifyCode.setEnabled(false);
	                        btnGetVerifyCode.setText("重新获取(" + String.valueOf(timerCount) + "s)");
	                        timerCount--;
	                    } else {
	                        btnGetVerifyCode.setEnabled(true);
	                        btnGetVerifyCode.setText("重新获取");
	                        if (task != null) {
	                            task.cancel();
	                        }
	                    }
	                    break;
	            }
	            super.handleMessage(msg);
	        }
   		 };

	
		class MyTimerTask extends TimerTask {
	
	        @Override
	        public void run() {
	            Message message = new Message();
	            message.what = 1;
	            handler.sendMessage(message);
	        }
	    }
	    
   		 timerCount = 60;
         task = new MyTimerTask();
         timer.schedule(task, 0, 1000); //延时1000ms后执行，1000ms执行一次
         
         
 
2. CountDownTimer

		new CountDownTimer(60000, 1000) {
	
	    public void onTick(long millisUntilFinished) {
	        mTextField.setText("seconds remaining: " + millisUntilFinished / 1000);
	     }
	
	     public void onFinish() {
	         mTextField.setText("done!");
	     }
	  	}.start();

3. 不做详细说明了