# 数组、集合

## 数组

### 一、数组的定义、实现、实现机制 

### 二、数组的ADT

   - 操作集合：可以表示为$a_i$, 且限定数组元素必须储存在地址连续的内存单元中

   - 操作集合：(1) acclocate() 分配所需空间。(2) getLength(); 取数组的长度； (3) set(i,x)存数组元素 (4)get(i) 取数组的元素。

     ```java
     int a[]=new int[10];
     int c=a.length;
     a[1]=5;
     int d=a[1];
     ```

- 定义二维数组

  ```
  int b[][]=new int[10][10];
  ```

- 对象数组：

  ```java
  public class Position{
      private int x,y;
      public Position(){
          x=y=0;
      }
  }
  Position pos[]=new Position[4];
  ```

  但是，对象数组的每个数组元素都需要通过new运算符单独创建。（for each/for). 对象数组也支持存操作和取操作，但和基本数据类型不同的是，所有对象名都是**引用**类型，所以，存取操作都是将一个已经创建的对象赋值给另一个对象引用，而不是新创建一个对象并赋值。

  插入数组元素：

  ```java
  public static void insertion(){
      int[] id=new int[10];
      for(int i=0;i<=5;i++){
          id[i]=i;
      }
      for(int i=5;i>=2;i--){
          id[i]=id[i-1];//向后移动元素，空出位置
      }
      id[1]=114;//New-value
  }
  //array deletion - similar to array insertion,时间复杂度为O(n)
  ```
  数组增加：若向已满的数组中继续添加元素，会抛出警告。我们可以对数组进行倍增。以下是两种方式： 1.增加指定大小c；2.倍增数组。求两种策略的时间复杂度？

We represent the time cost for pushing n elements as f(n).

1. incremental：子操作1.将新元素放入当前列表；2.若有元素仍需要push，则创建新的长度为（原长+指定长）的数组（消耗时间），将旧数组的元素复制到新元素中去。 

   > Assure $k$ as the number of new arrays we create, and the last new array exactly covers all $n$ elements, $k=?$ 
   >
   > $f(n)=O(n)+O(1)+O(c)+O(2c)+\ldots+O((k-1)c)$，$k=\dfrac{n}{c}$，则$f(n)=O(n^2)$。

2. 倍增(Doubling)：子操作类似，不过创建的是新的长度为原数组长度两倍的新数组。

   > Assure $k$ as the number of new arrays we create, and the last new array exactly covers all $n$ elements, $k=?$ 
   >
   > $f(n)=O(n)+O(1)+O(2)+O(4)+\ldots+O(2^{k-1})$，$k=\log_2n$，则$f(n)=O(n)$。

Amortized Time(均摊时间)：从本质上讲，摊销时间指的是 "如果进行多次操作，每次操作所需的平均时间"。摊销时间不一定是恒定的，也可以是线性和对数摊销时间或其他任何时间。

Incremental为$O(n^2)$, doubling为$O(n)$,均为$f(n)/n$.


  数组元素访问(Access)：$O(1)$

  

  ## 向量类

  1. Vector意为“可变长数组”。提供了扩充数组的功能，提供了自动扩充数组长度并把数组中原有的数据元素原样保存的功能。java.util.vector;


## 集合
1. Set的抽象数据类型（ADT）：add(); addAll(); iterator(); remove() removeAll() keepAll(): 保留集合中所有还存在于另一个指定集合中的所有元素 
clear(), size(), toArray()返回包含集合中所有元素的数组 contains(), containsAll()如果集合包含指定集合的(所有-All)元素，则返回true haahCode()返回哈希码值（集合中元素的地址）
2. Set的集合运算：Union-并集、Intersection-交集、Subset-子集 Subtraction差集（S-T={e|e is in S but not is in T}）；两个集合x和y的并集x.addAll(y); 两个集合x和y的交集x.retainAll(y); 判断x是否是y的子集 y.containsAll(x)  
3. Generic Merging(通用合并):
   - A的首位和B的首位进行比较，将结果放于空的序列S中，如此重复，若A的首位和B相同，则只放一次，若某一个集合为空，则将另一个集合的所有元素放入S中。
   ```java
   A=[3,5,6] B=[2,5,10,15] A union B=[2,3,5,6,10,15]
   a<b a.isLess(a,S)->A.remove(A.first()); 
   b<a b.isLess(b,S)->B.remove(B.first()); 
   a=b bothAreEqual(a,b,S)->A/B.remove(A/B.first());

