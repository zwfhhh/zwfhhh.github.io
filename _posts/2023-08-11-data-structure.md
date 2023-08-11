---
layout: post
title: 算法之数据结构练习
date: 2023-08-11 09:08:00 +0800
category: Algorithm
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/algorithm.jpg
icon: note
---


* content
{:toc}
# 数据结构练习

## 二叉树

### 如何将==数组转化为二叉树==

在解决一个二叉树问题时候，我们首先需要解决 得问题是如何将一个题目中给出得数组输入过后转化为一个二叉树，之后树建立出来了，才可能进一步去讨论这个二叉树得遍历问题

==数组转化为二叉树==代码如下：

```java
    //定义节点类
    static class TreeNode {
        int value;
        TreeNode left;
        TreeNode right;
        public TreeNode(int a) {
            value = a;
        }
    }


    static TreeNode CreatTree(Integer point[],int index) {//这里得index指的是数组中得索引，也指二叉树中得序号
                                                            //这里得point数组得用Integer,不然null是输入不进去得
        TreeNode treeNode = null;
        if(index<point.length){
            Integer a = point[index];
            if(a==null) {
                return null;
            }else {
                treeNode = new TreeNode(a);//CreatTree是static，所以TreeNode也要static
            }
            treeNode.left =CreatTree(point,index*2+1);//index*2+1是左节点和父节点之间得关系，总结得出得
            treeNode.right = CreatTree(point,index*2+2);
            return treeNode;
        }
        return treeNode;
    }
```

### 二叉树的遍历

#### 先序遍历

```java
statci void PreOrder(TreeNode root) {
    if(root==null) {
        return;
    }
    System.out.println(root.value);
    PreOrder(root.left);
    PreOrder(root.right);
}

```

#### 中序遍历

```java
statci void InOrder(TreeNode root) {
    if(root==null) {
        return;
    } 
    PreOrder(root.left);
    System.out.println(root.value);
    PreOrder(root.right);
}

```

#### 后序遍历

```java
statci void AfterOrder(TreeNode root) {
    if(root==null) {
        return;
    } 
    PreOrder(root.left);
    PreOrder(root.right
    System.out.println(root.value);
}

```

#### 层序遍历（算法题常考 重点）

```java
/*
通过List嵌套泛型List的方式来储存一层又一层的数据
[
	[3,1],
	[1,3,5],
	[4,5]
]

*/



statci List<List<Integer>> LevelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if(root==null) {
        return result;
    } 
    //创建一个队列来遍历树
    Queue<Integer> queue = new LinkedList<>();
    queue.add(root);
    //循环遍历Queue大小次数的for,保证一层的遍历完成
    while(!queue.isEmpty()) {
        List<Integer> list = new ArrayList<>();//用来装里面那层的数据
        int size = queue.size();
        for(int i = 0;i<size;i++) {//这里也可以用while(size>0){ xxxxxxxxxx;size--;}
			//出一次头节点，看看后面后没有左右子节点，有就直接加入list
            TreeNode treeNode = queue.poll();
            //出一次节点马上将节点值加入list中
            list.add(treeNode.value);
            if(treeNode.left!=null) {
                queue.add(treeNode.left);
            }
            if(treeNode.right!=null) {
                queue.add(treeNode.right);
            }
        }
        result.add(list);//完成一层的遍历加入一次，上面的for就是用来遍历层的，while是将后面的类似递归连起来
    }
    return result;  
}

```

## 线段树

学习博客位置  （链接ctrl+点击才可以）

[(544条消息) 线段树模板（Java）_Easenyang的博客-CSDN博客](https://blog.csdn.net/Easenyang/article/details/128199718)

b站视频名字：【数据结构】线段树（Segment Tree）

对于为啥要使用这个线段树的问题有：[(544条消息) 线段树从零开始_岩之痕的博客-CSDN博客_线段树](https://blog.csdn.net/zearot/article/details/52280189)













