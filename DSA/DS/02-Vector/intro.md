# 向量类

## ADT与DS

1. 抽象数据类型（Abstract Data Type，ADT）是计算机科学中具有类似行为的特定类别的数据结构的数学模型；或者具有类似语义的一种或多种程序设计语言的数据类型。抽象数据类型 = 数据模型 + 定义在该模型上的一组操作。

2. 数据结构（Data Structure）是计算机存储、组织数据的方式.数据结构 = 基于某种特定语言，实现ADT的一整套算法。

3. 对比：

   |   抽象数据类型ADT    |        数据结构DS        |
   | :------------------: | :----------------------: |
   |       抽象定义       |         具体实现         |
   |       一种定义       |         多种实现         |
   |    外部的逻辑特性    |     内部的表示和实现     |
   |   不考虑时间复杂度   |     与复杂度密切相关     |
   |   只考虑操作和语义   |    需要完整的算法实现    |
   | 不涉及数据的存储方式 | 要考虑数据的具体存储机制 |

4. 通俗易懂的例子：汽车驾驶员关心汽车的性能和驾驶表现；汽车制作者负责实现汽车的相应功能，二者通过汽车使用手册（说明书，ADT）来达成某种协议。
5. 另例：`int n`表示指定n属于int数据类型，其具有了int类型的某些特点，并支持相应的操作方法。但是，我们不用关心任务是如何完成的，而是关心能做什么。所以，我们需要将某些东西隐藏起来，不必详细说明实现过程。`int float char Vector`等都是抽象数据类型。
6. **抽象**：不需详细说明实现过程的泛化操作称为抽象。即“抽取了过程的本质，而隐藏了实现的细节。”



## 从数组到向量

1. 线性数组：元素各由编号唯一指代，并可直接访问。普通的数组、向量类、线性表等。若每个元素占用的空间量为s（已计入padding），则A[i]的物理地址=`A+i*s`;
2. 向量是数组的抽象与泛化，由一组元素按线性次序封装而成。各元素与[0, n)内的秩（zhì，rank）一一对应：`using Rank = unsigned int;`
3. 特点：操作、管理维护更加简化、统一与安全；元素类型可灵活选取，便于定制复杂数据结构。

1. 向量的ADT及其实例：增、删、改、查；大小、排序、遍历；

   1. 在Java中，向量（可变长数组）在`java.util.Vector`类中实现。

      ```Java
      public class Vector<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, Serializable{
          //Vector类的声明
      }
      ```

   2. 向量类的接口：

      ```Java
      public interface Vector { 
          //返回向量中元素数目 
          public int getSize(); 
          //判断向量是否为空 
          public boolean isEmpty(); 
          //取秩为r的元素 
          public Object getAtRank(int r) throws ExceptionBoundaryViolation; 
          //将秩为r的元素替换为obj 
          public Object replaceAtRank(int r, Object obj) throws ExceptionBoundaryViolation; 
          //插入obj，作为秩为r的元素；返回该元素 
          public Object insertAtRank(int r, Object obj) throws ExceptionBoundaryViolation; 
          //删除秩为r的元素 
          public Object removeAtRank(int r) throws ExceptionBoundaryViolation; 
      } 
      ```

   3.  基于数组实现向量ADT： 

      ```Java
      public class Vector_ExtArray<T> implements Vector<T> { 
          private int N = 8;//数组的容量，可不断增加 
          private int n;//向量的实际规模 
          private Object A[];//对象数组 
          //构造函数 
          public Vector_ExtArray() { A = new Object[N]; n = 0; } 
          //返回向量中元素数目 
          public int getSize() { return n; } 
          //判断向量是否为空 
          public boolean isEmpty() { return (0 == n) ? true : false; } 
          //取秩为r的元素 
          public Object getAtRank(int r) 
          throws ExceptionBoundaryViolation { 
              if (0 > r || r >= n) throw new ExceptionBoundaryViolation("意外：秩越界"); 
              return A[r]; 
          }
      //将秩为r的元素替换为obj 
          public Object replaceAtRank(int r, Object obj) 
          throws ExceptionBoundaryViolation { 
              if (0 > r || r >= n) throw new ExceptionBoundaryViolation("意外：秩越界"); 
              Object bak = A[r]; 
              A[r] = obj; 
              return bak; 
          } 
      //插入obj，作为秩为r的元素；并返回该元素 
          public Object insertAtRank(int r, Object obj) 
          throws ExceptionBoundaryViolation { 
              if (0 > r || r > n) throw new ExceptionBoundaryViolation("意外：秩越界"); 
              if (N <= n) {//空间溢出的处理 
                  N *= 2; 
                  Object B[] = new Object[N];//开辟一个容量加倍的数组 
                  for (int i=0; i<n; i++) B[i] = A[i];//A[]中内容复制至B[] 
                      A = B;//用B替换A（原A[]将被自动回收） 
              } 
              for (int i=n; i>r; i--) A[i] = A[i-1];//后续元素顺次后移 
              A[r] = obj;//插入 
              n++;//更新当前规模 
              return obj; 
          } 
      //删除秩为r的元素 
          public Object removeAtRank(int r) 
          throws ExceptionBoundaryViolation { 
              if (0 > r || r >= n) throw new ExceptionBoundaryViolation("意外：秩越界"); 
              Object bak = A[r]; 
              for (int i=r; i<n-1; i++) A[i] = A[i+1];//后续元素顺次前移 
              n--;//更新当前规模 
              return bak; 
          } 
      }
      ```

      

   4. 构造——基于复制的构造：首先在向量内部开辟出足够的空间，后再将区间内的元素逐一复制过来。

      ```Java
      public class Vector<T> {
          private T[] elementData;
          private int size;
          private int capacity;
          private static final int DEFAULT_CAPACITY = 10;
      
          public Vector(T []A, int lo, int hi) {
              copyFrom(A, lo, hi);
          }
      
          public void copyFrom(T[] A, int lo, int hi) {
              capacity = Math.max(DEFAULT_CAPACITY, 2 * (hi - lo));
              elementData = (T[]) new Object[capacity];
              size = 0;
              for (; lo < hi; lo++, size++) {
                  elementData[size] = A[lo];
              }
          }
      }
      ```
