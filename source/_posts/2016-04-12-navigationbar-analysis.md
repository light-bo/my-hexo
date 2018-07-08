---
title: "说说导航栏(UINavigationBar)"
date: 2016-08-13 15:50:01
tags:
	- iOS
---

### **你真的知道导航栏的组成么？**
<!--more-->
<div  align="center"> 
	<img src="http://7xskiu.com2.z0.glb.clouddn.com/navigationbar_component.png" width = "360" height = "300" align=center />
</div>

从上面的视图我们可以看到，我们非常“熟悉”的导航栏其实是由六个部分组成的，这六个部分从左到右分别为:  

![](http://7xskiu.com2.z0.glb.clouddn.com/component_names.png)

很显然，当我们需要设置导导航栏的背景颜色的时候，仅仅设置 backgroundColor 是不行的，导航栏的背景色由 _UIBackdropView 及其子视图决定，至于如何设置导航栏的背景颜色，本文不讨论。  
 
---
### **认识 UINavigationBar 的 translucent 属性**
translucent 与其字面含义一样，设置的是 navigationbar 的透明度，改变该属性的时候，除了对透明度的影响外，还有一点值得注意的，就是该属性的改变，会改变到当前 UIViewcontroller 的根视图（self.view）相对于屏幕的位置，如下图所示：
<div  align="center"> 
	<img src="http://7xskiu.com2.z0.glb.clouddn.com/translucent_yes.png" width = "360" height = "300" align=center />
</div>  
 

translucent 设置为 YES 的情况
<div  align="center"> 
	<img src="http://7xskiu.com2.z0.glb.clouddn.com/translucent_no.png" width = "360" height = "300" align=center />
</div>  

translucent 设置为 NO 的情况  
  
  从上面的两个图我们可以看出，当 translucent 设置为 YES 的时候，UIViewcontroller 的根视图会铺满整个整个屏幕，而当 translucent 为 NO 的时候， UIViewcontroller 的根视图则会从导航栏底部开始铺满整个屏幕，其实这也是很容易理解的，当 translucent 为 YES 的时候，导航栏是"透明"的，这时 UIViewcontroller 的根视图铺满整个屏幕是可以的，因为导航栏是“透明”的，我们还是可以看到导航栏下“隐隐约约”的根视图，但当导航栏不是“透明”的时候，显然导航栏底部就不应该有根视图的一部分了，我想，这就是之所以如此设计的原因吧。
  
---  
### 致谢
谢谢您的阅读，欢迎交流和指正。
