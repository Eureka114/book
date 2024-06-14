
# 树的基本方法

## 统计（子）树的规模——`getSize()`

1. 结论： 一棵树的规模，等于根节点下所有子树规模之和再加一，也等于根节点的后代总数。

2. 操作（递归）：先使用`firstChild`找出根节点的长子，并沿着`nextSibling` 引用顺次找到其余的孩子，递归地统计出各子树的规模。最后，只要将所有子树的规模累加起来，再计入根节点本身，就得到了整棵树的规模。当遇到没有任何孩子的节点（即原树的叶子）时，递归终止。

   ```java
   public int getSize() { 
       int size = 1;//当前节点也是自己的后代 
       TreeLinkedList subtree = firstChild;//从长子开始 
       while (null != subtree) {//依次 
           size += subtree.getSize();//累加 
           subtree = subtree.getNextSibling();//所有孩子的后代数目 
       } 
       return size;//即可得到当前节点的后代总数 
   }
   ```

3. 时间复杂度：$O(n)$。

   

## 计算节点的高度——`getHeight()`

1. 结论：若`u`是`v`的孩子，则`[1]height(v) ≥ height(u) + 1; [2]height(v)=1+max(height(u))` 。

2. 操作（递归）：仍是先使用`firstChild`找出根节点的长子，并沿着`nextSibling` 引用顺次找到其余的孩子，递归地统计出各子树的高度。然后找到所有子树的最大高度并计入根节点本身，就得到了根节点的高度（即树高）。当该节点为叶子时，递归中止。

   ```java
   public int getHeight() { 
       int height = -1; 
       TreeLinkedList subtree = firstChild;//从长子开始 
       while (null != subtree) {//依次 
           height = Math.max(height, subtree.getHeight());//在所有孩子中取最大高度 
           subtree = subtree.getNextSibling(); 
       } 
       return height+1;//即可得到当前节点的高度 
   } 
   ```

3. 时间复杂度：$O(n)$。



## 计算节点的深度——`getDepth()`

1. 结论：若`u`是`v`的孩子，则`depth(u) = depth(v) + 1`。 

2. 操作（尾递归或迭代）：`getDepth(v)`将从`v`的父亲开始，沿着`parent`指针不断上移，直到深度为0的树根。在这个过程中所遇到的每个节点，都是`v`的真祖先；反之，在这一过程中，v的每一真祖先迟早都会被找到。因此，根据总共上移的层数，就可以得到`v`在整棵树中的深度。

   ```java
   public int getDepth() { 
       int depth = 0; 
       TreeLinkedList p = parent;//从父亲开始 
       while (null != p) {//依次 
           depth++; p = p.getParent();//访问各个真祖先 
       } 
       return depth;//真祖先的数目，即为当前节点的深度 
   } 
   ```

3. 时间复杂度：$O(n)$。

   

## 树的遍历

### 遍历(Traversal)

按照某种次序访问树中各节点，每个节点被访问恰好一次。按照被访问的次序，可以得到由树中所有节点排列成的一个序列。$T=\text V\cup\text L\cup\text R$。遵照不同的遍历规则，我们可以得到不同的遍历方法。遍历：结果 ~ 过程 ~ 次序 ~ 策略。

### 先序遍历(Pre-order Traversal)

1. 首先访问其根节点，然后再递归地做左子树的先序遍历，最后做右子树的先序遍历。

2. 应用：打印结构化文件。

3. 递归实现：

   ```cpp
   //c++
   template <typename T, typename VST> 
   void traverse( BinNodePosi<T> x, VST & visit ) {
       if ( ! x ) return;
       visit( x->data );
       traverse( x->lc, visit );//尾递归，可以较为简单地化为迭代形式
       traverse( x->rc, visit );//只需要引入一个栈，即可实现上述操作
   } //O(n)
   ```

   ```java
   //java
   public void PreorderTraversal(TreeLinkedList v){
       if(null!=v){
           visit(v.element);
           for(TreeLinkedList u=v.getFirstChild(); null!=u; u=u.getNextSibling()){
               PreorderTraversal(u);
           }
       }
   }
   ```

   

   > 制约：使用默认的`Call Stack`，允许递归的深度有限。我们需要不依赖于递归机制，即使用迭代算法。

4. 对于先序遍历（尾递归）的迭代形式的实现：

   ```cpp
   //cpp
   template <typename T, typename VST> //元素类型、操作器 
   void travPre_I1 ( BinNodePosi(T) x, VST& visit ) { //二叉树先序遍历算法（迭代版#1） 
       Stack<BinNodePosi(T)> S; //辅助栈 
       if ( x ) S.push ( x ); //根节点入栈 
       while ( !S.empty() ) { //在栈变空之前反复循环 
           x = S.pop(); visit ( x->data ); //弹出并访问当前节点，其非空孩子的入栈次序为先右后左 
           if ( HasRChild ( *x ) ) S.push ( x->rc ); 
           if ( HasLChild ( *x ) ) S.push ( x->lc ); 
       }
   }
   ```

   > 缺陷：对于中序和后序遍历，即非尾递归的场合，则无法实现。我们需要考虑一种新的思路。

5. **“藤缠树”**：仔细观察可以发现，沿着左侧藤，整个遍历过程可分解为：**自上而下**访问藤上节点，再自下而上遍历各右子树。这里，各右子树的遍历彼此独立，自成一个子任务。

   > 我们如何进行上述的操作？首先，访问根节点；然后，访问根节点的左孩子；再然后，访问根节点的左孩子的左孩子……即首先我们沿着**左侧链**一直进行访问并向着树的更深层走下去。如此下去，直到某一个节点没有左孩子。此时，我们进行一次转移操作，回退到上一次出现右孩子的节点，然后继续重复上述的操作。

   思路如下：我们只将左侧链画出：在左侧链上面，我们仅仅画出根节点和链上各节点的左孩子，而每个节点右孩子和其下面的所有内容，我们抽象地绘制成一棵右子树，标记在其原先的右孩子的位置。第一次访问，一定会沿着左侧链上的节点进行访问，直到访问到没有左孩子的节点`L_d`。然后，我们进行回溯，自下而上地遍历从`L_d`到`L_0`（根节点）上面的全部的右子树。

   ```
   preorder(T)={
       visit(L0); visit(L1); ...; visit(L_end-1); visit(L_end); 
       preorder(T_end);//(preorder就是从该位置重新执行一系列的visit操作，直到叶子节点)
       preorder(T_end-1); ...; preorder(T1); preorder(T0);
   }
   ```

   4. 迭代实现

   ```c++
   //从当前节点出发，沿左分支不断深入，直至没有左分支的节点；沿途节点遇到后立即访问
   template <typename T, typename VST> //元素类型、操作器
   static void visitAlongVine( BinNodePosi<T> x, VST& visit, Stack<BinNodePosi<T>>& S ) {
      while ( x ) {
         visit( x->data ); //访问当前节点
         S.push( x->rc ); //右孩子入栈暂存（可优化：通过判断，避免空的右孩子入栈）
         x = x->lc; //沿左分支深入一层
      }
   }//使用栈，正是利用其LIFO的特性。
   
   template <typename T, typename VST> //元素类型、操作器
   void travPre_I2( BinNodePosi<T> x, VST& visit ) { //二叉树先序遍历算法（迭代版#2）
      Stack<BinNodePosi<T>> S; //辅助栈
      while ( true ) {
         visitAlongVine( x, visit, S ); //从当前节点出发，逐批访问
         if ( S.empty() ) break; //直到栈空
         x = S.pop(); //弹出下一批的起点
      }
   }
   ```

   

### 中序遍历(Inorder Traversal)

1. 在中序遍历中，我们首先递归地做左子树的中序遍历，然后访问根节点，最后递归地做右子树的中序遍历。

2. 应用：使用中序遍历打印与二叉树相关的算术表达式。

3. 递归实现：

   ```c++
   //c++
   template <typename T, typename VST> //元素类型、操作器
   void travIn_R ( BinNodePosi<T> x, VST& visit ) { //二叉树中序遍历算法（递归版）
       if ( !x ) return;
       travIn_R ( x->lc, visit );//注意到，这里已经不是尾递归了
       visit ( x->data );
       travIn_R ( x->rc, visit );
   }
   ```

   ```javascript
   //javascript
   class TreeNode {
       constructor(value, left = null, right = null) {
           this.value = value;
           this.left = left;
           this.right = right;
       }
   }
   
   function inOrderTraversal(root) {
       if (root === null) {
           return;
       }
   
       inOrderTraversal(root.left); // 遍历左子树
       console.log(root.value); // 访问根节点
       inOrderTraversal(root.right); // 遍历右子树
   }
   ```

   

4. 对中序遍历的思考和观察：

   > 1. **第一个访问的节点究竟是哪一个？**
   >
   >    在中序遍历中，第一个访问的节点是整个二叉树**最左侧**的节点，也就是**所有节点中深度最大的左子节点。**和先序遍历刚开始时的情况类似：在中序遍历中，先是转让“控制权”，直至转让到没有左孩子的节点。在先序遍历中，也是先向左向下移动，直到找到第一个没有左孩子的节点。
   2. “藤缠树”的思想：顺着最左侧通路，自底而上依次访问沿途各节点及其右子树。算法沿最左侧通路自底而上，以沿途各节点为界，中序遍历序列可分解为$d + 1$段，各段彼此独立，且均包括访问来自最左侧通路的某一节点$L_k$，以及遍历其对应的右子树$T_k$​。 

      ```
      inorder(T) = visit(Ld), inorder(Td);
      				visit(Ld-1),inorder(Td-1);
      					..., ...;
      						visit(L1), inorder(T1);
      							visit(L0),inorder(T0)
      ```

   3. 迭代实现：（版本1）

      ```c++
      template <typename T> //从当前节点出发，沿左分支不断深入，直至没有左分支的节点
      static void goAlongVine( BinNodePosi<T> x, Stack<BinNodePosi<T>>& S ) {
         while ( x ) { S.push( x ); x = x->lc; } //当前节点入栈后随即向左侧分支深入，迭代直到无左孩子
      }
      //需要LIFO的数据结构——栈，开口向下。
      template <typename T, typename VST> //元素类型、操作器
      void travIn_I1( BinNodePosi<T> x, VST& visit ) { //二叉树中序遍历算法（迭代版#1）
         Stack<BinNodePosi<T>> S; //辅助栈
         while ( true ) {
            goAlongVine( x, S ); //从当前节点出发，逐批入栈
            if ( S.empty() ) break; //直至所有节点处理完毕
            x = S.pop(); visit( x->data ); //弹出栈顶节点并访问之
            x = x->rc; //转向右子树（此句较为重要）
         }
      }
      ```

   4. 该算法的效率——分摊分析：

      1. `goAlongVine()`最多要调用$\Omega(n)$次，单次调用，最多需要做$\Omega(n)$次`push()`，但是，这个算法最多仅仅是$O(n)$。
      2. 每一次调用`goAlongVine()`后都恰好有一次`pop()`操作，全程不超过$O(n)$次。`push()`的次数不确定，但是，累计应该与`pop()`一样多。
      3. 在`goAlongVine()`操作中，所有的`push()`操作，就恰好对应着左侧链上的长度。这个次数不会超过所有的节点数。

   5. 延伸：对于二叉搜索树来说，中序遍历的作用至关重要。相关算法必需的一项基本操作，就是定位任一节点在中序遍历序列中的直接后继。在中序遍历意义下的直接后继就是最靠左的右后代（如果有右孩子）或者最低的左祖先（如果没有右孩子）。这样，可以实现相关的`succ()`接口：

      ```c++
      template <typename T> BinNodePosi<T> BinNode<T>::succ() { //定位节点v的直接后继
          BinNodePosi<T> s = this; //记录后继的临时变量
          if ( rc ) { //若有右孩子，则直接后继必在右子树中，具体地就是
              s = rc; //右子树中
              while ( HasLChild( *s ) ) s = s->lc; //最靠左（最小）的节点
          } else { //否则，直接后继应是“将当前节点包含于其左子树中的最低祖先”，具体地就是
              while ( IsRChild( *s ) ) s = s->parent; //逆向地沿右向分支，不断朝左上方移动
              s = s->parent; //最后再朝右上方移动一步，即抵达直接后继（如果存在）
          }
          return s;
      }
      ```

      > 1. 若当前节点有右孩子，则其直接后继必然存在，且属于其右子树。此时只需转入右子树，再沿该子树的最左侧通路朝左下方深入，直到抵达子树中最靠左（最小）的节点。
      > 2. 反之，若当前节点没有右子树，则若其直接后继存在，必为该节点的某一祖先，且是将当前节点纳入其左子树的最低祖先。于是首先沿右侧通路朝左上方上升，当不能继续前进时，再朝右上方移动一步即可。
      > 3.   作为后一情况的特例，出口时`s`可能为`NULL`。这意味着此前沿着右侧通路向上的回溯，抵达了树根。也就是说，当前节点全树右侧通路的终点——它也是中序遍历的终点，没有后继。

      对上述迭代版本的代码，使用`succ()`接口进行改写，得到新的版本。我们可以借助该版本，实现需要空间更少的算法。

      版本3：以上的迭代式遍历算法都需使用辅助栈，尽管这对遍历算法的渐进时间复杂度没有实质影响，但所需辅助空间的规模将线性正比于二叉树的高度，在最坏情况下与节点总数相当。为此，可对代码继续改进，借助`BinNode`对象内部的`parent`指针。该版本无需使用任何结构，总体仅需O(1)的辅助空间，属于就地算法。当然，因需要反复调用`succ()`，时间效率有所倒退。

      ```c++
      template <typename T, typename VST> //元素类型、操作器
      void travIn_I3( BinNodePosi<T> x, VST& visit ) { //二叉树中序遍历算法（迭代版#3，无需辅助栈）
         bool backtrack = false; //前一步是否刚从左子树回溯——省去栈，仅O(1)辅助空间
         while ( true ){
            if ( !backtrack && HasLChild( *x ) ) //若有左子树且不是刚刚回溯，则
               x = x->lc; //深入遍历左子树
            else { //否则——无左子树或刚刚回溯（相当于无左子树）
               visit( x->data ); //访问该节点
               if ( HasRChild( *x ) ) { //若其右子树非空，则
                  x = x->rc; //深入右子树继续遍历
                  backtrack = false; //并关闭回溯标志
               } else { //若右子树空，则
                  if ( !( x = x->succ() ) ) break; //回溯（含抵达末节点时的退出返回）
                  backtrack = true; //并设置回溯标志
               }
            }
         } 
      }
      ```
      
      

### 后序遍历(Postorder Traversal)

1. 在后序遍历中，我们首先递归地做左子树和右子树的后序遍历，然后访问根节点。

2. 应用：计算目录及其子目录中的文件所用空间。

3. 递归实现：

   ```c++
   //c++
   template <typename T, typename VST> //元素类型、操作器
   void travIn_R ( BinNodePosi<T> x, VST& visit ) { //二叉树中序遍历算法（递归版）
       if ( !x ) return;
       travIn_R ( x->lc, visit );
       travIn_R ( x->rc, visit );//两个都不是尾递归
       visit ( x->data );
   }
   ```

   ```python
   # python
   class TreeNode:
       def __init__(self, x):
           self.val = x
           self.left = None
           self.right = None
   
   def postorder_traversal(root):
       if root is None:
           return
   
       postorder_traversal(root.left)  # 遍历左子树
       postorder_traversal(root.right)  # 遍历右子树
       print(root.val)  # 访问根节点
   ```

   

4. **“藤缠树”**的思想：从根出发下行，对于**每一个节点**，如果其有左孩子，就继续向左分支；如果其没有左孩子，则向右侧分支。注意：向左分支是**尽可能**的行为。如果对于某一个节点来说，其既没有左孩子，又没有右孩子，则`visit()`开始执行。所以，在后序遍历的迭代版本中，最**靠左的叶子节点**（中序遍历中次序最靠左者）是**第一个被访问**的节点。这里，树的藤上依然会挂上某些右子树，但是不是所有的节点都挂上了右子树，因为对于某些只有右孩子的节点来说，它的右子树在藤上面。

5. **最高左侧可见叶节点(Highest Leaf Visible From Left, HLVFL)**：如果我们将树`T`画在一个二维平面上，并假设所有节点和边均不透明。于是从左侧水平向右看去，未被遮挡的最高叶节点`v`，称作最高左侧可见叶节点。注意，该节点既可能是左孩子，又可能是右孩子。

   ```
       1
      / \
     2   3
    / \ / \
   4  5 6  7
        / \
       8   9
   ```

   入栈示意图，左侧是栈顶：`1->45231->5231->231->896731->96731->6731->731->31->1->Null`

   其后序遍历序列为`4, 5, 2, 8, 9, 6, 7, 3, 1`。

6. 迭代实现：

   ```c++
   template <typename T> //在以S栈顶节点为根的子树中，找到最高左侧可见叶节点
   static void gotoLeftmostLeaf( Stack<BinNodePosi<T>>& S ) { //沿途所遇节点依次入栈
      while ( BinNodePosi<T> x = S.top() ) //自顶而下，反复检查当前节点（即栈顶）
         if ( HasLChild( *x ) ) { //尽可能向左
            if ( HasRChild( *x ) ) S.push( x->rc ); //若有右孩子，优先入栈
            S.push( x->lc ); //然后才转至左孩子
         } else //实不得已
            S.push( x->rc ); //才向右
      S.pop(); //返回之前，弹出栈顶的空节点
   }
   
   template <typename T, typename VST>
   void travPost_I( BinNodePosi<T> x, VST& visit ) { //二叉树的后序遍历（迭代版）
      Stack<BinNodePosi<T>> S; //辅助栈
      if ( x ) S.push( x ); //根节点入栈
      while ( !S.empty() ) { // x始终为当前节点
         if ( S.top() != x->parent ) ////若栈顶非x之父（而为右兄）
            gotoLeftmostLeaf( S ); //则在其右兄子树中找到HLVFL（相当于递归深入）
         x = S.pop(); 
         visit( x->data ); //弹出栈顶（即前一节点之后继），并访问之
      }
   }
   ```

7. 时间复杂度：仍然是线性的时间复杂度$O(n)$。使用摊还分析中的**聚集分析**和**记账法**可以进行分析。 

   > 对于后序遍历二叉树，我们可以将其看作是三个操作的集合：
   >
   > 访问左子树、访问右子树、访问当前节点。
   >
   > 每个操作都会被执行 n 次，其中 n 是二叉树的节点数量。因此，整个后序遍历的时间复杂度是 $O(n)$。
   >
   > 这是因为无论二叉树的形状如何（即使是最坏的情况，也就是所有的节点都只有左子节点或只有右子节点，形成一条线），后序遍历都需要访问每个节点一次。因此，总的操作次数是 n，所以时间复杂度是$ O(n)$。

8. 后序遍历的实际应用：表达式树、逆波兰表达式

   1. 在遇到一个中缀表达式的时候，我们先通过添加适当的括号来直接表明各个运算符的优先级。
   2. 按照左括号向下，右括号向上的原则（这样做可以区分出层次结构，便于直观地进行计算和构建树），对该中缀表达式进行排列。
   3. 保留数字和运算符的位置不变，使用恰当的连接线，将树构建出来。这里，每一个数字都是叶子节点，而运算符则为内部节点。
   4. 然后进行对构建好的表达式树的后序遍历。注意，双目运算符下面必有左孩子和右孩子，而单目运算符下面只有左孩子。
   5. 按照运算规则进行后序遍历，即可得到最终答案。（后序遍历的序列就是RPN）

### 层次遍历(Traversal by Level)

1. 它按照**树的层次**从上到下，同一层次从左到右的顺序访问每一个节点。具体来说，首先访问根节点，然后访问所有的第二层节点（即根节点的直接子节点），接着访问所有的第三层节点（即第二层节点的直接子节点），以此类推，直到所有的节点都被访问过。这种遍历方式常常用于**广度优先搜索（Breadth-First Search，BFS）算法**，因为它首先访问的是离根节点最近的节点。
2. 使用**队列**进行层次遍历：

```c++
template <typename T> template <typename VST> //元素类型、操作器
void BinNode <T>::travLevel( VST& visit ) { //二叉树层次遍历算法
   Queue<BinNodePosi<T>> Q; 
   Q.enqueue( this ); //引入辅助队列，根节点入队
   while ( !Q.empty() ) { //在队列再次变空之前，反复迭代
      BinNodePosi<T> x = Q.dequeue(); visit( x->data ); //取出队首节点并访问之
      if ( HasLChild( *x ) ) Q.enqueue( x->lc ); //左孩子入队
      if ( HasRChild( *x ) ) Q.enqueue( x->rc ); //右孩子入队
   }
}
```

