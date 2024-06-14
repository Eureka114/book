# 二叉搜索树（二叉排序树）
## 循关键码访问
1. 二叉搜索树（Binary Search Tree），也称二叉排序树（Binary Sort Tree）。它或者是一棵空树，或者是具有下列性质的二叉树：
> 1. 若左子树不空，则左子树上所有结点的关键码均小于根结点的关键码；
> 2. 若右子树不空，则右子树上所有结点的关键码均大于根结点的关键码；
> 3. 左、右子树本身又各是一棵二叉搜索树。

2. 循关键码访问（Call-by-key）
    1. 向量、列表并不能兼顾静态查找与动态修改。能否兼顾二者的优点？
    2. 使用键值对（Key-Value）：各数据项依所持关键码而彼此区分。当然，关键码之间也必须同时地支持比较大小和比对是否相等。数据集中的数据项，统一地表示和实现为词条（Entry）形式。
    3. 这里再次强调，为了与有序词典结构的定义一致，这里并不要求二分查找树中各节点的关键码互异。

3. BST的性质：

    1. 顺序性：任一节点均不小于/不大于其左/右后代。（其只是一种局部性的特征，但却可导出BST的整体特征）
    2. 单调性：二叉树T为二分查找树，当且仅当其中序遍历序列是单调非降的。
    3. 证明：考查二叉搜索树中的任一节点r。按照中序遍历的约定，r左（右）子树中的节点（若存在）均应先于（后于）r接受访问。按照二叉搜索树的定义，r左（右）子树中的节点（若存在）均不大于（不小于）r，故中序遍历序列必然在r处单调非降；反之亦然。鉴于以上所取r的任意性，题中命题应在二叉搜索树中处处成立。

4. 思考：BST的定义能否被修改？

    > 二叉搜索树的定义不能将原定义中的“**左（右）后代**”，替换为“**左（右）孩子**”。因为这样的定义会导致二叉搜索树的性质丧失，例如，下图中的二叉树是二叉搜索树。如按照修改后的定义，把图中的3改成30，虽然满足定义2，但已经不是二叉搜索树了。
    >
    > ![](https://blog.nanpuyue.com/usr/uploads/2019/05/487643383.svg)

## BST类的实现

1. BST的实现主要由两部分组成：树节点类BinNode和二叉搜索树类BST。
2. Python实现：

   ```python
   class Node:
       def __init__(self, key):
           self.left = None
           self.right = None
           self.val = key

   class BST:
       def __init__(self):
           self.root = None

       def insert(self, key):
           if self.root is None:
               self.root = Node(key)
           else:
               self._insert(self.root, key)

       def _insert(self, node, key):
           if key < node.val:
               if node.left is None:
                   node.left = Node(key)
               else:
                   self._insert(node.left, key)
           else:
               if node.right is None:
                   node.right = Node(key)
               else:
                   self._insert(node.right, key)

       def inorder(self, node):
           if node:
               self.inorder(node.left)
               print(node.val)
               self.inorder(node.right)
   ```

3. Java实现：

   ```java
   class Node {
       int key;
       Node left, right;
   
       public Node(int item) {
           key = item;
           left = right = null;
       }
   }
   
   class BST {
       Node root;
   
       BST() {
           root = null;
       }
   
       void insert(int key) {
           root = insertRec(root, key);
       }
   
       Node insertRec(Node root, int key) {
           if (root == null) {
               root = new Node(key);
               return root;
           }
           if (key < root.key)
               root.left = insertRec(root.left, key);
           else if (key > root.key)
               root.right = insertRec(root.right, key);
           return root;
       }
   
       void inorder()  {
           inorderRec(root);
       }
   
       void inorderRec(Node root) {
           if (root != null) {
               inorderRec(root.left);
               System.out.println(root.key);
               inorderRec(root.right);
           }
       }
   }
   ```
   
4. C++实现：

   ```cpp
   #include <iostream>
   
   struct Node {
       int key;
       struct Node *left, *right;
   };
   
   struct Node *newNode(int item) {
       struct Node *temp = new Node;
       temp->key = item;
       temp->left = temp->right = NULL;
       return temp;
   }
   
   void inorder(struct Node *root) {
       if (root != NULL) {
           inorder(root->left);
           std::cout << root->key << std::endl;
           inorder(root->right);
       }
   }
   
   struct Node* insert(struct Node* Node, int key) {
       if (Node == NULL) return newNode(key);
       if (key < Node->key)
           Node->left  = insert(Node->left, key);
       else if (key > Node->key)
           Node->right = insert(Node->right, key);
       return Node;
   }
   ```
   
   