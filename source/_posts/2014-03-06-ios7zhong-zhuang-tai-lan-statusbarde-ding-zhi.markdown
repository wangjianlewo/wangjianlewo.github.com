---
layout: post
title: "iOS7中状态栏-StatusBar的定制"
date: 2014-03-06 17:14:01 +0800
comments: true
categories: 
---
####开篇说明:
####在iOS7中，status bar的外观默认依赖UIViewController, 也就是说status bar随UIViewController的不同而不同.即在info.plist中的key:UIViewControllerBasedStatusBarAppearance 的默认值为YES.


###一, 若想全局设置StatusBar的外观需:

1,在项目的Info.plist文件里设置UIViewControllerBasedStatusBarAppearance为NO。即更改为StatusBar的状态不随UIViewController的不同而不同

2,使用该方法设置外观

	[[UIApplication sharedApplication] setStatusBarStyle:UIStatusBarStyleLightContent];
或 隐藏StatusBar 

	[[UIApplication sharedApplication]setStatusBarHidden:YES];

###二,对各个UIViewController单独进行设置需:

1.在项目的Info.plist文件里设置UIViewControllerBasedStatusBarAppearance为YES。(默认即为YES)

2.使用该方法设置外观。

	-(UIStatusBarStyle)preferredStatusBarStyle
	 { return UIStatusBarStyleLightContent;}
UIStatusBarStyleLightContent 为白色文字，深色背景时使用 
 
或 隐藏StatusBar

	-(BOOL)prefersStatusBarHidden { return NO;}
返回NO表示要显示，返回YES将隐藏

###三,需要注意的是:
单独设置的viewController必须为最高级别的viewController,(every top-level view controller in your app (other than a standard UIKit container view controller) ）中调用,如果一个控制器在UINavigationController内，则上述的控制StatusBar的方法没有被调用。

解决方案1: 设置navigationBar 的 barStyle

 	nav.navigationBar.barStyle = UIBarStyleBlack; 
此时StatusBar将会变为白色文字,而默认的UIBarStyleDefault则对应显示UIStatusBarStyleDefault的状态. 

解决方案2:
写一个UINavigationController的category,将顶层的控制器返回

	@interface UINavigationController (StatusBarStyle)
	@end
	
	@implementation UINavigationController (StatusBarStyle)
	-(UIViewController *)childViewControllerForStatusBarStyle {
	    return self.topViewController;
	}
	@end

解决方案3:
写一个UINavigationController的category，将上述设置StatusBar的代码写到分类中(使用UINavgationViewController的ViewController中无需再写)

	@interface UINavigationController (StatusBar)
	@end

	@implementation UINavigationController (StatusBar)
	- (UIStatusBarStyle)preferredStatusBarStyle
	{
	    return UIStatusBarStyleLightContent;
	}

还可返回导航控制器的最后一个控制器的StatusBar状态:

return [[self.viewControllers lastObject] preferredStatusBarStyle];

	- (BOOL)prefersStatusBarHidden {
  	  return NO;
	}
	@end

##总结帖,欢迎拍砖!