---
layout: post
title: "ZXingObjC 崩溃问题解决方法"
date: 2014-01-20 14:58:08 +0800
comments: true
categories: 
---
###一,  二维码扫描,有众所周知的zxing,和zbar.
1,zbar for iphone 已经一年没有更新了,都还没有支持ARC.

2,zxing 各个平台都支持,也一直在更新.
但是demo中只提供了ZXingWidgetController来进行解析,
里面解析二维码的代码比较多,从中提取直接对图片解析的代码也比较麻烦. 且项目中我的viewController已经继承了一个CommonViewController,所以如果使用zxing,需要改的地方还有很多.
###二,  在github上翻了翻,找到了ZXingObjC 
链接如下:https://github.com/TheLevelUp/ZXingObjC
评价挺高的,有600多个star. 添加,使用都很方便.

但在显示完扫描界面,跳到其他界面是,不到一秒,程序就会崩溃.崩溃信息如下:

	Thread 7 name:  Dispatch queue: com.zxing.captureQueue
	Thread 7 Crashed:
	0   libsystem_kernel.dylib        	0x3a53a350 __pthread_kill + 8
	1   libsystem_c.dylib             	0x3a4b111e pthread_kill + 54
	2   libsystem_c.dylib             	0x3a4ed96e abort + 90
	3   libsystem_c.dylib             	0x3a486398 free + 356
	4   libobjc.A.dylib               	0x3a0393a8 object_dispose + 16
	5   MyProject                         	0x0013849e -[ZXCapture dealloc] (ZXCapture.m:490)
	6   AVFoundation                  	0x3133fed6 __74-[AVCaptureVideoDataOutput _AVCaptureVideoDataOutput_VideoDataBecameReady]_block_invoke_0 + 294
	7   libdispatch.dylib             	0x3a45411c _dispatch_call_block_and_release + 8
	8   libdispatch.dylib             	0x3a457eca _dispatch_queue_drain$VARIANT$mp + 138
	9   libdispatch.dylib             	0x3a457dbc _dispatch_queue_invoke$VARIANT$mp + 36
	10  libdispatch.dylib             	0x3a45891a _dispatch_root_queue_drain + 182
	11  libdispatch.dylib             	0x3a458abc _dispatch_worker_thread2 + 80
	12  libsystem_c.dylib             	0x3a488a0e _pthread_wqthread + 358
	13  libsystem_c.dylib             	0x3a4888a0 start_wqthread + 4

百度上没有ZXingObjC的任何消息,然后google,发现关于ZXingObjC的问题也没有几个....

后来查看了github上的issue,终于发现遇到相同问题的啦!  在https://github.com/TheLevelUp/ZXingObjC/issues/26
找到了答案:
dismiss之前,先要将capture layer remove 掉:

	[self.capture.layer removeFromSuperlayer];
	[self.capture stop];
	[self dismissViewControllerAnimated:YES completion:nil];

完美解决! ^_^
#####Tips: 以后用第三库遇到问题时,不要百度,也先不要google,可以先去github的第三方库的链接地址,查看issue. 

#####很多问题,你会遇到,别人也会遇到,有人提出,就有人帮忙解答! &hearts;&hearts;&hearts;
