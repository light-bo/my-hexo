---
title: 仿 Nice 首页图片列表 9 图样式 (iOS)
date: 2017-08-09 21:05:02
tags: UICollectionViewLayout
---
# 说明
本文的代码已经上传到 GitHub 上，有兴趣的童鞋可以到 GitHub 上查看，欢迎沟通交流。

[https://github.com/light-bo/BLLSudokuImageLayout](https://github.com/light-bo/BLLSudokuImageLayout)

# 需要实现的效果
用过 nice app 的童鞋应该比较熟悉，nice 对于图片贴的显示是根据图片数量有 9 种不同的显示样式的，如下图所示：

<div  align="left">
	<img src="/images/iOS/sudoku-1.png" width = "300" height = "300" align=center/>
</div>


<div  align="left">
	<img src="/images/iOS/sudoku-2.png" width = "300" height = "300" align=center />
</div>


<div  align="left">
	<img src="/images/iOS/sudoku-3.png" width = "300" height = "300" align=center />
</div>   


<div  align="left">
	<img src="/images/iOS/sudoku-4.png" width = "300" height = "300" align=center />
</div>


<div  align="left">
	<img src="/images/iOS/sudoku-5.png" width = "300" height = "300" align=center />
</div>


<div  align="left">
	<img src="/images/iOS/sudoku-6.png" width = "300" height = "300" align=center />
</div>


<div  align="left">
	<img src="/images/iOS/sudoku-7.png" width = "300" height = "300" align=center />
</div>


<div  align="left">
	<img src="/images/iOS/sudoku-8.png" width = "300" height = "300" align=center />
</div>



<div  align="left">
	<img src="/images/iOS/sudoku-9.png" width = "300" height = "300" align=center />
</div>

# 如何实现
看到上面这种显示效果，很多人应该很容易就能想到，可以使用 UICollecitonView 来实现，不过这布局仅仅使用 **UIKit** 提供的 UICollectionViewFlowLayout 还是有点难以实现，看来只能借助于 UICollectionViewFlowLayout 来自定义布局了，本文采用的实现方案就是自定义一个 UICollectionViewLayout 类（继承于 UICollectionViewFlowLayout），来达成效果。

# 看看代码
### 自定义 layout 类

```
//BLLCollectionViewImageSudokuLayout.h

//继承自 UICollectionViewFlowLayout
@interface BLLCollectionViewImageSudokuLayout : UICollectionViewFlowLayout

/**
 The intermal of each column.

 每一列的间距
 */
@property (nonatomic, assign) float columnMargin;


/**
 The interval of each row.

 每一行的间距
 */
@property (nonatomic, assign) float rowMargin;


@end

```



自定义布局中根据图片的数量，计算图片布局，这里使用**策略模式**，每一种布局由一个类负责。

```
@implementation BLLCollectionViewImageSudokuLayout


- (instancetype)init {
    self = [super init];
    if (self) {
        _attrsArray = [NSMutableArray new];
    }

    return self;
}


- (BOOL)shouldInvalidateLayoutForBoundsChange:(CGRect)newBounds {
    return YES;
}


- (void)prepareLayout {
    [super prepareLayout];

    //计算所有 cell 的属性
    [self.attrsArray removeAllObjects];
    NSInteger count = [self.collectionView numberOfItemsInSection:0];
    for (int i = 0; i<count; i++) {
        UICollectionViewLayoutAttributes *attrs = [self layoutAttributesForItemAtIndexPath:[NSIndexPath indexPathForItem:i inSection:0]];
        [self.attrsArray addObject:attrs];
    }
}


- (CGSize)collectionViewContentSize {
    return CGSizeMake(self.collectionView.width, self.collectionView.height);
}


- (NSArray *)layoutAttributesForElementsInRect:(CGRect )rect {
    return self.attrsArray;
}


// 根据图片的数量，计算图片布局，这里使用策略模式，每一种布局由一个类负责，分别是 BLLSudokuItemLayoutAttributeAlgorithm1， BLLSudokuItemLayoutAttributeAlgorithm2， ...
- (UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath {
    NSInteger itemAmount = [self.collectionView numberOfItemsInSection:0];

    Class algorithmClass =  NSClassFromString([NSString stringWithFormat:@"BLLSudokuItemLayoutAttributeAlgorithm%zd", itemAmount]);
    BLLSudokuItemLayoutConfigurator *layoutConfigurator = [[BLLSudokuItemLayoutConfigurator alloc] initWithAlgorithmStrategy:[algorithmClass new]];

    return [layoutConfigurator layoutConfigWithCollectionView:self indexPath:indexPath];
}


@end

```

### 布局算法协议
```

@protocol BLLSudokuItemLayoutAttributeAlgorithmProtocol <NSObject>

@required
- (UICollectionViewLayoutAttributes *)layoutConfigWithCollectionView:(BLLCollectionViewImageSudokuLayout *)collectionViewSudokuLayout indexPath:(NSIndexPath *)indexPath;

@end

```

### 布局算法基类

```

//BLLSudokuItemLayoutAttributeAlgorithm
@interface BLLSudokuItemLayoutAttributeAlgorithm : NSObject <BLLSudokuItemLayoutAttributeAlgorithmProtocol>


@end

```


### 具体算法子类
简单地列出图片数量为 4 时，算法子类的实现

```
//BLLSudokuItemLayoutAttributeAlgorithm4
@interface BLLSudokuItemLayoutAttributeAlgorithm4 : BLLSudokuItemLayoutAttributeAlgorithm

@end


@implementation BLLSudokuItemLayoutAttributeAlgorithm4

- (UICollectionViewLayoutAttributes *)layoutConfigWithCollectionView:(BLLCollectionViewImageSudokuLayout *)collectionViewSudokuLayout indexPath:(NSIndexPath *)indexPath {
    float width = collectionViewSudokuLayout.collectionView.width - collectionViewSudokuLayout.sectionInset.left - collectionViewSudokuLayout.sectionInset.right;
    float halfWidth = (width - collectionViewSudokuLayout.columnMargin)/2;
    float height = collectionViewSudokuLayout.collectionView.height - collectionViewSudokuLayout.sectionInset.top - collectionViewSudokuLayout.sectionInset.bottom;
    float halfHeight = (height - collectionViewSudokuLayout.rowMargin) / 2;

    UICollectionViewLayoutAttributes *attrs = [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:indexPath];
    attrs.frame = CGRectMake((indexPath.row % 2) * (halfWidth + collectionViewSudokuLayout.columnMargin), (indexPath.row > 1? 1: 0) * (halfHeight+collectionViewSudokuLayout.rowMargin), halfWidth, halfHeight);

    return attrs;
}

@end

```

### 最后，类似于常规的 UICollectionViewLayout 的使用即可
```
   BLLCollectionViewImageSudokuLayout *collectionViewImageSudokuLayout = [[BLLCollectionViewImageSudokuLayout alloc] init];
    collectionViewImageSudokuLayout.columnMargin = 2;
    collectionViewImageSudokuLayout.rowMargin = 2;

    //yourWidth：整个图片区域的宽度  yourHeight：整个图片区域的高度
    _mainCollectionView = [[UICollectionView alloc] initWithFrame:CGRectMake(0, 0, yourWidth, yourHeight) collectionViewLayout:collectionViewImageSudokuLayout];
    _mainCollectionView.backgroundColor = [UIColor whiteColor];
    _mainCollectionView.delegate = self;
    _mainCollectionView.dataSource = self;
    [_mainCollectionView registerClass:[BLLImageCollectionViewCell class] forCellWithReuseIdentifier:kImageCellId];

    [self addSubview:_mainCollectionView];
```
