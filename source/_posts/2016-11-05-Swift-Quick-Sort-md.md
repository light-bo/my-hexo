---
title: 来个 Swift 的快速排序
date: 2016-11-05 15:47:47
tags: Swift
---

### **说几句**
对于编程来说，基本算法和设计模式这种东西就像武功中的**内功**一样，需要不断的修炼，结合自己的实际开发经验重复地去体味其中的精妙之处，我觉得，当你能够将这些东西真正融会贯通，熟练掌握的时候，你就奠定了成为一个大师的基础了，离成为像**《星球大战》**中的**尤达**这样的大师就不远了。 ^_^，现在给大家来一个 Swift 版本的快排，具体的算法思路就不赘述了，直接上代码。

### **Show the Codes**

``` Swift
/**
 * 需要用 inout 修饰 dataList 参数，因为该方法对 dataList 的元素进行了修改
 */
func quickSortOperation(dataList: inout [Int], low: Int, height: Int) -> Int {
    var tempLow = low
    var tempHeight = height
    
    let key: Int = dataList[tempLow]
    var temp: Int?
    
    while tempLow < tempHeight {
        while tempLow<tempHeight && dataList[tempHeight]>key {
            tempHeight -= 1
        }
        
        temp = dataList[tempLow]
        dataList[tempLow] = dataList[tempHeight]
        dataList[tempHeight] = temp!
        
        while tempLow<tempHeight && dataList[tempLow]<key {
            tempLow += 1
        }
        

        temp = dataList[tempLow]
        dataList[tempLow] = dataList[tempHeight]
        dataList[tempHeight] = temp!
    }
    
    return tempHeight
}


func quickSort(dataList: inout [Int], low: Int, height: Int)  {
    if dataList.count <= 0 {
        return
    }
    
    if low < height {
        let middle = quickSortOperation(dataList: &dataList, low: low, height: height)
        quickSort(dataList: &dataList, low: low, height: middle-1)
        quickSort(dataList: &dataList, low: middle+1, height: height)
    }
}

/**
 *  示例代码
 */
var dataList: [Int] = [1, 5, 8, 2, 0, 4, 9, 3]
print(dataList)

quickSort(dataList: &dataList, low: 0, height: dataList.count-1)

print(dataList)

```

### **运行结果(不截图了)**
终端的输入如下：

[1, 5, 8, 2, 0, 4, 9, 3]  
[0, 1, 2, 3, 4, 5, 8, 9]  
Program ended with exit code: 0


### **致谢**
感谢您的阅读，欢迎批评指正，期待与你的交流。 ^_^


