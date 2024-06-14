# 无序列表

操作仍是下面的几种：增、删、改、查。

## 插入与删除

1. 因为我们引入了两个哨兵结点，所以我们的插入算法和删除算法变得十分简单。

   ```c++
   //前插入（在this前插入）算法，后插入对称即可。：
   template<typename T> ListNodePosi<T> ListNode<T>::insertAsPred(T const& e){//O(1)
       ListNodePosi<T> x = new ListNode( e, pred, this ); //创建新结点，其前驱是pred，后继是this
       pred->succ = x; pred = x; //次序不可颠倒
       return x; //建立链接，返回新节点的位置
   } //得益于哨兵，即便this为首节点亦不必特殊处理——此时等效于insertAsFirst(e)
   ```

   

   ```c++
   template <typename T> T List<T>::remove( ListNodePosi<T> p ) { //删除合法节点p
       T e = p->data; //备份待删除节点存放的数值（设类型T可直接赋值）
       p->pred->succ = p->succ;   
       p->succ->pred = p->pred;  //短路联接
       delete p; _size--; return e; //返回备份的数值
   } //O(1)
   ```

   时间复杂度：$O(1)$。

   > （C++）析构函数`List<T>::~List()`：大致可以分两步：1.清空列表；2.释放头尾哨兵节点。
   >
   > 其中，清空列表的操作是反复删除首节点，使得列表变空；
   >
   > ```c++
   > int List<T>::clear(){
   > 	int oldSize = _size;
   > 	while(0 < _size)
   > 		remove(header->succ);
   > 	return oldSize;
   > }
   > ```
   >
   > 待上述操作完成之后，执行`delete header; delete trailer`即可。
   >
   > 析构函数的时间复杂度：$O(n)$。

## 查找算法

1. 这里将查找算法定义为在`p`的$n$ 个前驱中，找到某一元素。函数语句：`find(T const &e, Rank n, ListNodePosi<T> p);`。当然，我们也可以实现在它的$n$个后继中，查找到某一元素。

2. 算法的基本思路是从`p`出发，自后向前逐个比对，若匹配成功，则返回该节点；否则，则返回`NULL`。

3. 时间复杂度：$O(n)$。

## 去重算法

和在向量中的去重算法实现类似：对于任何一个节点，在保证前序的所有节点所存放的元素没有重复的前提下（使用从前到后依次查找即可实现该步骤），查找其前驱是否有重复的元素（使用`find`函数），若有，则使用`remove()`方法删除该节点，若无，则秩加一。（此时，该节点成为了唯一性的节点，也同时成为了下一个待去重节点的前驱节点。）返回列表规模的变化量，即被删除元素的总数。
