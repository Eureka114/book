# 堆（Heap）

## 定义

1. 堆通常是一个可以被看做一棵树的数组对象。堆总是满足下列性质：

   1. （堆序性）堆中某个节点的值总是不大于或不小于其父节点的值；

   2. （结构性）堆总是一棵**完全二叉树**（除最后一层外，其他层节点都有两个子节点，并且最后一层节点**都左排列**）。





将**根节点最大**的堆叫做最大堆或**大根堆**，**根节点最小**的堆叫做最小堆或**小根堆**。常见的堆有二叉堆、斐波那契堆等。

```
//小根堆
								2(Root)
        +-----------------------+------------------+
		4										   5
    +---+-------------+                   +--------+
  	9           	  10				 11(Last Node)
```

> **Theorem: A heap storing n keys has height $O(\log n)$**
>
> Proof: (Using properties of Complete Binary Tree)
>
> Let h be the height of a heap storing n keys
>
> Since there are $2^i$keys at depth $i=0,\ldots,h-1$, 
>
> and at least one key at depth $h$, we have $n\ge 1+2+4+\ldots+2^{h-1}-1$
>
> Hence, $n\ge 2^h$, i.e., $h\le\log n$

## 用堆实现优先队列

若`insert()`和`delMin()`操作的时间复杂度线性正比于堆的高度，则它们都可以在$O(\log n)$时间内完成这两种操作。堆在物理上可以借助向量实现。内部节点的最大`Rank`：$\left\lfloor\dfrac{n-2}{2}\right\rfloor$

```c++
#define Parent(i) (((i) - 1) >> 1)
#define LChild(i) (1 + ((i) << 1))
#define RChild(i) ((1 + (i)) << 1)
```

> 上面内容是：
>
> 对于一个Rank为`i`的节点`node`
>
> 其左孩子的Rank为`2i+1`
>
> 其右孩子的Rank为`2i+2`
>
> 其父节点的Rank为`(i-1)/2`。

利用堆结构来表示的优先队列，包括了如下的要素：

```
1. 堆`H`。即一棵完全二叉树，其中各节点存有条目并根据其关键码满足堆序性。可以通过向量实现`H`。为了叙述方便，以下用`k(v)`来表示节点`v`（所存放的）条目的关键字。 
2. 比较器`C`。
```

在查询规模时，实际上是在查询`H`的规模。因为二叉树由向量实现，所以`isEmpty()`和`getSize()`均可以在$O(1)$时间内完成。若要获取堆顶，只需要取出向量中`Rank==1`的元素即可。

```java
public class ComplBinTree_Vector extends BinTree_LinkedList implements ComplBinTree {
	private Vector T;//向量
	
//构造方法：默认的空树
	public ComplBinTree_Vector()
	{ T = new Vector_ExtArray();	root = null; }

//构造方法：按照给定的节点序列，批量式建立完全二叉树
	public ComplBinTree_Vector(Sequence s)
	{ this();	if (null !=s) while (!s.isEmpty()) addLast(s.removeFirst()); }
}
```

## 插入与上滤（`insert(key, e)`操作的实现）(UpHeap)

1. 通过完全二叉树的`addLast()`方法，将条目`v=(key,e)`作为末尾节点插入到堆中。此时，由于至少插入到了最后一层，堆的结构性未被破坏，堆仍是一棵完全二叉树。

2. 插入后，除非`H`原先为空，否则`v`必有父亲。对于`v`和器父亲`u`的关键码大小有两种可能（以小根堆举例）：`key(v) ≥ key(u)`或者`key(v)<key(u)`。若为后者，则堆的堆序性不再满足，必须进行父亲和孩子的交换。注意：此交换可能会进行多次，直到整个堆的堆序性得到满足。

3. **上滤(Percolating Up)**：指新插入节点通过与父亲交换不断向上移动的这一过程。如果利用向量完成二叉树，每一次交换只需要$O(1)$时间，则`insert()`的操作的时间复杂度为$O(\log n)$

4. 代码实现：

   ```java
   //上滤算法
   	protected void percolateUp(BinTreePosition v) {
   		BinTreePosition root = H.getRoot();//记录根节点
   		while (v != H.getRoot()) {//不断地
   			BinTreePosition p = v.getParent();//取当前节点的父亲
   			if (0 >= comp.compare(key(p), key(v)))	break;//除非父亲比孩子小
   			swapParentChild(p, v);//否则，交换父子次序
   			v = p;//继续考察新的父节点（即原先的孩子）
   		}
   	}
   ```

   > **English Version:**
   >
   > After the insertion of a new key k at the node z, the heap-order property **may** be **violated**
   >
   > Algorithm **upheap** restores the heap order property by swapping k along an upward path from the insertion node
   >
   > **Up-heap** terminates when the key k reaches **the root** or a **node whose parent has a key smaller** **than or equal to k**
   >
   > Since a heap has height $O(\log n)$, up-heap runs in $O(\log n)$time

## 删除与下滤（`delMin()`操作的实现）(DownHeap)

1. 若将堆顶删除之后，堆的结构性不再满足。为了恢复其结构性，简便做法是最末尾的节点`v`移动至堆顶位置。

2. 若堆中剩余至少两个节点，则移动至堆顶的`v`必然有后代。此时，由于`v`是从堆底移动上来的，堆的堆序性无法得到满足。这里，对称地运用上滤操作：如果`v`与孩子之间违背堆序性，我们在`v`的两个孩子中挑选出**更小者**（记作`u`，实际上`u`也是`v`及其两个孩子中的最小者），并将`v`与`u`交换。经过这样的一次交换，`v`将下降一层；而更重要的是，`v`及其（原先的）两个孩子之间的堆序性随即得到恢复。如此重复，直到整个堆的堆序性得到恢复。

3. **下滤(Percolating Down)**：指堆顶节点通过与孩子交换不断向下移动的这一过程。如果利用向量完成二叉树，每一次交换只需要$O(1)$时间，则`delMin()`的操作的时间复杂度为$O(\log n)$

4. 代码实现：

   ```java
   //下滤算法
   	protected void percolateDown(BinTreePosition v) {
   		while (v.hasLChild()) {//直到v成为叶子
   			BinTreePosition smallerChild = v.getLChild();//首先假设左孩子的（关键码）更小
   			if (v.hasRChild() && 0 < comp.compare(key(v.getLChild()), key(v.getRChild())))
   				smallerChild = v.getRChild();//若右孩子存在且更小，则将右孩子作为进一步比较的对象
   			if (0 <= comp.compare(key(smallerChild), key(v))) break;//若两个孩子都不比v更小，则下滤完成
   			swapParentChild(v, smallerChild);//否则，将其与更小的孩子交换
   			v = smallerChild;//并继续考察这个孩子
   		}
   	}
   ```
   
   > **English Version:**
   >
   > After **replacing** the root key with the key k of the last node w, the heap-order property may be violated
   >
   > Algorithm **downheap** restores the heap order property by swapping k along a downward path from the root
   >
   > Down-heap terminates when the key k **reaches a leaf or a node whose children have keys larger than or equal to k**
   >
   > Since a heap has height $O(\log n)$, down-heap runs in $O(\log n)$time

## 建堆（Heapification）

1. 蛮力算法（自上而下）：反复调用`insert()`方法建立完全二叉堆。每一次调用`insert()`方法要$O(\log n)$间，故依次插入所有$n$个节点需要$O(\log n)$间。

   
   $$
   T(n)=O(\log1)+O(\log2)+\ldots+O(\log n)=O(\log n!)=O(\log n^n)=O(n\log n)
   $$
   

   这里，我们甚至可以先利用时间复杂度为$O(n\log n)$的算法(`QuickSort, MergeSort`)对元素按照关键码进行全排序，再依次插入。所以，用蛮力建立堆并不可取。

2. Robert Floyd算法（自下而上）：将上述蛮力建堆策略的处理方向与次序颠倒过来。首先，将所有节点存储为一棵完全二叉树，从而满足结构性和完整性。为了恢复堆序性，可以自下而上，对各个内部节点实施下滤操作。在所有内部节点都经过下滤之后，二叉树处处都将满足堆序性，亦即成为一个名副其实的堆。

   > 建堆策略（Heapify Algorithm）：
   >
   > 1. Starting at index $i=\left\lfloor\dfrac{n}{2}\right\rfloor-1$;
   > 2. Call `downheap()` until $i=0$, decrementing $i$ at each step.

   > 结论：任意给定分别以`r0`、`r`1为根节点的堆`H0`、`H1`以及节点`p`。为了得到对应于$H_0\cup H_1\cup {p}$的一个堆，只需将`r0`和`r1`当作`p`的孩子，然后对`p`进行下滤。

   ```
   算法：Heapificate(N[], n) 
   输入：n个堆节点组成的序列N[] 
   输出：将输入的节点组建为一个堆 
   说明：Robert Floyd算法，O(n)时间 
   { 
       创建容量为n的空堆H; 
       将N[]中的n个节点直接复制到堆中; //结构性自然得到满足 
       自底而上，依次下滤各内部节点; //此后堆序性也必然满足 
       返回H; 
   }
   ```

   ```java
   public PQueue_Heap(Comparator c, Sequence s) {
       comp = c;
       H = new ComplBinTree_Vector(s);
       if (!H.isEmpty()) {
           for (int i = H.getSize()/2-1; i >= 0; i--)//自底而上
           	percolateDown(H.posOfNode(i));//逐节点进行下滤
       }
   }
   ```

   该方法的效率分析：首先，在完全二叉树中对高度为`h`的任意节点进行下滤，最多要进行`h`次节点交换。若记堆的高度为`d`，则高度为`h`的节点不会多于$2^{d-h}$个，因此，若该算法下滤操作的时间为$S(n)$，堆的规模为$n$，则有

   
$$
	S(n)\le\sum_{h=0}^d(h\cdot2^{d-h})=2^{d+1}-d-2<2^{d+1}
$$

   

   考虑到完全二叉树的上面$d$层（深度为0至d-1）的所有节点构成一棵满树，则有


$$
	n\ge 2^d-1
$$


   于是，有


$$
S(n)<2(n+1)=O(n)
$$

   

   

   证明如下：

   <center>![](https://img2.imgtp.com/2024/05/26/6w65BSE0.svg)</center>


   故所有下滤操作总共只需线性的时间。考虑到将n个节点组织为一棵完全二叉树也可以在线性时间内完成，有

   > 只需$O(n)$时间，即可将$n$个条目组织为一个二叉堆结构。

3. 在一个大顶堆中，最小元素通常位于堆的底部，因此直接找到并删除最小元素的操作复杂度是$O(n)$，而不是$O(\log n)$这是因为我们需要遍历整个堆来找到最小元素。在删除元素后，我们还需要重新调整堆，这又是一个$O(\log n)$操作。所以，总的时间复杂度是$O(n)$。

   如果你需要在$O(\log n)$时间复杂度内执行获取和删除最小元素的操作，**一种常见的做法**是使用一个**额外**的小顶堆来存储所有的元素。这样，大顶堆可以用来快速获取和删除最大元素，而小顶堆可以用来快速获取和删除最小元素。这种数据结构通常被称为“**双堆**”或“双端优先队列”。

   请注意，这种方法的缺点是插入操作的时间复杂度会变为$O(\log n)$因为每次插入元素时，都需要在两个堆中进行操作。同时，这也会增加存储空间的需求，因为需要存储两个堆。
