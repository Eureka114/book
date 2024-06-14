# BST的平衡与等价

## 二分查找树的平均性能

1. BST在最坏情况下，都需要$O(n)$时间才能完成，就这一点而言，似乎和字典结构无甚差别。现在我们主要来考察二分查找的平均性能。我们将针对两种随机统计的口径（随机生成、随机组成），给出BST的平均性能。

2. **随机生成**：

   1. 考虑关键码互异的$n$个条目，对于这些条目的任何一种全排列$\sigma$，若从空树开始，依次调用`insert()`算法，将$\sigma$中的关键码插入，都可以得到这n个条目的一棵二叉搜索树`T(σ)`。我们有如下定义：

      > 与随机排列 $\sigma$ 相对应的二分查找树`T(σ)`，称作由 $\sigma$ 生成的二分查找树。

   2. 对于任意 $n$ 个互异关键码，总共有 $n!$ 个全排列。如果假定这 $n!$ 个排列作为输入的概率均等，则只要将它们各自生成的二分查找树的平均查找长度进行平均，所得到的总体平均查找长度将能反映二分查找树的平均查找性能。

      > 由 $n$ 个互异条目随机生成的BST，平均查找长度为 $O(\log n)$ 。

3. **随机组成**：

   1. 一点思考：同一组条目的不同排列所生成二分查找树有可能雷同。所以，对所有 $n!$ 个随机排列进行平均，并不能反映二分查找树的平均查找性能。根据以上的分析，$n$ 个条目组成的BST的数目，将远远小于 $n!$ 。

   2. 一点改进：假定树中的n个节点是给定的，然后在中序遍历次序保持一致的前提下，统计它们能够构成的二分查找树的数目。

      > 观察结论：在保持中序遍历次序的前提下，由n个互异节点构成的每棵二叉树，都是一棵二分查找树（称作由这些节点组成的一棵二分查找树）。

   3. 那么，这些拓扑结构互异的BST有多少？

      > 定理：由n个互异节点组成的二分查找树，总共有 $\dfrac{(2n)!}{n!(n+1)!}$ 棵。

      

      > 推导：将n个互异节点组成的二分查找树的总数记为 $T(n)$ 。尽管由同一组节点组成的二叉搜索树不尽相同，但它们的中序遍历序列却必然相同，不妨记作：
      >
      > 
      > $$
      > \left[x_0,x_1,\ldots,x_{k-1}\right],x_k,\left[x_{k+1},\ldots,x_{n-1}\right]
      > $$
      > 
      >
      > 根据所取的树根节点不同，所有的搜索树可以分为 $n$ 类。如果以 $x_k$ 为根节点，则其左右子树见上。
      >
      > 对应的递推关系如下：
      >
      > 
      > $$
      > T(0)=T(1)=1,\quad T(n)=\sum\limits_{k=0}^{n-1}T(k)\cdot T(n-k-1)
      > $$
      > 
      >
      > （边界条件很好理解，而递推式可以以减治的方式进行理解：对于某一棵BST，其由左、右两个子树和根节点组成。因为中序序列确定，所以它们三者的排序方式不会改变，即依然是左子树——根节点——右子树组成。于是，问题转化成求 $T(L),T(R)$ ，其中，$L,R$ 分别是构成这两棵子树的元素总个数，且 $n=L+R+1$。同时，根据乘法原理，可以推出递推式。）
      >
      > 这是典型的Catalan数式递推关系，解之即得题中结论。

   4. 若假定这些树出现的概率相等，则通过对它们各自的平均查找长度进行平均，也可以得到一个总体的平均指标。

      > 由 $n$ 个互异条目随机生成的BST，平均查找长度为 $O(\sqrt n)$ 。

4. 两种衡量方式的比较：同一组关键码的不同排列所生成的二叉搜索树，未必不同。第一种情况下，某些BST被统计的次数较多。而实际上，越是平衡的树，被统计的次数亦越多。从这个角度讲，前一种平均的方式，在无形中高估了二叉搜索树的平均性能。因此，相对而言，第二种更加可信。

## BST：理想平衡与渐进平衡

1. 理想平衡：节点数目固定时，兄弟子树的高度越接近（平衡），全树也将倾向于更低。由n个节点组成的理想平衡二叉树，高度的下界为$\lfloor\log_2 n\rfloor$。当达到该下界时，称作理想平衡。这大致相当于完全树甚至满树：叶节点只能出现于最底部的两层——条件过于苛刻。
2. 渐进平衡（适度平衡）：理想平衡出现的概率极低，因此我们更多地关注渐进平衡。对于一棵BST，如果其高度渐进地不超过为$O(\log n)$，则称作渐进平衡。这种情况也在我们可以接受的范围内。
3. 平衡二叉搜索树（Balanced BST）：对于一棵BST，如果其满足渐进平衡，我们称之为平衡二叉搜索树。

## 中序歧义和等价变换

1. 中序歧义：对于两棵拓扑结构不同的二叉树，它们的中序遍历序列可能相同。这种现象称作中序歧义。中序歧义的存在，使得我们无法通过中序遍历序列来唯一确定一棵二叉树。

2. 等价BST：对于两棵拓扑结构不同的二叉树，如果它们的中序遍历序列相同，则称这两棵树是等价的。等价BST有两个重要性质：
   1. 上下可变：联接关系不尽相同，承袭关系可能颠倒。
   2. 左右不乱：中序遍历序列完全一致，全局单调非降。

3. 局部性和等价变换：平衡二叉搜索树的适度平衡性，都是通过对树中每一局部增加某种限制条件来保证的。比如，在红黑树中，从树根到叶节点的通路，总是包含一样多的黑节点；在AVL树中，兄弟节点的高度相差不过1。这些条件设定的比较精妙，除了保证适度平衡性，还具有以下的局部性：

   > 1. 经过单次动态修改操作后，至多只有$O(1)$处局部不再满足限制条件；
   > 2. 总可在$O(\log n)$时间内，使这$O(1)$处局部（以至全树）重新满足限制条件。

   这就意味着：刚刚失去平衡的二叉搜索树，必然可以迅速转换为一棵等价的平衡二叉搜索树。等价二叉搜索树之间的上述转换过程，也称作**等价变换**。

4. 旋转调整：最基本的修复手段，就是通过围绕特定节点的旋转，实现等价前提下的局部拓扑调整。关于二叉平衡树，平衡的调整操作分为包括左旋（Left Rotate 或者 zag）和右旋（Right Rotate 或者 zig） 两种。由于二叉平衡树在调整时需要保证中序遍历序列不变，这两种操作均不改变中序遍历序列。
   1. zig（右旋）：右旋也称为「右单旋转」或「LL 平衡旋转」。对于结点 A 的右旋操作是指：将 A 的左孩子 B 向右上旋转，代替 A 成为根节点，将 A 结点向右下旋转成为 B 的右子树的根结点，B 的原来的右子树变为 A 的左子树。对于右旋操作一般的更新顺序是：暂存 B 结点（新的根节点），让 A 的左孩子指向 B 的右子树 T2，再让 B 的右孩子指针指向 A，最后让 A 的父结点指向暂存的 B。
   
   2. zag（左旋）：左旋也称为「左单旋转」或「RR 平衡旋转」。对于结点 A 的左旋操作是指：将 A 的右孩子 B 向左上旋转，代替 A 成为根节点，将 A 结点向左下旋转成为 B 的左子树的根结点，B 的原来的左子树变为 A 的右子树。对于左旋操作一般的更新顺序是：暂存 B 结点（新的根节点），让 A 的右孩子指向 B 的左子树 T2，再让 B 的左孩子指针指向 A，最后让 A 的父结点指向暂存的 B。
   
   3. zig/zag操作只改变了三组结点关联，相当于对三组边进行循环置换一下，因此需要暂存一个结点再进行轮换更新。
   
      ![bst-rotate](https://oi-wiki.org/ds/images/bst-rotate.svg)
   
   4. 四种平衡性被破坏的情况：

      LL 型：T 的左孩子的左子树过长导致平衡性破坏。

      调整方式：右旋节点 T。

      ![bst-LL](https://oi-wiki.org/ds/images/bst-LL.svg)

      RR 型：与 LL 型类似，T 的右孩子的右子树过长导致平衡性破坏。

      调整方式：左旋节点 T。

      ![bst-RR](https://oi-wiki.org/ds/images/bst-RR.svg)

      LR 型：T 的左孩子的右子树过长导致平衡性破坏。

      调整方式：先左旋节点 L，成为 LL 型，再右旋节点 T。

      ![bst-LR](https://oi-wiki.org/ds/images/bst-LR.svg)

      RL 型：与 LR 型类似，T 的右孩子的左子树过长导致平衡性破坏。

      调整方式：先右旋节点 R，成为 RR 型，再左旋节点 T。

      ![bst-RL](https://oi-wiki.org/ds/images/bst-RL.svg)
## 补充

1. 由 $n$ 个互异条目随机生成的BST，平均查找长度为 $O(\log n)$ ；由 $n$ 个互异条目随机生成的BST，平均查找长度为 $O(\sqrt n)$  的证明详见

   > L. Devroye. A Note on the Height of Binary Search Trees. J. of ACM (1986), 33(3):489-498
   >
   > P. Flajolet & A. Odlyzko. The Average Height of Binary Trees and Other Simple Trees.  Journal of Computer and System Sciences (1982), 25(2):171-213


## 参考资料
1. [二叉搜索树 & 平衡树 - OI Wiki (oi-wiki.org)](https://oi-wiki.org/ds/bst/)