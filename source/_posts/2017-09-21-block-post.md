---
title: Block， 你为啥要 copy？
date: 2017-09-21 23:55:42
header-img: blog-header.jpeg
tags: iOS
---

# 说几句  
在 **iOS** 面试的时候，一个被问烂的问题就是 **property** 的各种修饰符的使用，这个问题为什么这么受欢迎呢？原因在于，这个问题确实可以很好地考察面试者对 **OC** 内存管理机制的理解程度。其中，有一个问题经常被提到，那就是 **OC** 中的 **block** 属性为什么要使用 **copy** 进行修饰呢？

# block
与很多其他语言一样，**block** 类似于 C 语言中的函数指针，在 **OC** 中代表着一个函数（方法） 对象，那么作为这样一个对象，它的内存是如何管理的呢？理解了它的内存管理，自然就知道为什么要用到相应的修饰符了。

# 堆栈
说到内存管理，就会涉及到堆栈，至于堆和栈相关的内存概念，大家可以简单看下这个资料。

[What is a Memory Heap?](https://stackoverflow.com/questions/2308751/what-is-a-memory-heap)

在 **OC** 中，**block** 在内存的管理中是被当成栈对象来管理的，这意味着如果你想要自己管理 **block** 的生命周期的话，必须将 **block** **copy** 到堆上来管理，所以很明显，对于 **block** 我们应该使用 **copy** 来修饰。

其实，再思考一下，像这样的管理， **OC** 在语言层面上完全可以帮我们做这件事，防止我们犯错误，于是，去详细看了一下官方文档，就有了下面的解释。


# 官方文档
[官方文档](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html#//apple_ref/doc/uid/TP40011210-CH8-SW1) 中对这个问题是这样描述的：   

```  
You should specify copy as the property attribute, because a
block needs to be copied to keep track of its captured state outside
of the original scope. This isn’t something you need to worry about
 when using Automatic Reference Counting, as it will happen
 automatically, but it’s best practice for the property attribute to
 show the resultant behavior.
```
意思就是说如果你是使用 **ARC** 编写代码的话，使用 **strong** 去修饰 **block** 也是没有问题的，因为 **ARC** 会帮你做 **copy** 这个动作，但是为了显示指明程序中的 **copy** 动作，建议还是使用 **copy** 修饰符显式地指出这里的内存行为。
