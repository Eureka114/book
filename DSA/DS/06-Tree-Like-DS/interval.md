# Interval Tree 区间树

## 从Stabbing Query问题开始

问题描述：在一维的情况下，给出一组区间$S=\{s_i=[x_i,x_i']\mid 1\le x\le n\}$，和一个点$q_x$。现在，请你找出所有包含点$q_x$的区间。这个问题称为窗口问题/截窗问题，适合用区间树来求解。

## 区间树

1. 定义：区间树是用于保存区间的树数据结构。具体来说，它允许人们有效地找到与任何给定区间或点重叠的所有区间。
2. Definition: An interval tree is a tree data structure to hold intervals. Specifically, it allows one to efficiently find all intervals that overlap with any given interval or point.

3. 区间树的部分特点/性质：建树的时间复杂度为$O(n\log n)$，空间复杂度为$O(n)$；有效地插入/删除区间的时间复杂度为$O(\log n)$，若运行时间(Runtime)为$m$，则搜索的时间复杂度为$O(\log n+m)$。
4. 特别地，如果区间的右端点在一个非常小的整数集之间，（如：$1,2,\ldots,O(n)$）则我们甚至可以使用改良版的“间隔树”来实现这一个预处理的时间复杂度为$O(n)$，查询的时间复杂度为$O(1+m)$的算法。(Schmidt, 2009)

## Stabbing Query的解法

1. 若区间不相重叠，则完全可以使用一棵普通的BST存储所有的区间，此时的时间复杂度在$O(\log n)$。

2. 但是，对于有重叠的区间，因为左、右端点的相对情况未知，所以不能使用上述方法。或许，我们可以使用两棵BST分别存放左、右端点。但是，因为合并结果并找出答案需要$O(\log n+n)=O(n)$的时间，时间复杂度不尽人意。这时，我们就应该使用区间树了。[Wikipedia](https://en.wikipedia.org/wiki/Interval_tree)介绍了区间树的两种备选设计，称为**居中区间树**和**增强树**。

3. 居中区间树(Centered Interval Tree)：

  1. 令点集$P$为所有区间的左、右端点。（$\text{Card}(A)=2n$）再令$x_{mid}$为点集$P$的中间元素。

  2. 现在，所有区间可以被划分成3个子集合：$S_{Left}=\{S_i|x_i'<x_{mid}\}$、$S_{Mid}=\{S_i|x_i\le x_{mid}\le x_i'\}$和$S_{Right}=\{S_i|x_i>x_{mid}\}$。我们可以递归地构建区间树，直到区间的个数为1。这里，我们有：

$$
\max(\text{Card}(S_{L}),\text{Card}(S_{R}))\le\frac{n}{2}
$$

$$
\text{Best Case: }\text{Card}(S_{Mid})=n,\quad\text{Worst Case: }\text{Card}(S_{Mid})=1
$$


​    

  3. 观察：只要不含点$x_{mid}$的区间就会被归为左/右子集合，而包含的（含端点）则会被划分至中间的子集合。同时，令$L_{L/R}$为所有按照左/右端点排列的在中间子集的区间元素。所以，所有的区间会出现两次。（即在以左、右端点为代表的区间集$L_L,L_R$各出现一次）

  4. 建立二叉树：现在，可以建立一棵二叉树，其中，每个节点应该存储：

> - 中心点
> - 指向另一个节点的指针，该节点包含中心点左侧的所有间隔
> - 指向另一个节点的指针，该节点包含完全位于中心点右侧的所有间隔
> - 所有与中心点重叠的间隔，按其起点排序
> - 与中心点重叠的所有间隔，按其终点排序

> - Center point
> - Pointer to another node containing all intervals completely to the Left/Right of the center point
> - All intervals overlapping the center point sorted by their Beginning/Ending point

  5. 进行查找$q_x$：代码如下。对于这个线性递归，每一次查询需要访问$O(\log n+m)$个节点。

```c
void queryIntervalTree(int xmid(v), int qx){
    if(!v) return;//base
    if(qx<xmid(v)){
        use L-list to report all intervals of Smid(v) containing qx;
        queryIntervalTree(lc(v),qx);
    }
    else if (xmid(v)<qx){
        use R-list to report all intervals of Smid(v) containing qx;
        queryIntervalTree(rc(v),qx);
    }
    else{
        report all segments of Smid(v);
        //ignore rc(v) and lc(v), since has been found.
    }
}
```

##  增强树(Augmented Tree)



## 参考资料

1. Schmidt, Jens. (2009). Interval Stabbing Problems in Small Integer Ranges. Lecture Notes in Computer Science. 5878. 163-172. 10.1007/978-3-642-10631-6_18.  https://dx.doi.org/10.1007/978-3-642-10631-6_18
2. [间隔树 - 维基百科，自由的百科全书 (wikipedia.org)](https://en.wikipedia.org/wiki/Interval_tree)
3. [DSACPP | 清华大学(tsinghua.edu.cn)](https://dsa.cs.tsinghua.edu.cn/~deng/ds/dsacpp/index.htm)