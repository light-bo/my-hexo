---
title: 反转二叉树
date: 2016-10-29 16:07:41
tags: Algorithm
---

### **问题描述**
为了更加直观地描述翻转二叉树的问题，我们直接使用下图对问题进行描述：
<div align="center">
	<img src="http://7xskiu.com1.z0.glb.clouddn.com/binaryTreeReverse"/align=center>
</div>

从图中我们可以看出，二叉树翻转后的结果就如同原来二叉树在镜子里的 “像” 一样，这就是这个问题所要达到的效果。



---  
### **算法原理**
 在数据结构中，对于树这种结构，我们经常采用的策略是使用递归的方式，在这里，我们也使用递归来解决这个问题。

递归算法步骤：  

	1. 对二叉树的左子树进行翻转   
	2. 对二叉树的右子树进行翻转  
	3. 将左右子树的根节点进行交换（不用考虑原二叉树的根节点，因为原二叉树的根节点在翻转前后没有改变）

	
---
### **Show the codes (C)**
* 树节点定义

```
	struct BinaryTreeNode {
		char val;
		BinaryTreeNode *lchild;
		BinaryTreeNode *rchild;
	};
	
	typedef BinaryTreeNode *BinaryTree;
```


* 递归代码

```
BinaryTree InvertBinaryTree(BinaryTree &tree) {
	if(NULL == tree) return NULL;
	
	tree->lchild = InvertBinaryTree(tree->lchild);
	tree->rchild = InvertBinaryTree(tree->rchild);
	
	BinaryTree *tempTree = tree->lchild;
	tree->lchild = tree->rchild;
	tree->rchild = tempTree;
	
	return tree;
}
```

 
---  
### **致谢**
谢谢您的阅读，有何意见和建议，欢迎交流！^_^
