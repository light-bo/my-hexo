---
title: JSONModel 的基本使用
date: 2016-08-12 23:52:17
tags:
	- iOS
---

前言
====
接触 ios 开发有4个多月了，在这期间，做了一个小型的项目，也参与到公司项目的开发之中，也算是正式地成为一个 ios 开发者了。今天，偶然间接触到了 JSONModal 这个开源库之后，心里的感受只能用“相见恨晚”来诠释。 JSONModal 让我们对服务器 JSON 数据的解析变得如此简单，优雅。我们只需向服务器请求数据，然后几乎不需要做什么工作就可以得到一个我们想要的 modal，这是一件多么幸福的事情啊！本文对JSONModal 的使用方法的介绍主要参考 [JSONModal][1] 库的 markdown 文件。
<!-- more -->

简介
====
JSONModel 是一个能够让你根据获取到的服务器数据快速而智能地建立出一个数据模型（modal）的开源库。你可以在 ios 和 OSX 程序中使用该库。
JSONModel 能够自动地将你获取到 json 数据映射到你的 modal 中，从而减少你对这些数据进行解析的繁杂而不优雅的代码（表示没用该库之前一个对JSON数据一个一个进行解析的感觉好痛苦啊）。

![JSONModel](http://7xskiu.com1.z0.glb.clouddn.com/JSONModel.png)

添加 JSONModal 到你的项目中
====
环境要求
----

- ARC内存管理
- iOS 5.0+ / OSX 10.7+
- SystemConfiguration.framework





获取 JSONModal
====

两种方法：
直接使用源文件
----
1. 到 github 直接将 JSONModal 整个源文件下载下来
2. 添加 JSONModel 整个文件夹到你的项目当中
3. 添加依赖库SystemConfiguration.framework

利用Cocoa pods
----
在 Podfile 中添加如下语句：
	

```
pod 'JSONModel'
```

关于 Cocoa pods 的使用请自行搜索相关的资料




基本用法
====
假设你获取到的 JSON 数据如下所示：

```
	{"id":"10", "country":"Germany", "dialCode": 49, "isInEurope":true}
```

- 创建一个类作为你的 modal 类，并且使得该类继承 JSONModel 类
- 在该类的头文件中声明各个属性，属性的名称应该与 json 数据


```objective-c
#import "JSONModel.h"

@interface CountryModel : JSONModel

@property (assign, nonatomic) int id;
@property (strong, nonatomic) NSString* country;
@property (strong, nonatomic) NSString* dialCode;
@property (assign, nonatomic) BOOL isInEurope;

@end
```

在 .m 文件中无需写任何的代码 

- 初始化数据

```
#import "CountryModel.h"

NSString* json = (fetch here JSON from Internet) ... 

NSError* err = nil;

CountryModel* country = [[CountryModel alloc] initWithString:json error:&err];
```
细心的你可能会发现，我们在 CountryModel 类中所声明的属性的类型与 json 数据中相关的类型是不同的，这样真的可以么？是的， JSONModal 就是这样智能，你要做的只是在你的 CountryModel 类中声明你想要的属性类型就可以了。
以下列出各种情况下的一些示例代码




示例代码
====
1. 简单的映射(获取到的数据是一个简单的字典)
----

```objective-c
{
  "id": "123",
  "name": "Product name",
  "price": 12.95
}
```

```objective-c
@interface ProductModel : JSONModel
@property (assign, nonatomic) int id;
@property (strong, nonatomic) NSString* name;
@property (assign, nonatomic) float price;
@end

@implementation ProductModel
@end
```

2. model 之中嵌套了 model 的情况
----
```objective-c
{
  "order_id": 104,
  "total_price": 13.45,
  "product" : {
    "id": "123",
    "name": "Product name",
    "price": 12.95
  }
}
```

```objective-c
@interface OrderModel : JSONModel
@property (assign, nonatomic) int order_id;
@property (assign, nonatomic) float total_price;
@property (strong, nonatomic) ProductModel* product;//直接声明一个product 所对应的对象即可
@end

@implementation OrderModel
@end
```

3. 内嵌model并且内部model拥有多个元素（数组）
----

```objective-c
{
  "order_id": 104,
  "total_price": 103.45,
  "products" : [
    {
      "id": "123",
      "name": "Product #1",
      "price": 12.95
    },
    {
      "id": "137",
      "name": "Product #2",
      "price": 82.95
    }
  ]
}
```

```objective-c
@protocol ProductModel
@end

@interface ProductModel : JSONModel
@property (assign, nonatomic) int id;
@property (strong, nonatomic) NSString* name;
@property (assign, nonatomic) float price;
@end

@implementation ProductModel
@end

@interface OrderModel : JSONModel
@property (assign, nonatomic) int order_id;
@property (assign, nonatomic) float total_price;
@property (strong, nonatomic) NSArray<ProductModel>* products;
@end

@implementation OrderModel
@end
```


4. model 多层嵌套(3 层以上)
----

当modal的嵌套在 3 层以上的时候，就需要在 keyMapper 函数中对modal的属性和 json的键值之间做一个映射。
```objective-c
{
  "order_id": 104,
  "order_details" : [
    {
      "name": "Product#1",
      "price": {
        "usd": 12.95
      }
    }
  ]
}

```

```objective-c
@interface OrderModel : JSONModel
@property (assign, nonatomic) int id;
@property (assign, nonatomic) float price;
@property (strong, nonatomic) NSString* productName;
@end

@implementation OrderModel

+(JSONKeyMapper*)keyMapper
{
  return [[JSONKeyMapper alloc] initWithDictionary:@{
    @"order_id": @"id",
    @"order_details.name": @"productName",
    @"order_details.price.usd": @"price"
  }];
}

@end

```

当你的程序中有多个 modal 具有相同的属性名，并且需要映射层别的名字的时候，可以使用 JSONModel 的全局映射，这样，整个 App 中所有的 modal 中相应的字段都会映射到指定的名字。

```objective-c
[JSONModel setGlobalKeyMapper:[
    [JSONKeyMapper alloc] initWithDictionary:@{
      @"item_id":@"ID",
      @"item.name": @"itemName"
   }]
];

```


5. 下滑线的命名映射到驼峰式的命名  
----

``` objective-c
{
  "order_id": 104,
  "order_product" : @"Product#1",
  "order_price" : 12.95
}
```

```objective-c
@interface OrderModel : JSONModel

@property (assign, nonatomic) int orderId;
@property (assign, nonatomic) float orderPrice;
@property (strong, nonatomic) NSString* orderProduct;

@end

@implementation OrderModel

+(JSONKeyMapper*)keyMapper
{
  return [JSONKeyMapper mapperFromUnderscoreCaseToCamelCase];
}

@end
```
6. 可选属性
----
在实际的开发过程中，经常遇到这样的情况，从服务器获取到 json 数据中有某个数据的值为 null，或者获取到的 json 数据中缺少 model 中的某个或几个属性， 在这两种情况下， JSONModal 都会发生解析错误。
为了避免这种错误，需要在 model 具体的字段上将属性声明为可选属性。

```objective-c
{
  "id": "123",
  "name": null,
  "price": 12.95
}
```

```objective-c

@interface ProductModel : JSONModel
@property (assign, nonatomic) int id;
@property (strong, nonatomic) NSString<Optional>* name;
@property (assign, nonatomic) float price;
@property (strong, nonatomic) NSNumber<Optional>* uuid;
@end

@implementation ProductModel
@end
```

7. 忽略相关属性
----
在开发的过程中，有时我们需要 JSONModal 忽略我们自定义的 model 中得某些属性，这时需要将相关的属性声明为 Ignore

```objective-c
{
  "id": "123",
  "name": null
}
```

```objective-c
@interface ProductModel : JSONModel
@property (assign, nonatomic) int id;
@property (strong, nonatomic) NSString<Ignore>* customProperty;
@end

@implementation ProductModel
@end
```

8. 设置 model 的所有的属性为可选的属性
----

JSONModel 提供了一种设置 model 中所有的属性全部为可选的方法，具体如下：

```
@implementation ProductModel
+(BOOL)propertyIsOptional:(NSString*)propertyName
{
  return YES;
}
@end
```

9. 惰性解析转化
----

为了提高程序的效率，我们在程序中经常会采用“懒加载”的方法，即使用一个对象的时候，不要创建该对象，而是等到要用到该对象的时候才对对象进行创建。这里用到的思路是一样的，即等到要用到 model 中某个属性的时候，才对 json 数据进行解析并映射到 model 的属性中以供使用。

```
{
  "order_id": 104,
  "total_price": 103.45,
  "products" : [
    {
      "id": "123",
      "name": "Product #1",
      "price": 12.95
    },
    {
      "id": "137",
      "name": "Product #2",
      "price": 82.95
    }
  ]
}
```


```
@protocol ProductModel
@end

@interface ProductModel : JSONModel
@property (assign, nonatomic) int id;
@property (strong, nonatomic) NSString* name;
@property (assign, nonatomic) float price;
@end

@implementation ProductModel
@end

@interface OrderModel : JSONModel
@property (assign, nonatomic) int order_id;
@property (assign, nonatomic) float total_price;
@property (strong, nonatomic) NSArray<ProductModel, ConvertOnDemand>* products;//添加ConvertOnDemand
@end

@implementation OrderModel
@end

```

写在最后
----
谢谢您的阅读，如果有什么意见和建议，欢迎交流！



[1]: https://github.com/icanzilb/JSONModel
