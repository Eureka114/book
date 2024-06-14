# 树基础和基本实现

## 树的基本知识

1. ### 树的优点：

   |       项目       |                             优点                             |                             缺点                             |
   | :--------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
   | 基于数组实现的DS | 在**常数**的时间内找到目标对象，并读取或更新其内容。（通过下标或秩） | 一旦需要对这类结构进行修改（插入、删除），都需要耗费**线性**的时间。 |
   | 基于链表实现的DS | 允许我们借助引用或位置对象，在**常数**的时间内插入或删除元素； | 为了找出居于特定次序的元素，我们不得不花费**线性**的时间对整个结构进行遍历查找。 |

   > 树：每一次查找、更新、插入或删除操作都可以在$O(\log n)$内完成，而每次遍历都可以在$O(n)$的时间内完成。（平衡二分查找树）

   > 不难证明，对任意的$c>0$，都有$\log n = O(n^c)$。也就是说，就多项式而言$O(\log n)$与$O(1)$可以无限接近，而$O(\log n)$与$O(n)$相比几乎提高了一个线性因子。因此就这一点而言，树结构的确能够将数组和链表的优点结合起来。

2. ### 树的某些特征：

   1. **半线性结构(Semi-linear Structures)**: 树结构的元素间不存在天然的直接后继或直接前驱关系，因此属于非线性结构(Non-linear structures)。而只要附加上某种约束（比如遍历），也可以在树结构中的元素之间确定某种线性次序，因此也有人称之为“半线性结构”。
   2. **分层结构**：层次化。例子：文件系统、数据库系统、URL等。
   3. **综合性**：兼具`Vector`和`List`的优点；兼顾高效的查找、插入、删除。
   4. 树和图的相似性：树 = 无环连通图 = 极小连通图 = 极大无环图。

3. ### 树的有关术语和性质：

   1. 节点的深度、树的深度与高度：**树中所有节点的最大深度，称作树的深度或高度。**

      1. 树中的元素也称为节点`Node`。树中的每个节点`v`都被赋予了一个特殊的指标⎯⎯深度，记作`depth(v)`。
      2. 每个节点的深度都是一个非负整数；
      3. 深度为0的节点有且仅有一个，称作树根（Root）；
      4. 对于深度为$k\ (k\ge 1)$的每个节点`u`，都有且仅有一个深度为k-1的节点`v`与之对应，称作`u`的父亲（Parent）或父节点。
      5. 若节点`v`是节点`u`的父亲，则`u`称作`v`的孩子（Child），并在二者之间建立一条树边（Edge）。边的方向是从父亲指向孩子。
      6. 尽管每个节点至多只有一个父亲，但却可能有多个孩子。同一节点的孩子互称“兄弟”（Sibling）。
      7. 树中节点的数目，总是等于边数加一。

   2. 度、内部节点与外部节点：

      1. **度（Degree）**：取某一节点，该节点的**孩子**数目。注意：节点的父亲不计入度中。

      2. **“内部节点”（Internal node）**：至少拥有一个孩子的节点称作内部节点。

      3. **“外部节点”（External node）**：没有任何孩子的节点则称作外部节点，或“叶子”(Leaf)。

         > 一个节点为叶子当且仅当它的度为`0`。

   3. 路径（Path）：

      1. 由树中$k+1$节点通过树边首尾衔接而构成的序列$\{(v_0, v_1), (v_1, v_2), \ldots, (v_{k-1}, v_k) | k \ge  0\}$，称作树中长度为$k$的一条路径。下面的树，{(1, 2), (2, 5), (5,8), (8, 7), (7, 0)}为一条路径。注意，当讨论路径时，不需要考虑方向。

         ```
         								5
                 +-----------------------+------------------+
         		2										   8
             +---+-------------+                   +--------+--------+
           	1           	  3					  6 				7
                      +--------+               +---+         		+---+
                      9 						  4 						0
         ```

      2. 树中任何两个节点之间都存在唯一的一条路径。

      3. 若`v`是`u`的父亲，则`depth(v) + 1 = depth(u)`。

         推论：从树根通往任一节点的路径长度，恰好等于该节点的深度。

   4. 祖先、后代、子树和节点的**高度**：

      1. 每个节点都是自己的“祖先”（Ancestor），也是自己的“后代”（Descendant）；  

      2. 若`v`是`u`的父节点的祖先，则`v`也是`u`的祖先；若`u`的父节点是`v`的后代，则`u`也是`v`的后代。

      3. **真祖先（后代）**：除节点本身以外的祖先（后代），称作真（proper）祖先（后代）。

      4. 任一节点`v`的深度，等于其真祖先的数目。 

      5. 任一节点`v`的祖先，在每一深度上最多只有一个。（否则路径不唯一，与上面矛盾）

      6. 树`T`中每一节点`v`的所有后代也构成一棵树，称作`T`的“以`v`为根的**子树（Subtree）**”。

         > 空节点（`null`）本身也构成一棵树，称作“空树”（Empty tree）。空树虽然不含任何节点，但却是任何树的（平凡）子树。 特别地，空树的高度为$-1$。

         > 我们可将“以`v`为根的子树”直接称作“子树`v`”。

      7. 若子树`v`的深度（高度）为`h`，则称`v`的高度为`h`，记作height(v) = h。

      8. > 对于叶子节点`u`的任何祖先`v`，必有`depth(v) + height(v) \ge  depth(u)`。

   5. 共同祖先及最低共同祖先：

      1. **共同祖先（Common Ancestor）**： 在树`T`中，若节点`u`和`v`都是节点`a`的后代，则称节点`a`为节点`u`和`v`的共同祖先。

      2. 每一对节点至少存在一个共同祖先。

      3. **最低共同祖先（Lowermost Common Ancestor, LCA）**：在一对节点u和v的所有共同祖先中，深度最大者称为它们的最低共同祖先，记作 `lca(u, v)`。LCA必存在且唯一。

         

## 树抽象数据类型及其实现

1. ### “父亲——长子——弟弟”模型：

   1. 根据树的定义，每个节点的所有后代均构成了一棵子树，故从数据类型的角度来看，树、子树 以及树节点都是等同的。这里，将它们统一为一个类：`Tree`.

2. ### 基于列表实现树：

   ```Java
   public class TreeLinkedList implements Tree { 
       private Object element;//树根节点 
       private TreeLinkedList parent, firstChild, nextSibling;//父亲、长子及最大的弟弟 
       //（单节点树）构造方法 
       public TreeLinkedList() 
       	{ this(null, null, null, null); } 
       //构造方法 
       public TreeLinkedList(Object e, TreeLinkedList p, TreeLinkedList c, TreeLinkedList 
       s) { 
           element = e; 
           parent = p; 
           firstChild = c; 
           nextSibling = s; 
       } 
       /*---------- Tree接口中各方法的实现 ----------*/ 
       //返回当前节点中存放的对象 
       public Object getElem() 
       	{ return element; } 
       //将对象obj存入当前节点，并返回此前的内容 
       public Object setElem(Object obj) 
       	{ Object bak = element; element = obj; return bak; } 
       //返回当前节点的父节点；对于根节点，返回null 
       public TreeLinkedList getParent() 
       	{ return parent; } 
       //返回当前节点的长子；若没有孩子，则返回null 
       public TreeLinkedList getFirstChild() 
       	{ return firstChild; } 
       //返回当前节点的最大弟弟；若没有弟弟，则返回null 
       public TreeLinkedList getNextSibling() 
      	 	{ return nextSibling; } 
       //返回当前节点后代元素的数目，即以当前节点为根的子树的规模 
       public int getSize() { 
           int size = 1;//当前节点也是自己的后代 
           TreeLinkedList subtree = firstChild;//从长子开始 
           while (null != subtree) {//依次 
               size += subtree.getSize();//累加 
               subtree = subtree.getNextSibling();//所有孩子的后代数目 
       	} 
   		return size;//即可得到当前节点的后代总数 
       } 
       //返回当前节点的高度 
       public int getHeight() { 
           int height = -1; 
           TreeLinkedList subtree = firstChild;//从长子开始 
           while (null != subtree) {//依次 
               height = Math.max(height, subtree.getHeight());//在所有孩子中取最大高度 
               subtree = subtree.getNextSibling(); 
           } 
           return height+1;//即可得到当前节点的高度 
       } 
       //返回当前节点的深度 
       public int getDepth() { 
           int depth = 0; 
           TreeLinkedList p = parent;//从父亲开始 
           while (null != p) {//依次 
           	depth++; p = p.getParent();//访问各个真祖先 
           } 
           return depth;//真祖先的数目，即为当前节点的深度 
       } 
   } 
   ```