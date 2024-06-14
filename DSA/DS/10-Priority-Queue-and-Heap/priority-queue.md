# 优先队列

1. 一种特殊的队列。在优先队列中，元素被赋予优先级，当访问队列元素时，具有最高优先级的元素最先删除。

   1. 优先队列与普通队列最大的不同点在于出队顺序。
   2. 普通队列的出队顺序跟入队顺序相关，符合「先进先出（First in, First out）」的规则。
   3. 优先队列的出队顺序跟入队顺序无关，优先队列是按照元素的优先级来决定出队顺序的。优先级高的元素优先出队，优先级低的元素后出队。优先队列符合 **「最高级先出（First in, Largest out）」** 的规则。

2. 优先队列的应用场景非常多，比如：

   - **数据压缩**：赫夫曼编码算法；
   - **最短路径算法**：Dijkstra 算法；
   - **最小生成树算法**：Prim 算法；
   - **任务调度器**：根据优先级执行系统任务；
   - **事件驱动仿真**：顾客排队算法；
   - **排序问题**：查找第 k 个最小元素。

3. 关键码、比较器与偏序关系

   1. 词条(Entry)：仿照词典结构，我们也将优先级队列中的数据项称作词条。

   2. 关键码(Key)：与特定优先级相对应的数据属性，也称作关键码。注意：关键码之间必须可以比较大小。作为优先队列的一个基本要求，在关键码之间必须能够定义**某种全序关系**(Total Order Relation)。该关系必须满足下述三条性质：

      > 自反性：对于任意关键码`k`，都有`k ≤ k `/可比较性：对于任意的关键码`x,y`均有`x ≤ y`或`y ≤ x`
      >
      > 反对称性：若`k1 ≤ k2`且`k2 ≤ k1`，则`k1 = k2`
      >
      > 传递性：若`k1 ≤ k2`且`k2 ≤ k3`，则`k1 ≤ k3 `

   

4. Entry接口：

   ```Java
   public interface Entry<K,V>{
   	K getKey();
       void setKey(K key);
   	V gatValue();
       void setValue(V value);
   }
   ```

   > 1. In our lecture, we assume that an element's key remains fixed once it has been added to a priority queue.
   > 2. Fixed keys does **not mean** fixed order.
   > 3. The priority (order) of a job is not fixed, may change when the whole set of jobs change in the queue. This is meaning that jobs need to be **reordered**.

5. 比较器：

   1. 与C++不同，Java不支持对比较操作符`> < ==`的重载。我们需要基于某种`Comparable`接口实现一个关键码类，并将所有通常的比较方法封装起来，以支持关键码之间的比较。但是，如果按照关键码本身的类型来决定关键码的比较方式，那么在很多情况下，这并不能最终决定关键码的**具体**比较方式。如`3`和`12`，按照整型和字符串类型（字典序）比较，会得到不同的结果。

   2. 不如基于`Comparator`接口专门实现一个独立于关键码之外的比较器类，由它来确定具体的比较规则。在创建每个优先队列时，只要指定这样一个比较器对象，即可按照该比较器确定的规则，在此后进行关键码的比较。这一策略的另一优点在于，一旦不想继续使用原先的比较器对象，可以随时用另一个比较器对象将其替换掉，而不用重写优先队列本身。 

   3. 代码实现：

      ```java
      public interface Comparator{
      	public int compare(Object a, Object b);
      }
      ```

      每一次进行比较时，基于该接口实现需要的比较类和方法。

      默认情况下的比较器：

      ```java
      public class ComparatorDefault implements Comparator { //实现该类
          public ComparatorDefault() {} 
          public int compare(Object a, Object b) throws ClassCastException { 
          	return ((Comparable) a).compareTo(b); //实现该方法
          } 
      } 
      ```

6. 优先队列的ADT及代码实现：

   1. 操作接口：

   | 操作接口   | 功能描述                                 |
   | ---------- | ---------------------------------------- |
   | `size()`   | 报告优先级队列的规模，即其中词条的总数   |
   | `insert()` | 将指定词条插入优先级队列                 |
   | `getMax()` | 返回优先级最大的词条（若优先级队列非空） |
   | `delMax()` | 删除优先级最大的词条（若优先级队列非空） |

   2. `Java`接口：

      ```java
      public interface PQueue { 
          //统计优先队列的规模 
          public int getSize(); 
          //判断优先队列是否为空 
          public boolean isEmpty(); 
          //若Q非空，则返回其中的最小条目（并不删除）;否则，报错 
          public Entry getMin() throws ExceptionPQueueEmpty; 
          //将对象obj与关键码k合成一个条目，将其插入Q中，并返回该条目 
          public Entry insert(Object key, Object obj) throws ExceptionKeyInvalid; 
          //若Q非空，则从其中摘除关键码最小的条目，并返回该条目；否则，报错 
          public Entry delMin() throws ExceptionPQueueEmpty; 
      }
      ```

   3. 基于优先队列的排序器：

      ```java
      public class Sorter_PQueue implements Sorter {
          private Comparator C;
          public Sorter_PQueue()
         		{ this(new ComparatorDefault()); }
          public Sorter_PQueue(Comparator comp)
         		{ C = comp; }
          public void sort(Sequence S) {
              Sequence T = new Sequence_DLNode();//为批处理建立优先队列而准备的序列
              while (!S.isEmpty()) {//构建序列T
                  Object e = S.removeFirst();//逐一取出S中各元素
                  T.insertLast(new EntryDefault(e, e));//用节点元素本身作为关键码
              }
              PQueue pq = new PQueue_Heap(C, T);
              while(!pq.isEmpty()) {//从优先队列中不断地
                  S.insertLast(pq.delMin().getValue());//取出最小元素，插至序列末尾
                  System.out.println("\t:\t" + S.last().getElem());
              }
          }
      }//每一次都要排序
      ```

7. 用向量和列表实现优先队列：

   1. 使用向量实现优先队列：（不推荐）

   2. 使用无序列表实现优先队列：

      思想：为了插入一个被赋予关键码`key`的对象`obj`，我们首先创建一个条目对象`entry = (key, obj)`，然后调用`insertLast(entry)`方法将其接至列表尾部。时间复杂度$O(1)$。这样构造的列表是无序的，所以无论使用`getMin()`还是`delMin()`方法，都需要对整个列表进行一次扫描。则要花费$O(n)$时间才能找到最小条目（最重要元素）。

   3. 使用有序列表实现优先队列：

      思想：欲提高`delMin()`方法的效率，可以要求所有条目按非升次序排列。这样仅需调用`removeLast()`就可以在$O(1)$时间内去掉末尾的最小条目（最重要元素）。但是，为了维护列表的有序性，在插入时，必须找正确的插入位置，即遍历列表。故在此方法中，`insert()`方法的时间复杂度为$O(n)$。

   4. 时间复杂度：

      |    Method     |      Unsorted List      |       Sorted List       |
      | :-----------: | :---------------------: | :---------------------: |
      |    `min()`    | ${\color{Green} O(n)} $ |         $O(1)$          |
      |  `insert()`   |         $O(1)$          | ${\color{Green} O(n)} $ |
      | `removeMin()` | ${\color{Green} O(n)} $ |         $O(1)$          |
      |   `size()`    |         $O(1)$          |         $O(1)$          |
      |  `isEmpty()`  |         $O(1)$          |         $O(1)$          |

      > Consider these $O(n)$ algorithms. Can we do better? That means, we traverse this Priority Queue when executing `min(), insert()` and `removeMin()` , which are $O(n)$ methods. If we can impel these method in $O(\log n)$， that would be better.

8. 有关排序算法——选择排序和插入排序

   1. 选择排序：如果采用无序列表来实现优先队列，则PQSort的前一步中的n次`insert()`操作总共只需要$O(n)$时间。然而，随后一步的效率却太低了——为了执行每一次`delMin()`操作，需要耗费的时间都将正比于优先队列当前的规模，因此第二阶段总共需要耗费$O(n^2)$的时间。
   2. 插入排序：采用有序列表来实现优先队列，则每次`delMin()`操作都可以在常数时间内完成，故 PQSort算法后一阶段总的时间复杂度可以降至$O(n)$。然而这并非没有代价。为了维护列表的有序性， 前一阶段中的每一次`insert()`操作，都需要对当前的列表进行扫描，以确定新条目的插入位置。在最坏的情况下，为此需要花费的时间将正比于优先队列当前的规模。因此，前一阶段的总体时间复杂度为$O(n^2)$。
   3. 效率比较：选择排序的时间为$\Theta(n^2)$，插入排序最好情况为$O(n)$，最坏情况为$O(n^2)$。
   4. 实现优先队列最高效的结构，就是借助**堆(Heap)**结构。

