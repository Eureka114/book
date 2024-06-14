# 有序向量

## 何谓有序？

必须让元素按顺序排序。这里就涉及到有序性的甄别操作（从冒泡排序模板改进）

1. 相邻逆序对的数目，可用来度量向量的逆序程度。

   ```c++
   template <typename T> void checkOrder ( Vector<T> & V ) { //通过遍历
   	int unsorted = 0; V.traverse( CheckOrder<T>(unsorted, V[0]) ); //统计紧邻逆序对
   	if ( 0 < unsorted )
   		printf ( "Unsorted with %d adjacent inversion(s)\n", unsorted );
   	else
   		printf ( "Sorted\n" );
   }
   ```

2. 算法效率过于低下：因为在有序向量中，重复的元素必然相互紧邻构成一个区间，则每一个区间只需要保留单个元素即可。下面算法的时间复杂度为$O(n^2)$，与无序向量的唯一化操作相同，毫无效率可言。即**逐一移动**。

   ```c++
   template <typename T> Rank Vector<T>::uniquify() { //有序向量重复元素剔除算法（低效版）
      Rank oldSize = _size, i = 1; //当前比对元素的秩，起始于首元素
      while ( i < _size ) //从前向后，逐一比对各对相邻元素
         _elem[i - 1] == _elem[i] ? remove ( i ) : i++; //若雷同，则删除后者；否则，转至后一元素
      return oldSize - _size; //向量规模变化量，即被删除元素总数
   }
   ```

3. 改进的高效版本：使用两个指针，一个用来遍历元素，另一个则用来记录所有互异元素的数量。因为0号元素在其和其前驱内必然唯一，先令`i=0`。指针`j`开始后移，当找到第一个与前面互异的元素时，将第`j`号元素赋值给第`i+1`号元素（此时，必然有$i+1\le j$​）。重复上述操作，直至`j`移动至向量末尾，后将返回总删除个数，即`j-i`。即**不移动元素，最后批量删除**。共$n-1$次迭代，而每一次只有常数时间，故时间复杂度为$O(n)$。

   ```c++
   template <typename T> Rank Vector<T>::uniquify() { //有序向量重复元素剔除算法（高效版）
      Rank i = 0, j = 0; //各对互异“相邻”元素的秩
      while ( ++j < _size ) //逐一扫描，直至末元素
         if ( _elem[i] != _elem[j] ) //跳过雷同者
            _elem[++i] = _elem[j]; //发现不同元素时，向前移至紧邻于前者右侧
      _size = ++i; shrink(); //直接截除尾部多余元素（这一步表明后续重复的元素直接不参与向量的计数，即间接“删除”了他们）
      return j - i; //向量规模变化量，即被删除元素总数
   }
   ```

   > 不用考虑因为互异的元素太多而导致初始的靠前的互异元素被删除的情况，因为在到达这一步之前，已经在他的前面“拷贝”了一份副本。

## 查找

1. 语义约定：至少也应该便于向量的维护，以满足某些操作，如`vec.insert(1+vec.search(e),e)`。
2. 约定返回值：在`[lo,hi)`中返回不大于该值的最后一个元素。当有多个命中元素时，必须返回最靠后（秩最大）者；失败时，应返回小于e的最大者（含哨兵`lo-1`）。即：若e在$(-\infty,v[lo])$中，则返回左侧哨兵：若e在$(v[hi-1],\infty)$，则返回hi-1（末位元素是右侧哨兵的左邻）。
3. 二分查找：

   1. 减而治之：
      1.  e < x：则e若存在必属于左侧子区间，故可（减除[mi,hi)并）递归深入[lo, mi)
      2.  x < e：则e若存在必属于右侧子区间，亦可（减除[lo,mi]并）递归深入(mi, hi)
      3.  e = x：已在此处命中，可随即返回 //若有多个，返回何者？


   2. 代码实现（时间复杂度$T(n)=T(n/2)+O(1)=O(\log n)$，成功和失败的平均查找长度均大致为$O(1.5\log n)$，证明使用递推法，见教材第50页）：

      ```c++
      //二分查找算法（版本A）：在有序向量的区间[lo, hi)内查找元素e，0 <= lo <= hi <= _size
      template <typename T> static Rank binSearch( T* S, T const& e, Rank lo, Rank hi ) {
         while ( lo < hi ) { //每步迭代可能要做两次比较判断，有三个分支
            /*DSA*/ for ( int i = 0; i < lo; i++ ) 
                printf ( "     " ); 
            if ( lo >= 0 ) 
                 for ( int i = lo; i < hi; i++ ) 
                     printf ( "....^" );
            printf ( "\n" );
            Rank mi = ( lo + hi ) >> 1; //以中点为轴点（区间宽度折半，等效于其数值表示的右移一位）
            if( e < S[mi] ) hi = mi; //深入前半段[lo, mi)继续查找
            else if ( S[mi] < e ) lo = mi + 1; //深入后半段(mi, hi)继续查找
            else return mi; //在mi处命中
            if ( lo >= hi ) {
                 for ( int i = 0; i < mi; i++ ) 
                     printf ( "     " ); 
                 if ( mi >= 0 ) 
                     printf ( "....|\n" ); 
                 else printf ( "<<<<|\n" ); }
         } //成功查找可以提前终止
         return -1; //查找失败
      } //有多个命中元素时，不能保证返回秩最大者；查找失败时，简单地返回-1，而不能指示失败的位置
      ```

## Fibonacci查找（二分查找的改进1）

1. 转向左、右分支前的关键码比较次数不等，而递归深度却相同；通过递归深度的不均衡对转向成本的不均衡做补偿，平均查找长度应能进一步缩短！如有$n=fib(k-1)$，则可以取$mi=fib(k-1)-1$，将子向量分为三部分：前$fib(k-1)-1$，命中“1”，后$fib(k-2)-1$。
2. 代码实现：

   ```c++
   template <typename T> static Rank fibSearch( T * S, T const & e, Rank lo, Rank hi ) {//0 <= lo <= hi <= _size
   	for ( Fib fib(hi - lo); lo < hi; ) { //Fib数列制表备查
           while ( hi - lo < fib.get() ) 
               fib.prev(); //自后向前顺序查找轴点（分摊O(1)）
           Rank mi = lo + fib.get() - 1; //确定形如Fib(k)-1的轴点
           if ( e < S[mi] ) 
               hi = mi; //深入前半段[lo, mi)
           else if ( S[mi] < e ) 
               lo = mi + 1; //深入后半段(mi, hi)
           else return mi; //命中
   	}
   	return -1; //失败
   } //有多个命中元素时，不能保证返回秩最大者；失败时，简单地返回-1，而不能指示失败的位置
   ```
3. $\phi=\frac{\sqrt(5)-1}{2}=0.6180339...$
4. 通用策略：在任何区间`A[0,n)`内，总是选取`A[`$\lambda$` n]`作为轴点，

   1. 比如：二分查找对应于$\lambda=0.5$，Fibonacci查找对应于$\lambda=\phi$。
   1. 这类查找算法的渐近复杂度为$\alpha(\lambda)\cdot\log_2 n=O(\log n)$，常系数$\alpha(\lambda)$何时达到最小...
   1. 递推式：（加权平均的思想，并加上成本`->{1,2}`）

$$
\alpha(\lambda) \cdot \log _{2} n=\lambda \cdot\left[1+\alpha(\lambda) \cdot \log _{2}(\lambda n)\right]+(1-\lambda) \cdot\left[2+\alpha(\lambda) \cdot \log _{2}((1-\lambda) n)\right]
$$

   ​整理，有：


$$
\frac{-\ln 2}{\alpha(\lambda)}=\frac{\lambda \cdot \ln \lambda+(1-\lambda) \cdot \ln (1-\lambda)}{2-\lambda}
$$


当  $\lambda=\phi=\dfrac{\sqrt{5}-1}2$  时，  $\alpha(\lambda)=1.440420 \ldots$  达到最小。

## 二分查找的改进2

1. 每次迭代仅使用1次比较，则所有分支只有2个方向，而不再是3个。

2. 代码实现：

   ```c++
   //二分查找算法（版本B）：在有序向量的区间[lo, hi)内查找元素e，0 <= lo < hi <= _size
   template <typename T> static Rank binSearch( T * S, T const & e, Rank lo, Rank hi ) {
      while ( 1 < hi - lo ) { //有效查找区间的宽度缩短至1时，算法才终止
         Rank mi = (lo + hi) >> 1; //以中点为轴点，经比较后确定深入[lo, mi)或[mi, hi)
         e < S[mi]  ?  hi = mi  :  lo = mi;
      } //出口时hi = lo + 1
      return e == S[lo]  ?  lo : -1 ; //有多个命中元素时，不能保证返回秩最大者；查找失败时，简单地返回-1，而不能指示失败的位置
   }
   ```

3. 上述的所有操作，均没有实现第2点中的“语义约定”。

## 二分查找的改进3

1. 代码实现：

   ```c++
   //版本C
   template <typename T> static Rank binSearch( T * S, T const & e, Rank lo, Rank hi ) {
      while ( lo < hi ) { //不变性：[0, lo) <= e < [hi, n)
         Rank mi = (lo + hi) >> 1;
         e < S[mi]  ?  hi = mi  :  lo = mi + 1; //[lo, mi)或(mi, hi)，[mi]或被遗漏？
      } //出口时，区间宽度缩短至0，且必有[lo = hi] = M
      return lo - 1; //至此，[lo]为大于e的最小者，故[lo-1] = m即为不大于e的最大者
   } //留意与版本B的差异
   ```
   > 也可参考Python中的`bisect_left()`函数。
2. 与版本B的差异：
   1. 待查找区间宽度缩短至0而非1时，算法才结束
   2. 转入右侧子向量时，左边界取作`mi+1`而非`mi`——`A[mi]是否会被遗漏？`不会
   3. 无论成功与否，返回的秩严格符合接口的语义约定。
3. 版本C的正确性：

   1. 单调性显而易见。
   2. 不变性：`A[0,lo)<=e<A[hi,n)`（其中，e是欲查找的元素）
      1. 初始时，lo=0,  hi=n, 自然成立。
      2. 数学归纳：假设不变性一直保持至某一次执行完成，对于下一次执行，以下分为两种情况：
         - 当`e<A[mi]`时，由于之前有`e<A[hi]`成立，且向量有序，则可以将`hi`的范围拓展成下界为`mi`,而保证初始条件不变。即`e<A[hi]`和`e<(A[mi]~A[hi])`$\Rightarrow$`e<[mi]`。
         - 当`A[mi]≤e`时，与上述同理，所以可以将`lo`的上界拓展成`mi+1`，以保证在`[0,lo-1)`(`[0,lo)`)中，均满足`A[0,lo)≤e`。

## 此外……

还有插值查找等内容，本质上是通过猜测轴点`mi`，提高收敛速度。而在实际上的算法实现中，往往是“算法接力”。首先通过插值查找将范围迅速缩小，然后再使用二分查找，进一步缩小范围，最后使用顺序查找。
