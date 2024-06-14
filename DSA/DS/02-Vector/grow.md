

# 可变长数组——向量的扩充算法

## 空间管理

1. 若采用静态空间管理策略，容量_capacity固定，则有明显的不足……

   - 上溢(Overflow)：_elem[]不足以存放所有元素，尽管此时系统往往仍有足够的空间>
   - 下溢(Underflow): _elem[]存放的内容过少，装填因子$\lambda=\dfrac{\text{\_size}}{\text{\_ capacity}}<<50\%$
   - 而且，一般的应用环境中，难以准确预测空间的需求量。所以，我们转向动态空间管理策略……

2. 动态空间管理：和蝉蜕壳时类似，在向量即将上溢时，适当扩大内部数组的容量。扩容算法如下（Doubling方法）：

   ```C++
   template <typename T> void Vector<T>::expand() { //向量空间不足时扩容
   	if ( _size < _capacity ) return; //尚未满员时，不必扩容
   	_capacity = max( _capacity, DEFAULT_CAPACITY ); //不低于最小容量
   	T* oldElem = _elem;
   	_elem = new T[ _capacity <<= 1 ]; //容量加倍
   	for ( Rank i = 0; i < _size; i++ ) //复制原向量内容
   		_elem[i] = oldElem[i]; //T为基本类型，或已重载赋值操作符'='
   	delete [] oldElem; //释放原空间
   } //得益于向量的封装，尽管扩容之后数据区的物理地址有所改变，却不致出现野指针
   ```

   ```Java
   private void grow(int minCapacity) { 
       int oldCapacity = elementData.length; //10
       int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                        capacityIncrement : oldCapacity);
       if (newCapacity - minCapacity < 0)
           newCapacity = minCapacity;
       if (newCapacity - MAX_ARRAY_SIZE > 0)
           newCapacity = hugeCapacity(minCapacity);
       elementData = Arrays.copyOf(elementData, newCapacity);
   }
   ```

## 向量扩充——Doubling方法和Increment方法

1. 除了倍增外，还可以每次扩容时追加同样大小的空间，代码修改略。

2. 效率：

   1. Increment递增策略：最坏情况：在初始容量为0的空向量中，连续插入$n=m\cdot I >>2$个元素，不做删除，则在第$kI+1$次插入时，都要扩容，若不急申请空间的操作，各次扩容过程中的复制原向量的时间成本依次为$0,I,2I,3I,\cdots,(m-1)I$，总耗时$O(n^2)$，每次的分摊成本为$O(n)$。

   2. Doubling加倍策略：最坏情况：在初始容量1的满向量中，连续插入$n=2^m >>2$个元素，而无删除操作，则在第$2^k$次插入时，都要扩容。各次扩容过程中复制原向量的时间成本依次为$1,2,4,8,16,\cdots,2^{m-1},(2^m=n)$，总耗时$O(n)$，每次的分摊成本为$O(1)$。

   3. 倍增策略在空间上做了一些损失，而换取了时间上的巨大提升。

      

   ## 平均分析和分摊分析

   |          平均复杂度(Average Complexity)          |          分摊复杂度(Amortized Complexity)          |
   | :----------------------------------------------: | :------------------------------------------------: |
   | 根据各种操作出现概率的分布，将对应的成本加权平均 | 连续实施的足够多次操作，所需总体成本摊还至单次操作 |
   |       各种可能的操作，作为独立事件分别考查       |     从实际可行的角度，对一系列操作做整体的考量     |
   |          割裂了操作之间的相关性和连贯性          |         更加忠实地刻画了可能出现的操作序列         |
   |    往往不能准确地评判数据结构和算法的真实性能    |       更为精准地评判数据结构和算法的真实性能       |

   