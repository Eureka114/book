# 图的存储

 对图进行操作，就需要进行图的存储。在本文中，用 $n$ 代指图的点数，用 $m$ 代指图的边数，用 $d^+(u)$ 代指点 $u$ 的出度，即以 $u$ 为出发点的边数。

## 直接存边

### 方法

使用一个数组来存边，数组中的每个元素都包含一条边的起点与终点（带边权的图还包含边权）。（或者使用多个数组分别存起点，终点和边权。）

### 代码实现

```c++
struct Edge {int u, v;};
int n, m;
vector<Edge> e;
vector<bool> vis;
bool find_edge(int u, int v) {
  for (int i = 1; i <= m; ++i) {
    if (e[i].u == u && e[i].v == v) {
      return true;
    }
  }
  return false;
}

void dfs(int u) {
  if (vis[u]) return;
  vis[u] = true;
  for (int i = 1; i <= m; ++i) {
    if (e[i].u == u) {
      dfs(e[i].v);
    }
  }
}

int main() {
  cin >> n >> m;
  vis.resize(n + 1, false);//resize()函数，第一个参数是更改的大小，第二个参数(可选)
  e.resize(m + 1);//是填充的值，如果没有，就使用构造函数或默认
  for (int i = 1; i <= m; ++i) cin >> e[i].u >> e[i].v;

  return 0;
}    
```

### 复杂度

查询是否存在某条边：$O(m)$。遍历一个点的所有出边：$O(m)$。遍历整张图：$O(nm)$。空间复杂度：$O(m)$。

### 应用

由于直接存边的遍历效率低下，一般不用于遍历图。

在 Kruskal 算法中，由于需要将边按边权排序，需要直接存边。

在有的题目中，需要多次建图（如建一遍原图，建一遍反图），此时既可以使用多个其它数据结构来同时存储多张图，也可以将边直接存下来，需要重新建图时利用直接存下的边来建图。

## 关联矩阵

- 考虑一个`n`列`e`行的二维数组`inc[][]`。这里，`n`是点的个数，而`e`是边的条数。如果存在从`u`到`v`的边`e`，则`inc[u][e]=inc[v][e]=1`。不难发现，对于其中的每一列，只会有两个值为1，而其他的值为0。
- 该方法在某些特定问题上十分有效，但是由于其对数组的利用率不高，因此不过多介绍。

## 邻接矩阵

### 方法

- 使用一个二维数组 `adj` 来存边，其中 `adj[u][v]` 为 1 表示存在 $u$ 到 $v$ 的边，为 0 表示不存在。如果是带边权的图，可以对上述的定义进行扩展，在 `adj[u][v]` 中存储 $u$ 到 $v$ 的边的边权。（这里，对于无向图我们可以使用整个数组，也可以使用整个数组对角线的一半；而对于有向图和带权图，我们要使用整个数组。）
- 注意：如果不考虑自环，则所有的`adj[i][i]`，不论在无权图或带权图中，都不存在。对于不存在的边，通常统一取值为$\infty$或0。

### 代码实现

```cpp
#include <iostream>
#include <vector>

using namespace std;

int n, m;
vector<bool> vis;
vector<vector<bool> > adj;

bool find_edge(int u, int v) { return adj[u][v]; }

void dfs(int u) {
  if (vis[u]) return;
  vis[u] = true;
  for (int v = 1; v <= n; ++v) {
    if (adj[u][v]) {
      dfs(v);
    }
  }
}

int main() {
  cin >> n >> m;

  vis.resize(n + 1, false);
  adj.resize(n + 1, vector<bool>(n + 1, false));

  for (int i = 1; i <= m; ++i) {
    int u, v;
    cin >> u >> v;
    adj[u][v] = true;
  }

  return 0;
}
```

### 复杂度

- 查询是否存在某条边：$O(1)$。遍历一个点的所有出边：$O(n)$。

- 遍历整张图：$O(n^2)$。空间复杂度：$O(n^2)$。（一般情况下很难达到，这是比较大的浪费）

### 应用

邻接矩阵只适用于没有重边（或重边可以忽略）的情况。

其最显著的优点是可以 $O(1)$ 查询一条边是否存在。（这是邻接表无法做到的。）

由于邻接矩阵在稀疏图上效率很低（尤其是在点数较多的图上，空间无法承受），所以一般只会在稠密图上使用邻接矩阵。

## 点操作和边操作

（来自[数据结构(上) - 清华大学 - 学堂在线 (xuetangx.com)](https://next.xuetangx.com/learn/THU08091000384/THU08091000384/19318292/video/43008980)）

### 点操作

- 如果我们想要实现一个“对于任意顶点`i`，如何枚举其所有的邻接顶点`neighbor`？”的问题，对于邻接矩阵来说，是这样实现的：

    ```c++
    int nextNbr(int i, int j){//点操作
        while(-1 < j && !exists(i,--j));
        return j; 
    }
    //初始时，传入nextNbr(i,n)；
    
    bool exists(int i, int j){//边操作
        return ( 0 <= i ) && ( i < n ) && ( 0 <= j ) && ( j < n ) && E[i][j] != NULL;
    }
    ```
    如果，使用邻接表的话，时间复杂度可以降低至$O(1+d^-(i))$。
    
- 如果要进行更为复杂的操作，如点的插入算法和点的删除算法，这时候的情况就要比边的插入或删除算法更加复杂，因为在边的插入和删除算法中，矩阵的规模没有发生变动；而在点的插入和删除算法中，边的规模可能会发生变动。
- 对于点的插入，如果现在有一个点集`v[]`和一个邻接矩阵`E[][]`，我们需要插入一个新的点`i`，则发生的变化是：
    1. 对每一个行向量的末尾增加一个单元并引入一个初始为空的记录，并且更新边的计数。
    2. 生成一个行向量，长度为`n+1`，并且每一个元素都初始化为空。
    3. 取出新增加的行向量的地址，并放入更上一级的边表中，以便于后续的操作。
    4. 插入点`i`至点集`v[]`的末尾。
- 对于点的删除，和点的插入算法类似。
### 边操作

- 当然，处理这些点操作之外，还有边操作，如返回`e(u,v)`的数据、状态、权重等。这些内容都需要先进行`exists(u,v)`判断。

- 如果进行更加复杂的操作呢？如边的插入算法和边的删除算法：

	> 对于边的插入算法：
	>
	> 1. 忽略已有的边（使用`exists(u,v)`进行判断）；
	> 2. 创建新的边（`new Edge()`），并更新边的计数；
	> 3. 更新关联顶点的出度和入度。（增加）
	>
	> 对于边的删除算法：
	>
	> 1. 备份边`(u,v)`的数据；
  > 2. 删除边`(u,v)`，并更新边的计数；
  > 3. 更新关联顶点的出度和入度。
  > 4. 返回被删除边的信息。（减少）

## 邻接表

### 方法

- 使用一个支持动态增加元素的数据结构构成的数组，如 `vector<int> adj[n + 1]` 来存边，其中 `adj[u]` 存储的是点 $u$ 的所有出边的相关信息（终点、边权等）。

### 代码

```cpp
#include <iostream>
#include <vector>

using namespace std;

int n, m;
vector<bool> vis;
vector<vector<int> > adj;

bool find_edge(int u, int v) {
  for (int i = 0; i < adj[u].size(); ++i) {
    if (adj[u][i] == v) {
      return true;
    }
  }
  return false;
}

void dfs(int u) {
  if (vis[u]) return;
  vis[u] = true;
  for (int i = 0; i < adj[u].size(); ++i) dfs(adj[u][i]);
}

int main() {
  cin >> n >> m;

  vis.resize(n + 1, false);
  adj.resize(n + 1);

  for (int i = 1; i <= m; ++i) {
    int u, v;
    cin >> u >> v;
    adj[u].push_back(v);
  }

  return 0;
}
```

### 基于链表的邻接表

当然，也可以使用一系列的链表来实现上述操作。

- 对于有向图$G=(V,E)$，我们需要一共$V$组链表来存放所有的值。
- 节点包含两个元素：该节点的编号和指向下一个节点的指针。
- 对于图中的顶点$v$，该条链表的首节点为自己，其余节点为一系列和$v$相连的节点，故邻接表的长度为$L=1+d^+(v)$（不含末尾节点所指向的空节点）。
- 若图是无向图，则对于一对使用无向边连接而成的两个节点$(u,v)$，它们中间的连接关系要在这一系列的链表中出现两次。具体来说：在第$u$条链表中，要写`u->v`；而在第$v$条链表中，要写`v->u`。
- 若图是带权图，则需要在节点中加入一个权值元素。

### 复杂度

查询是否存在 $u$ 到 $v$ 的边：$O(d^+(u))$（如果事先进行了排序就可以使用二分查找做到 $O(\log(d^+(u)))$）。

遍历点 $u$ 的所有出边：$O(d^+(u))$。

遍历整张图：$O(n+m)$。空间复杂度：$O(m)$。

### 应用

存各种图都很适合，除非有特殊需求（如需要快速查询一条边是否存在，且点数较少，可以使用邻接矩阵）。

尤其适用于需要对一个点的所有出边进行排序的场合。

## 链式前向星

> 百度百科：如果说邻接表是不好写但效率好，邻接矩阵是好写但效率低的话，前向星就是一个相对中庸的数据结构。前向星固然好写，但效率并不高。而在优化为链式前向星后，效率也得到了较大的提升。虽然说，世界上对链式前向星的使用并不是很广泛，但在不愿意写复杂的邻接表的情况下，链式前向星也是一个很优秀的数据结构。

### 引入

- 链式前向星其实就是静态建立的邻接表，时间效率为$O(m)$，空间效率为$O(m)$，遍历的效率仍为$O(m)$。链式前向星存储的是以$[1,n]$为起点的边的集合。

- 例子：对于下面的图$G=(V,E)$，数据由三部分构成：`from, to, edgeValue`。

	```bash
	5 7
	1 2 1
	2 3 2
	3 4 3
	1 3 4
	4 1 5
	1 5 6
	4 5 7
	```

	链式前向星的输出如下：

	```bash
	1 //以1为起点的边的集合
	1 5 6
	1 3 4
	1 2 1
	 
	2 //以2为起点的边的集合
	2 3 2
	 
	3 //以3为起点的边的集合
	3 4 3
	 
	4  //以4为起点的边的集合
	4 5 7
	4 1 5
	 
	5 //以5为起点的边不存在
	```

### 语义约定

首先，对所有的边进行标号$[0,n-1]$，并约定如下变量含义：

- `next`：表示与这个边起点相同的上一条边的编号。
- `head[i]`：表示以 i 为起点的最后一条边的编号，初始化为`-1`。

然后写增加边的函数：

```cpp
void add_edge(int u, int v, int w)//加边，u起点，v终点，w边权
{
    edge[cnt].to = v; //终点
    edge[cnt].w = w; //权值
    edge[cnt].next = head[u];//以u为起点上一条边的编号，也就是与这个边起点相同的上一条边的编号
    head[u] = cnt++;//更新以u为起点上一条边的编号
}
```

- 例如：对于1 3 4这条边，有：`edge[3].to = 3; edge[3].next = 0; head[1] = 3;`。

遍历函数如下：

```cpp
for(int i = 1; i <= n; i++)//n个起点{
    cout << i << endl;
    for(int j = head[i]; j != -1; j = edge[j].next){//遍历以i为起点的边
        cout << i << " " << edge[j].to << " " << edge[j].w << endl;
    }
    cout << endl;
}
```

- 第一层循环：找出所有的点。依次遍历以$[1,n]$为起点的边的集合。
- 第二层循环：遍历以`i`为起点的所有边。
	- `k`首先等于`head[i]`。其中，`head[i]`中存的是以 `i` 为起点的最后一条边的编号。
	- 然后通过`edge[j].next`来找下一条边的编号。
	- 当找到最后一个边时（以`i`为起点的第一条边），由于之前初始化时，`head[i]=-1`，此时，有：`edge[j].next=-1`。遍历中止。
- 代码如下：

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1005;//点数最大值
int n, m, cnt;//n个点，m条边
struct Edge
{
    int to, w, next;//终点，边权，同起点的上一条边的编号
}edge[maxn];//边集
int head[maxn];//head[i],表示以i为起点的第一条边在边集数组的位置（编号）
void init()//初始化
{
    for (int i = 0; i <= n; i++) head[i] = -1;
    cnt = 0;
}
void add_edge(int u, int v, int w)//加边，u起点，v终点，w边权
{
    edge[cnt].to = v; //终点
    edge[cnt].w = w; //权值
    edge[cnt].next = head[u];//以u为起点上一条边的编号，也就是与这个边起点相同的上一条边的编号
    head[u] = cnt++;//更新以u为起点上一条边的编号
}
int main()
{
    cin >> n >> m;
    int u, v, w;
    init();//初始化
    for (int i = 1; i <= m; i++)//输入m条边
    {
        cin >> u >> v >> w;
        add_edge(u, v, w);//加边
        /*
        加双向边
        add_edge(u, v, w);
        add_edge(v, u, w);
        */
    }
    for (int i = 1; i <= n; i++)//n个起点
    {
        cout << i << endl;
        for (int j = head[i]; j != -1; j = edge[j].next)//遍历以i为起点的边
        {
            cout << i << " " << edge[j].to << " " << edge[j].w << endl;
        }
        cout << endl;
    }
    return 0;
}

```

## 时间复杂度总结

|        Method         | Edge List |   Adjacent List    | Adjacent Map | Adj Matrix |
| :-------------------: | :-------: | :----------------: | :----------: | :--------: |
|    `numVertices()`    |  $O(1)$   |       $O(1)$       |    $O(1)$    |   $O(1)$   |
|     `numEdges()`      |  $O(1)$   |       $O(1)$       |    $O(1)$    |   $O(1)$   |
|     `vertices()`      |  $O(n)$   |       $O(n)$       |    $O(n)$    |   $O(n)$   |
|       `edges()`       |  $O(m)$   |       $O(m)$       |    $O(m)$    |   $O(m)$   |
|    `getEdge(u, v)`    |  $O(m)$   | $O(\min(d_u,d_v))$ |  期望$O(1)$  |   $O(1)$   |
|     `inDegree(v)`     |  $O(m)$   |       $O(1)$       |    $O(1)$    |   $O(n)$   |
|    `outDegree(v)`     |  $O(m)$   |       $O(1)$       |    $O(1)$    |   $O(n)$   |
|  `outgoingEdges(v)`   |  $O(m)$   |      $O(d_v)$      |   $O(d_v)$   |   $O(n)$   |
|  `incomingEdges(v)`   |  $O(m)$   |      $O(d_v)$      |   $O(d_v)$   |   $O(n)$   |
|   `insertVertex(x)`   |  $O(1)$   |       $O(1)$       |    $O(1)$    |  $O(n^2)$  |
|   `removeVertex(v)`   |  $O(m)$   |      $O(d_v)$      |   $O(d_v)$   |  $O(n^2)$  |
| `insertEdge(u, v, x)` |  $O(1)$   |       $O(1)$       |  期望$O(1)$  |   $O(1)$   |
|    `removeEdge(e)`    |  $O(1)$   |       $O(1)$       |  期望$O(1)$  |   $O(1)$   |



## 参考资料

[数据结构(上) - 清华大学 - 学堂在线 (xuetangx.com)](https://next.xuetangx.com/learn/THU08091000384/THU08091000384/19318292/video/43008986)

[平面图的欧拉定理 - chasedeath - 博客园 (cnblogs.com)](https://www.cnblogs.com/chasedeath/p/14246463.html)

[联通平面图的欧拉公式及推广(联通的推广需要用到平面次数和等于边数的两倍)_区域的数量v+e-CSDN博客](https://blog.csdn.net/ResumeProject/article/details/113035217)

[欧拉公式_平面图的欧拉公式-CSDN博客](https://blog.csdn.net/jinghui_7/article/details/81625966)

[图的存储 - OI Wiki (oi-wiki.org)](https://oi-wiki.org/graph/save/)
