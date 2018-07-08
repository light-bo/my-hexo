---
title: 当 Swift 遇上 NSClassFromString
date: 2016-10-12 11:46:09
tags: Swift
---



### NSClassFromString
<!--more-->

```
func NSClassFromString(_ aClassName: String) -> AnyClass?

Description
	Obtains a class by name.
	
Parameters	
	aClassName: The name of a class.
	
Returns	
	The class object named by aClassName, or nil if no class by 
	that name is currently loaded. If aClassName is nil, returns nil.
	
Availability	
	iOS (8.0 and later), macOS (10.10 and later),
	 tvOS (9.0 and later), watchOS (2.0 and later)
```
从文档可以看出，这个 api 其实就是能够根据一个字符串获取该字符串对应的类的 class 对象，也就是说，这个 api 可以让我们通过提供一个类的类名（字符串），然后得到该字符串对应的类的实例。这在我们日常的开发中，在某些场景下，对我们简化代码是挺有用的一个东西。

- - -
### 举个栗子 && Show the codes
每一个 ios app 中，基本都会有一个【设置页】，类似下图：

<div  align="center"> 
	<img src="http://7xskiu.com1.z0.glb.clouddn.com/setting.PNG" width = "250" height = "400" align=center />
</div>

基本上每点击一行，都会跳转到不同的 UIViewController 中，我们很自然的一个想法是在
**func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)** 函数中进行一坨的 if 语句，但这样一点不优雅，现在我们利用上面的 api 改写下。

- 将所有需要跳转的 UIViewController 配置在一个数组中 


```
/*
 * viewController 为要跳转的 UIViewController 的类的名称，
 * constructMethod 表示的是该 UIViewController 的实现方式，code 表示
 * 该 UIViewController 是代码实现的，BLLStoryBoard 表示该 UIViewController 
 * 是由 storyboard 实现的，并且 BLLStoryBoard 是 storyboard 的名称，
 * 这样弄是因为不同方式，跳转的代码不同
 */
 
let controllers = [["viewController": "BLLSnapKitViewController", "constructMethod": "code"],
                       ["viewController": "BLLStoryBoardEntryViewController", "constructMethod": "BLLStoryBoard"] ]
                       
```

- 使用 NSClassFromString 进行跳转

```
   func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        tableView.deselectRow(at: indexPath, animated: true)
        
        if let appName: String = Bundle.main.object(forInfoDictionaryKey: "CFBundleName") as? String {
            
            let configDic:[String: String] = controllers[indexPath.row]
            let vcName = configDic["viewController"]!
            
            //在 swift 下类名前面需要添加 bundle 名称
            let classStringName: String = appName + "." + vcName
            
            let construcMethod: String = configDic["constructMethod"]!
            
            if construcMethod == "code" {
                if let toClass: UIViewController.Type = NSClassFromString(classStringName) as? UIViewController.Type {
                    let toController: UIViewController = toClass.init()
                    self.navigationController?.pushViewController(toController, animated: true)
                }
            } else {
                let storyBoard = UIStoryboard(name: construcMethod, bundle: Bundle.main)
                let vc: UIViewController = storyBoard.instantiateViewController(withIdentifier: vcName)
                 self.navigationController?.pushViewController(vc, animated: true)
            }
        }
    }

```

- - -
### 致谢

感谢您的阅读，欢迎与您的沟通交流 ^_^






