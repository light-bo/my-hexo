---
title: 让 UITextView 带上 placeholder
date: 2016-08-13 15:43:14
tags:
	- iOS
---

为啥需要这东西
====
在使用 UITextField 这个基本控件的时候，我们都很熟悉其有 placeholder 这个属性，即是一个占位符，我们利用该属性可以在 UITextField 不处于输入状态的时候显示一些提示消息，比如 **“请输入用户名”**，**“必填”** 这样的提示文案。但是 UITextField 这个控件只能够显示单行的文本，一旦我们需要显示多行文本时，我们就需要使用到 UITextView 这个控件，但 UITextView 是不带 placeholder 属性的，本文介绍让 UITextView 带上 placeholder 的一种方法。

<!-- more-->

实现思路
====
* 继承 UITextView，封装成 UITextView 的子类
* 在该子类中监听  **UITextViewTextDidBeginEditingNotification**  和  **UITextViewTextDidEndEditingNotification**  两个通知。
* 添加 **placeholder** 属性，并实现其 set 方法
* 重写 UITextView 的 text 方法

  
  
Show me the code
====
```
//  BLLTextView.h
//
//  Created by light_bo on 15/12/28.
//  Copyright © 2015年 light_bo. All rights reserved.
//

#import <UIKit/UIKit.h>

@interface BLLTextView : UITextView

@property (nonatomic, copy) NSString *placeholder;

@end

```
  
  

```
//
//  BLLTextView.m
//
//  Created by light_bo on 15/12/28.
//  Copyright © 2015年 light_bo. All rights reserved.
//

#import "BLLTextView.h"

@implementation BLLTextView


- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if(self) {
        [self addObserver];
    }
    
    return self;
}

//
//- (void)awakeFromNib {
//    [super awakeFromNib];
//    
//    [self addObserver];
//}


#pragma mark --注册通知
- (void)addObserver {
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(textDidBeginEditing:)
                                                 name:UITextViewTextDidBeginEditingNotification
                                               object:self];
    
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(textDidEndEditing:)
                                                 name:UITextViewTextDidEndEditingNotification
                                               object:self];
}


#pragma mark --UITextField notification
- (void)textDidBeginEditing:(NSNotification *)notification {
    if([super.text isEqualToString:_placeholder]) {
        super.text = @"";
        [super setTextColor:[UIColor blackColor]];
    }
}


- (void)textDidEndEditing:(NSNotification *)notification {
    if(0 == super.text.length) {
        super.text = _placeholder;
        [super setTextColor:[UIColor lightGrayColor]];
    }
    
}

- (void)setPlaceholder:(NSString *)placeholder {
    _placeholder = placeholder;
    [self textDidEndEditing:nil];
}


#pragma mark --重写 text 方法
- (NSString *)text {
    NSString *text = [super text];
    
    if([text isEqualToString:_placeholder]) {
        return @"";
    }
    
    return text;
}


#pragma mark --dealloc
- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}



@end


```

