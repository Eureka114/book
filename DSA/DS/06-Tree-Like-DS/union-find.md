# 并查集

## 引入

- 概念：并查集是一种描述**不相交集合**的**树型的**数据结构，即若一个问题涉及多个元素，它们可划归到不同集合，同属一个集合内的元素等价，不同集合内的元素不等价。并查集可以用来处理一些不相交集合的合并和查询问题。
- 主要构成：并查集主要又一个森林和两种主要方法构成。
 	- 森林：并查集是一种树状的可能不相交集合的结构，因此需要一个森林，其中， 其中每棵树表示一个集合，树中的节点表示对应集合中的元素。
 	- 主要方法1：合并(Union)：合并两个元素所属集合（合并对应的树）
 	- 主要方法2：查询(Find)：查询某个元素所属集合（查询对应的树的根节点），这可以用于判断两个元素是否属于同一集合。

- 并查集用在一些有 **N** 个元素的集合应用问题中，我们通常是在开始时让每个元素构成一个单元素的集合，然后按一定顺序将属于同一组的元素所在的集合合并，其间要反复查找一个元素在哪个集合中。这个过程看似并不复杂，但数据量极大，若用其他的数据结构来描述的话，往往在空间上过大，计算机无法承受，也无法在短时间内计算出结果，所以只能用并查集来处理。此外，并查集在经过修改后可以支持单个元素的删除、移动；使用动态开点线段树还可以实现可持久化并查集。

> 注意：并查集无法以较低复杂度实现集合的分离。

## 并查集的构造：数组

### 初始化

```java
public UnionFindSet{
    private int[] id;
    private int count;
    public UnionFind1(int n) {
        count = n;
        id = new int[n];// 初始化, 每一个id[i]指向自己, 没有合并的元素
        for (int i = 0; i < n; i++)
            id[i] = i;
    }
}
```

### 合并和查找元素

```java
// 查找过程, 查找元素p所对应的集合编号
private int find(int p) {
    assert p >= 0 && p < count;
    return id[p];
}

// 查看元素p和元素q是否所属一个集合，O(1)复杂度
public boolean isConnected(int p, int q) {
    return find(p) == find(q);
}

// 合并元素p和元素q所属的集合，O(n) 复杂度
public void unionElements(int p, int q) {
    
    int pID = find(p);
    int qID = find(q);
    if (pID == qID) return;

    // 合并过程需要遍历一遍所有元素, 将两个元素的所属集合编号合并
    for (int i = 0; i < count; i++)
        if (id[i] == pID) id[i] = qID;
}
```

### 效率分析

在以数组构造并查集的情况下，实际过程中，查找的时间复杂度为$O(1)$，而连接效率并不高，为$O(n)$。再次查看关于并查集的定义，可能会发现：或许，我们可以使用另外的一种方法——树，来实现并查集。

## 并查集的构造：树

这里，我们使用一个数组构建一棵指向父节点的树。把每一个元素，看做是一个节点并且指向自己的父节点，根节点指向自己。

- 判断两个元素是否连接，只需要判断根节点是否相同即可。
- 连接两个元素，只需要找到它们对应的根节点，使根节点相连，那它们就是相连的节点。

### 初始化

```java
public class UnionFind2{
    private int[] parent;
    private int count;
    
    public UnionFind2(int count){
        parent = new int[count];
        this.count = count;
         for( int i = 0 ; i < count ; i++)
            parent[i] = i;//初始化, 每一个parent[i]指向自己, 表示每一个元素自己自成一个集合
    }
}
```

### 合并和查找元素

```java
//查找元素p所对应的集合编号, O(h)复杂度
private int find(int p){
    assert( p >= 0 && p < count );
    while( p != parent[p] ) p = parent[p];//根节点的特点: parent[p] == p
    return p;
}

//查看元素p和元素q是否所属一个集合, O(h)复杂度
public boolean isConnected( int p , int q ){
    return find(p) == find(q);
}
//合并元素p和元素q所属的集合, O(h)复杂度
public void unionElements(int p, int q){
    int pRoot = find(p);
    int qRoot = find(q);
    if( pRoot == qRoot ) return;
    parent[pRoot] = qRoot; //合并
}
```

![img](https://www.runoob.com/wp-content/uploads/2020/10/size-01.png)

### `size`的优化

- 对于上述的方法，如果要做`unionElements(4,9)`的话，就是一步一步地查询4的前驱，遍历完一整棵树后找到了8，然后让9成为8的父亲。
- 这样做的话，开销太大。为此，我们可以采用如下策略：在进行具体指向操作的时候先进行判断，把元素少的集合根节点指向元素多的根节点，能更高概率的生成一个层数比较低的树。
- 为此，我们在构建并查集的时候多引入一个数组参数`sz[]`，其中，`sz[i]`表示以`i`为根节点的元素的个数。

```java
public class UnionFind3{
    private int count;
    private int[] parent;
    private int[] sz;
    
    public UnionFind3(int count){
        this.count=count;
        parent = new int[count];
        sz = ne int[count];
        for( int i = 0 ; i < count ; i ++ ){
            parent[i] = i;
            sz[i] = 1;
        }
    }
    /* Other Methods */
    public void unionElements(int p, int q){

        int pRoot = find(p);
        int qRoot = find(q);

        if( pRoot == qRoot )
            return;

        // 根据两个元素所在树的元素个数不同判断合并方向
        // 将元素个数少的集合合并到元素个数多的集合上
        if( sz[pRoot] < sz[qRoot] ){
            parent[pRoot] = qRoot;
            sz[qRoot] += sz[pRoot];
        }
        else{
            parent[qRoot] = pRoot;
            sz[pRoot] += sz[qRoot];
        }
    }
}
```

### `rank`的优化

然而，上面的方法还是存在一些问题：如对于下面的例子，如果使用上述方法来操作`union(4,2)`，则层数反而还会增多。这样，对于较深的元素的合并请求，效率就比较低下。

![img](https://www.runoob.com/wp-content/uploads/2020/10/rank-01.png)

更准确的是，根据两个集合层数，具体判断根节点的指向，层数少的集合根节点指向层数多的集合根节点。这就是`rank`优化。

为此，我们在构建并查集类时，需要引入的额外参数是`rank`数组，其中，`rank[i]`表示以 i 为根的集合所表示的树的层数。

在构造函数中，我们让每一个`rank[i]`都等于1，`parent[i]=i`。

合并两元素的时候，需要比较根节点集合的层数，整个过程是 O(h)复杂度，h为树的高度。

```java
public void unionElements(int p, int q){

    int pRoot = find(p);
    int qRoot = find(q);

    if( pRoot == qRoot )
        return;

    if( rank[pRoot] < rank[qRoot] ){
        parent[pRoot] = qRoot;
    }
    else if( rank[qRoot] < rank[pRoot]){
        parent[qRoot] = pRoot;
    }
    else{ // rank[pRoot] == rank[qRoot]
        parent[pRoot] = qRoot;
        rank[qRoot] += 1;   // 维护rank的值
    }
}
```

### `find()`函数的路径压缩

如果希望能够更加快速的查找到一个根节点，我们就可以对`find()`函数进行路径压缩。

```java
private int find(int p){
    assert( p >= 0 && p < count );
    // 不断去查询自己的父亲节点, 直到到达根节点
    // 根节点的特点: parent[p] == p
    while( p != parent[p] )
        p = parent[p];
    return p;
}
```

为了实现更好的压缩效果，可以采用递归的写法。

```java
private int find(int p) {
    assert (p >= 0 && p < count);

    //第二种路径压缩算法
    if (p != parent[p])
        parent[p] = find(parent[p]);
    return parent[p];
}
```

上述的两种算法的时间复杂度虽然都为$O(h)$，但是树的高度明显降低了，使得其算法和后续的合并算法的耗时更少。

## 时间复杂度

这里，我们考虑的是使用路径压缩和使用启发式合并的情况。对于上述的两个情况，并查集的每个操作时间为$O(\alpha(n))$，其中，$\alpha$是Ackermann函数的反函数。其增长极其缓慢，也就是说其单次操作的平均运行时间可以认为是一个很小的常数。

Ackermann函数 $A(m,n)$ 的定义为：

$$
    A(m,n)=
    \begin{cases}
        n+1,&\text{if }m=0,\\
        A(m-1,1), &\text{if }m>0\text{ and }n=0,\\
        A(n-1,A(m,n-1)), &\text{otherwise.}
    \end{cases}
$$

1. [并查集 - OI Wiki (oi-wiki.org)](https://oi-wiki.org/ds/dsu/)

2. [并查集从入门到出门 | yukiyama (iyukiyama.github.io)](https://iyukiyama.github.io/union-find/)
3. [【算法与数据结构】—— 并查集-CSDN博客](https://blog.csdn.net/the_zed/article/details/105126583)
