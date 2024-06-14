# 二叉树的基本方法

## `getSize(), getHeight(), getDepth()`

与5C-树的基本方法十分相似。

## 更新规模记录——`updateSize()`

1. 若当前节点的孩子发生变化，比如原有的某个孩子被删除或者有新的孩子插入，就需要更新当前节点及其祖先的规模记录，以便后续的查询。

2. 原理：若节点`v`的左、右孩子分别为`lc`和`rc`，则`size(v) = 1 + size(lc) + size(rc)`。

3. 结论：一旦左右的子树规模已经确定，我们就可以在$O(1)$​时间内得到以节点v为根的子树规模。然后逆行向上，依次更新各个祖先的规模。

4. 算法实现：

   ```
   算法：updateSize(v)
   输入：二叉树中任一节点v
   输出：更新v的后代规模记录
   {
       令size(v) = 1 + size(lc) + size(rc);
       若v的父亲p存在，则调用updateSize(p)，递归地更新父亲的规模记录;//尾递归，可改写为迭代形式
   }
   ```

   

   ```java
   public void updateSize() {
       size = 1;//当前节点
       if (hasLChild())	size += getLChild().getSize();//左子树的规模
       if (hasRChild())	size += getRChild().getSize();//右子树的规模
       if (hasParent())	getParent().updateSize();//递归更新各个真祖先的规模记录
   }
   ```

5. 算法的效率：若节点`v`的深度为`depth(v)`，则总共新要修改`depth(v)+1`个节点的高度记录。而更新每一个节点的高度记录，只需要执行两次`getHeight()`操作、两次加法和两次取最大操作，不过常数时间，故该算法的总体运行时间为$O(\text{depth(v)}+1)$。

## 更新高度记录——`updateHeight()`

1. 同样地，在孩子发生变化后，也有必要更新当前节点的高度记录。

2. 原理：若`u`是`v`的孩子，则`height(v) >= height(u) + 1`。 且`height(v)=1+max(height(u))`。

3. 结论：只需读出左、右孩子的高度，取二者中的大者，再计入当前节点本身，就 得到了当前节点v的新高度。当然，此后也需要从v出发沿parent引用逆行向上，依次更新各个祖先 的高度记录。

4. 算法实现：

   ```
   算法：updateHeight(v)
   输入：二叉树中任一节点v
   输出：更新v的高度记录 
   { 
       height(v) = 0;//先假设没有左、右孩子 
       若v有左孩子lc，则令：height(v) = Max(height(v), 1 + height(lc)); 
       若v有右孩子lc，则令：height(v) = Max(height(v), 1 + height(rc)); 
       若v的父亲p存在，则调用updateHeight(p)，递归地更新父亲的高度记录;
   }
   ```

   ```java
   public void updateHeight() {
   		height = 0;//先假设没有左、右孩子
   		if (hasLChild())	height = Math.max(height, 1+getLChild().getHeight());//左孩子
   		if (hasRChild())	height = Math.max(height, 1+getRChild().getHeight());//右孩子
   
   		if (hasParent())	getParent().updateHeight();//递归更新各个真祖先的高度记录
   	}
   ```

5. 算法的效率：若节点`v`的深度为`depth(v)`，则总共新要修改`depth(v)+1`个节点的高度记录。而更新每一个节点的高度记录，只需要执行两次`getHeight()`操作、两次加法和两次取最大操作，不过常数时间，故该算法的总体运行时间为$O(\text{depth(v)}+1)$。

## 更新深度记录——`updateDepth()`

1. 在父亲节点发生变化后，也有必要更新当前节点的深度记录。

2. 原理：若`u`是`v`的孩子，则`depth(u) = depth(v) + 1`。

3. 结论：只需读出新的父亲节点的深度，再加上一即得到当前节点新的深度。当然，此后还需要沿着`lChild`和`rChild`引用，逐层向下递归地更新每一后代的深度记录。

4. 算法实现：

   ```
   算法：updateDepth(v)
   输入：二叉树中任一节点v
   输出：更新v的深度记录
   {
       若v的父亲节点p存在，则令depth(v) = depth(p)+1;
       否则，令depth(v) = 0;
       若v的左孩子lc存在，则调用updateDepth(lc);//沿孩子引用逐层向下，
       若v的右孩子rc存在，则调用updateDepth(rc);//递归地更新所有后代的深度记录
   }
   ```

   ```java
   public void updateDepth() {
       depth = hasParent() ? 1+getParent().getDepth() : 0;//当前节点
       if (hasLChild())	getLChild().updateDepth();//沿孩子引用逐层向下，
       if (hasRChild())	getRChild().updateDepth();//递归地更新所有后代的深度记录
   }
   ```

5. 算法的效率：若节点v的后代规模为`size(v)`，则总共需要修改`size(v)`个节点的深度记录。鉴于单个节点的深度记录可以在常数时间内得到更新，故 `updateDepth()`算法的总体运行时间为 $O(\text{size}(v))$。

## 分离子树——`secede()`

1. 为了简化二叉树动态操作的实现，这里专门设计了一个`secede()`方法。该方法的功能是，将以某一节点为根的子树从母树中分离出来。

2. 算法实现：

   ```
   算法：secede(v)
   输入：二叉树中任一节点v
   输出：将以v为根的子树丛母树中分离出来
   {
       若v有父亲 {
           切断父亲指向v的引用; 
           调用updateSize(v)和updateHeight(v)，更新v及其祖先的规模记录和高度记录;
           切断v指向父亲的引用; 
           调用updateDepth(v)，更新v及其后代的深度记录; 
   	} 
   } 
   ```

   ```java
   //断绝当前节点与其父亲的父子关系
   //返回当前节点
   public BinTreePosition secede() {
       if (null != parent)	{
           if (isLChild())	parent.setLChild(null);//切断父亲指向当前节点的引用
           else parent.setRChild(null);
   
           parent.updateSize();//更新当前节点及其祖先的规模
           parent.updateHeight();//更新当前节点及其祖先的高度
   
           parent = null;//切断当前节点指向原父亲的引用
           updateDepth();//更新节点及其后代节点的深度
       }
       return this;//返回当前节点
   }
   ```

3. 算法的效率：这一算法无非是对节点`v`各执行了一次上述的三种`update`方法。因此，`secede(v)`的算法的运行时间为$O(\text{depth}(v)+\text{size}(v)+1)$。

## 连接父子——`attachL()`和`attachR()`

1. 这一个方法的功能是，将节点`c`作为左或右孩子与节点`v`联接起来。

2. 算法实现：

   ```
   算法：attachL(p, c) 
   输入：两个二叉树节点p与c 
   输出：将c作为左孩子，与p联接起来 
   { 
       若p已经有左孩子lc，则首先调用secede(lc)将其摘除;  
       调用secede(c)，使c及其后代脱离原属母树; 
       设置相应的引用，在p和c之间建立父子关系; 
       调用updateSize(p)和updateHeight(p)，更新节点p及其祖先的规模和高度; 
       调用updateDepth(c)，更新c及其后代节点的深度;
   }
   ```

   ```java
   public BinTreePosition attachR(BinTreePosition c) {
       if (hasRChild())	getRChild().secede();//摘除当前节点原先的右孩子
       if (null != c) {
           c.secede();//c脱离原父亲
           rChild = c;	c.setParent(this);//确立新的父子关系
           updateSize();//更新当前节点及其祖先的规模
           updateHeight();//更新当前节点及其祖先的高度
           c.updateDepth();//更新c及其后代节点的深度
       }
       return this;
   }
   ```

   另外一边的思想和具体代码实现基本一致。

## 直接前驱/后继的定位算法——`getPrev()`和`getNext()`

1. 若规定：“左（右）子树必须完全居于根节点的（左）右侧”，则所有节点在水平轴上投影的自左向右次序，恰好与中序遍历序列的次序吻合。这表明：中序遍历就是按照自左向右的次序访问各个节点。

2. 根据其中序遍历序列S(T)，我们都可以在其中所有节点之间定义出一个线性次序。所以，除首、末节点外，每一节点都有唯一的直接前驱（后继）。如何快速实现定位直接前驱（后继）的算法？

3. 注意到以下事实，我们实际上可以使用该事实来实现直接查找前驱（后继）的定位算法。

   > 二叉树中，除中序遍历序列中的首节点外，任一节点`v`的直接前驱`u`不外乎三种可能： 
   >
   > 1. `v` 没有左孩子，同时`v`是右孩子：此时，`u`就是`v`的父亲节点；  
   > 2. `v`没有左孩子，同时v是左孩子：此时，从`v`出发沿`parent`引用逆行向上，直到第一个是右孩子 的节点`w`，则`u`就是`w`的父亲节点；  
   > 3. `v`有左孩子：此时，从`v`的左孩子出发，沿`rChild`引用不断下行，最后一个（没有右孩子的）节 点就是`u`。

4. 代码实现：根据上述结论，我们可以实现`getPrev()`和`getNext()`算法：

   ```java
   public BinTreePosition getPrev() {
       //若左子树非空，则其中的最大者即为当前节点的直接前驱
       if (hasLChild()) return findMaxDescendant(getLChild());
       //至此，当前节点没有左孩子
       if (isRChild())	return getParent();//若当前节点是右孩子，则父亲即为其直接前驱
       //至此，当前节点没有左孩子，而且是左孩子
       BinTreePosition v = this;//从当前节点出发
       while (v.isLChild()) v = v.getParent();//沿左孩子链一直上升
       //至此，v或者没有父亲，或者是父亲的右孩子
       return v.getParent();
   }
   
   protected static BinTreePosition findMaxDescendant(BinTreePosition v) {
       if (null != v)
       while (v.hasRChild()) v = v.getRChild();//从v出发，沿右孩子链一直下降
       //至此，v或者为空，或者没有右孩子
       return v;
   }
   ```

   