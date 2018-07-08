---
title: "iOS 多个 tab 页 demo"
date: 2016-08-13 15:34:30
tags:
	- iOS
---


简介
====
在日常的开发中，App 中多个功能模块常常表现为多个 tab 页的形式，利用 tab 页，可以使得 App 的功能模块非常清晰，同时又非常有利于团队的协作开发。本文记录了这样一个 demo 的示例代码。

<!-- more-->

代码
====
```
/**
 * the codes support iOS 7.0 and later iOS version.
 *
 */ 


- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];

	UITabBarController *tabBarController = [[UITabBarController alloc] init];

	UIImage *unselecteImage = [[UIImage imageNamed:@"01"] imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];//采用图片的原始格式，系统不对图片进行处理

	UIImage *selectedImage = [[UIImage imageNamed:@"02"] imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];

	ViewController *vc1 = [[ViewController alloc] init];
	vc1.tabBarItem = [[UITabBarItem alloc] initWithTitle:@"微信" image:unselecteImage selectedImage:selectedImage];
	//...

	ViewControllerTwo *vc2 = [[ViewControllerTwo alloc] init];
	vc2.title = @"通讯录";
	//设置选中和未选中的图片...

	ViewControllerThree *vc3 = [[ViewControllerThree alloc] init];
	vc3.title = @"发现";
	//...

	ViewControllerFour *vc4 = [[ViewControllerFour alloc] init];
	vc4.title = @"我";
	//...

	NSArray *viewControllers = [NSArray arrayWithObjects:vc1, vc2, vc3, vc4, nil];
	tabBarController.viewControllers = viewControllers;

	UINavigationController *nc = [[UINavigationController alloc] initWithRootViewController:tabBarController];


	self.window.rootViewController = nc;
	[self.window setTintColor:[UIColor greenColor]];


	[self.window makeKeyAndVisible];

	return YES;
}

```

demo 截图
===
![这里写图片描述](http://img.blog.csdn.net/20150913133805286)
