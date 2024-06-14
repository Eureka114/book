# 线性表

## 单链表
1. 单链表(Singly-linked Lists)是具体的数据结构，包含一系列的结点序列：每一个结点储存`Data`和`*Next`。数据域：存储元素数值数据；指针域：存储直接后继的存储位置。

   > 这里把Node定义为私有静态内部类(private static class)，原因：1.杜绝被别的外部类调度或实例化； 2.只为其依附的外部类服务。[Java基础-私有静态内部类](https://blog.csdn.net/weixin_44228952/article/details/108482040)
2. 单链表操作举例：

   - 插入(Insertion)：
     - 若在头位置插入：插入的结点应指向current.head；插入的结点应为头结点。若在tail后插入结点，tail.next=current, current.next=null。插入后，size++. 时间复杂度：$$O(1)$$. 
     - 若在中间位置插入，两个步骤：1. build links for your insertion code, 2. change links on the original linked list. O(n)

```java
public void insert(int i, Object obj) throws Exception {//把新结点插入到第i个结点前，新结点data域的值为obj.
    if(i < 0 || i > size){
        throw new Exception("参数错误！");
    }
    index(i - 1);//先定位到第i-1个结点处，此时，current=第i-1个结点；
    current.setNext(new Node(obj,current.next));//创建新的结点，新结点的data=obj(T=Node)，next域为(current.next，实际上是指向第i个结点)
    // 加入新结点，set current由i-1.next变为新创建的结点
    size++;//第28行通过构造方法使得新结点和后继结点链接；setNext方法使得前序结点和新结点链接。
}
```
3. Singly-linked Lists versus ArrayLists
   - 相似：
     - Both two data structures store collections of elements, which can be accessed, added, removed, and modified.
     - Both two data structures can grow and shrink dynamically as elements are added or removed.
   - 不同：
     - Contiguous memory
     - Data access and manipulation
     - Dynamic sizing

## 单链表和顺序表的对比

|  项目  |                             优点                             |                             缺点                             |
| :----: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| 顺序表 |             主要优点是算法简单，空间单元利用率高             | 主要缺点是需要预先确定数据元素的最大个数，插入和删除时需要移动较多的数据元素。 |
| 单链表 | 主要优点是不需要预先确定数据元素的最大个数，插入和删除操作不需要移动数据元素； | 主要缺点是查找数据元素时需要顺序进行，不能像顺序表那样随机查找任意一个数据元素。另外，每个结点中要有一个指针域，因此空间单元利用率不高。而且单链表操作的算法也较复杂。 |

## 循环链表

结构特点是链表中最后一个结点的指针域指向整个链表的第一个结点，从而使链表形成一个环。它的优点是从链尾到链头比较方便。循环单链表也有带头结点和不带头结点两种结构。

```java
1.构造函数新增head.next=head的语句，把初始时的带头结点的循环单链表重新设计
2.在index(i)成员函数中，while(current!=null)换为while(current!=head) A=[3,5,6] B=[2,5,10,15] A union B=[2,3,5,6,10,15]
    a<b a.isLess(a,S)->A.remove(A.first()); b<a b.isLess(b,S)->B.remove(B.first()); a=b bothAreEqual(a,b,S)->A/B.remove(A/B.first());
```



## 双向链表

双向链表是每个结点除后继指针域外还有一个前驱指针域，它有带头结点和不带头结点，循环和非循环结构，双向链表是解决查找前驱结点问题的有效途径。
` A=[3,5,6] B=[2,5,10,15] A union B=[2,3,5,6,10,15]`

 `a<b a.isLess(a,S)->A.remove(A.first()); `

`b<a b.isLess(b,S)->B.remove(B.first()); `

`a=b bothAreEqual(a,b,S)->A/B.remove(A/B.first());`

```java
public class Node{
    Object data;
    Node prior;
    Node next;
}
pubilc class DoubleLink{
    Node head;
    Node current;
}
```

我们有

```java
p.next.prior=p.prior.next=p;
```

构建DoubleLink类时，需要Override insert/delete等成员函数，加上反向的。

> 单链表 头部插入 O(1) 尾部插入若有头结点O(1),无O(n) 头部删除O(1) 尾部删除 若有头结点O(n) 无O(n)
>
> 双链表 O(1)

## 仿真链表

可以用二维数组/Pair/Map等实现：

| index | element | next |
| ----- | ------- | ---- |
| 0     | A       | 1    |
| 1     | B       | 2    |
| 2     | C       | -1   |

