---
title: "读取 json 文件中的数据"
date: 2016-08-13 15:54:41
tags:
	- iOS
---


说几句
====
在 iOS 客户端的开发中，某些需求我们需要使用到一些数量储存量不大，数据内容相对比较固定的数据，比如商品的类型，书籍的类型等，这时我们可以本地维护一个 plist 文件，也可以利用一个 JSON 格式的文本文件进行数据的本地存储，考虑到 plist 文件是 iOS 平台特有的数据格式，为了方便与 Android 平台统一，采用 JSON 文件（JSON 格式的文本文件） 则更为合适。

<!--more-->

JSON 文本文件 sample
====

```
[ 
  {"code": "HK", "en": "Hong Kong", "cn": "香港"},  
  {"code": "TW", "en": "Taiwan", "cn": "台湾"},  
  {"code": "MO", "en": "Macao", "cn": "澳门"},  
  {"code": "US", "en": "United States of America (USA)", "cn": "美国"},  
  {"code": "AR", "en": "Argentina", "cn": "阿根廷"},  
  {"code": "AD", "en": "Andorra", "cn": "安道尔"},  
  {"code": "AE", "en": "United Arab Emirates", "cn": "阿联酋"},  
  {"code": "AF", "en": "Afghanistan", "cn": "阿富汗"},  
  {"code": "AG", "en": "Antigua & Barbuda", "cn": "安提瓜和巴布达"},  
  {"code": "AI", "en": "Anguilla", "cn": "安圭拉"},  
  {"code": "AL", "en": "Albania", "cn": "阿尔巴尼亚"}
]
```

Show the codes
====

```
//
//  NSArray+JsonFile.h
//  
//
//  Created by light_bo on 15/11/13.
//  Copyright © 2015年 apple. All rights reserved.
//

#import <Foundation/Foundation.h>

@interface NSArray (JsonFile)

+ (NSArray *)arrayWithJsonFile:(NSString *)fileName;

@end

```


```
//
//  NSArray+JsonFile.m
//  
//
//  Created by light_bo on 15/11/13.
//  Copyright © 2015年 apple. All rights reserved.
//

#import "NSArray+JsonFile.h"

@implementation NSArray (JsonFile)

+ (NSArray *)arrayWithJsonFile:(NSString *)fileName {

	//fileName: JSON 格式数据文件名称（注意文件的路径）
    NSString *filePath = [[NSBundle mainBundle] pathForResource:fileName
                                                         ofType:nil];
    
    NSData *jsonData = [NSData dataWithContentsOfFile:filePath];
    
    NSError *error = nil;
    NSArray *objectArray = [NSJSONSerialization JSONObjectWithData:jsonData
                                                           options:NSJSONReadingMutableContainers
                                                             error:&error];
    if(error) {
        NSLog(@"%@", error);
        return nil;
    }
    
    return objectArray;
}

@end

```

从上面的代码中，很显然，其方法是读取文本中的数据存储在 **NSData** 中（字节流），然后利用 **NSJSONSerialization** 这个类相关的方法将这些数据**反序列化**为相对应的数据结构（对象），本文则返回的是 **NSArray** 对象。

致谢
====
感谢您的阅读，欢迎评论和指教。 ^_^
