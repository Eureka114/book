# 列表基础与ADT

## 列表：循位置访问

1. 根据是否修改数据结构，所有操作大致分为两类方式：静态和动态。
   1. 静态： 仅读取，数据结构的内容及组成一般不变：get$O(1)$、search$O(\log n)$;
   2. 动态： 需写入，数据结构的局部或整体将改变：put、insert、remove$O(n)$.
2. 与操作方式相对应地，数据元素的存储与组织方式也分为两种，即静态和动态。
   1. 静态： 数据空间整体创建或销毁，数据元素的物理次序与其逻辑次序严格一致；可支持高效的静态操作。比如向量，元素的物理地址与其逻辑次序线性对应。
   2. 动态： 为各数据元素动态地分配和回收的物理空间。相邻元素记录彼此的物理地址，在逻辑上形成一个整体；可支持高效的动态操作。
3. 列表（list是采用动态储存策略的典型结构。
   1. 其中的元素称作节点（node），通过指针或引用彼此联接。
   2. 在逻辑上构成一个线性序列$L={a_0,a_1,\ldots,a_n}$.
   3. 相邻节点彼此互称前驱（predecessor）或后继（successor）。
   4. 没有前驱/后继的节点称作首（first/front）/末（last/rear）节点。
4. Call-By-Position:列表的循位置访问
   1. 列表中各元素的物理地址将不再决定于逻辑次序，动态操作可以在局部完成，复杂度有望控制在$O(1)$
   2. Call-By-Position：利用节点之间的相互引用，找到特定的节点。（如：我的...朋友A的...亲戚B的...同事C的...战友D的...同学Z）
   3. 如果是按逻辑次序的连续访问，单次也是$O(1)$

## 列表ADT

1. 列表节点：作为列表的基本元素，列表节点首先需要独立地“封装”实现。

   > list~node~data类似于列车~车厢~货物

2. 列表节点的ADT：

   ```c++
   template <typename T> using ListNodePosi = ListNode<T>*; //列表节点位置（C++.0x）
   template <typename T> struct ListNode { //简洁起见，完全开放而不再严格封装
       T data; //数值
       ListNodePosi<T> pred; //前驱
       ListNodePosi<T> succ; //后继
       ListNode() {} //针对header和trailer的构造
       ListNode(T e, ListNodePosi<T> p = NULL, ListNodePosi<T> s = NULL)
       : data(e), pred(p), succ(s) {} //默认构造器
       ListNodePosi<T> insertAsPred( T const & e ); //前插入
       ListNodePosi<T> insertAsSucc( T const & e ); //后插入
   };
   ```

3. 列表模板类：

   ```c++
   #include "listNode.h" //引入列表节点类
   template <typename T> class List { //列表模板类
       private: Rank _size; ListNodePosi<T> header, trailer; //哨兵
       protected: /* ... 内部函数 */
       public: //构造函数、析构函数、只读接口、可写接口、遍历接口
   };
   ```

   > 这里，我们把节点分成以下几种：头结点、尾结点、首结点、末结点、中间结点。其中，头、尾结点为哨兵结点，它们具有`private`属性，对外部不可见。首结点认为是对外部可见的结点中的前驱为头结点的结点，末结点认为是对外部可见的结点中的后继为尾结点的结点。一个列表中不一定有首、末结点，也不一定首、末结点同时出现。头、首、末、尾节点的秩，可分别理解为-1、0、n-1、n。

4. 构造：

   ```c++
   template <typename T> void List<T>::init() { //初始化，创建列表对象时统一调用
       header  = new ListNode<T>;
       trailer = new ListNode<T>;
       header->succ = trailer; header->pred = NULL;
       trailer->pred = header; trailer->succ = NULL;
       _size = 0;
   }
   ```

5. 此时可以重载下标运算符，模仿向量的Call-By-Rank访问方式。时间复杂度$O(r)$，当均匀分布时，期望复杂度为$(1+2+3+\ldots+n)/n=O(n)$。