---
title: "iOS 将时间戳转换为可读字符串"  
date: 2016-08-13 15:30:08
tags:
	- iOS
---
  
  
说几句
====
在平时的开发中，客户端和服务端有时需要进行关于时间方面的通讯，这时，服务端经常是返回给客户端一个时间戳的字段，所谓的时间戳，其实就是一个字符串序列（至于这字符串如何生成，请自行查阅相关资料），这个字符串序列能够唯一地标识某一时刻的时间，但是这个字符串是不具有“可读性”的，我们需要将其转换成“刚刚”，“5 分钟前”等具有人眼“可读性”的字符串。本文主要记录了进行这样的转换的一种方法。

<!-- more-->

有码有真相
====

```
//将时间戳转化为可读字符串
+ (NSString *)formatTimeStamp:(NSString *)timestamp {
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setDateFormat:@"yyyy MM dd HH:mm:ss"];//设置时间显示格式
    [dateFormatter setLocale:[[NSLocale alloc] initWithLocaleIdentifier:@"CN"]];//设置时区

    NSDate *dates = [NSDate dateWithTimeIntervalSince1970:[timestamp floatValue]/1000];
    
    NSTimeInterval late = [dates timeIntervalSince1970];
    
    NSDate* dat = [NSDate dateWithTimeIntervalSinceNow:0];
    NSTimeInterval now = [dat timeIntervalSince1970];

    NSString *timeString = @"";
    
    NSTimeInterval timeInterval = now - late;
    
    if (timeInterval/3600 < 1) {
        //一个小时内
        timeString = [NSString stringWithFormat:@"%f", timeInterval/60];

        NSInteger min = [timeString integerValue];
        if(min <= 0) {
            timeString = @"刚刚";
        } else {
            timeString = [NSString stringWithFormat:@"%d分钟前", min];
        }
    } else if (timeInterval/3600>1 && timeInterval/86400<1) {
        //超过一小时（24 小时内）
        NSTimeInterval cha = now - late;
        int hours = ((int)cha) % (3600 * 24) / 3600;
        timeString = [NSString stringWithFormat:@"%d小时前",hours];
    } else if (timeInterval/86400>1) {
        //超过 24 小时
        NSDateFormatter *dateformatter=[[NSDateFormatter alloc] init];
        [dateformatter setDateFormat:@"YY-MM-dd"];
        timeString = [NSString stringWithFormat:@"%@",[dateformatter stringFromDate:dates]];
    }
    
    return timeString;
}
```
