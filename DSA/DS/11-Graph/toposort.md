# 拓扑排序

## 定义

在图论中，**拓扑排序(Topological Sorting)**是一个**有向无环图(Directed Acyclic Graph, DAG)**的所有顶点的线性序列，且该序列必须满足下面两个条件：

> 1. 每个顶点出现且只出现一次。
> 2. 若存在一条从顶点 A 到顶点 B 的路径，那么在序列中顶点 A 出现在顶点 B 的前面。

注意：有向无环图（DAG）才有拓扑排序，非DAG图没有拓扑排序一说。

例子：对于下面的图来说，一个可能的拓扑排序可以是`[1,3,2,4,5,6,7,8]`。拓扑排序的结果不唯一。

![img](https://oscimg.oschina.net/oscnet/up-8f6717028963b3bea2b37a3ce72eae8f784.png)

## 实现：零入度算法

零入度算法，又称作Kahn算法，是使用BFS实现的拓扑排序的算法。

### 算法思路

1. 从 DAG 图中找到**入度为 0** 的节点 A（也就是没有箭头指向它的节点），将其放入拓扑序列的结果集。
2. 同时删除由节点 A 出发的所有边。
3. 在剩下的 DAG 图中重复 1-2 两步。
4. 如果最后可以把全部的节点都删除并加入到结果集，那表示 DAG 图可以被拓扑排序；否则，如果最后有节点被剩下，那说明该图是有环图，无法被拓扑排序。

> 代码的核心是维持一个入度为 0 的顶点的集合。

### 代码实现

伪代码：

```text
L ← Empty list that will contain the sorted elements
S ← Set of all nodes with no incoming edges
while S is not empty do
    remove a node n from S
    insert n into L
    for each node m with an edge e from n to m do
        remove edge e from the graph
        if m has no other incoming edges then
            insert m into S
if graph has edges then
    return error (graph has at least one cycle)
else
    return L (a topologically sorted order)
```

具体代码：

```cpp
int n, m;
vector<int> G[MAXN];
int in[MAXN];  // 存储每个结点的入度

bool toposort() {
  vector<int> L;
  queue<int> S;
  for (int i = 1; i <= n; i++)
    if (in[i] == 0) S.push(i);
  while (!S.empty()) {
    int u = S.front();
    S.pop();
    L.push_back(u);
    for (auto v : G[u]) {
      if (--in[v] == 0) {
        S.push(v);
      }
    }
  }
  if (L.size() == n) {
    for (auto i : L) cout << i << ' ';
    return true;
  } else {
    return false;
  }
}
```

### 复杂度

假设这个图 $G = (V, E)$在初始化入度为0的集合$S$的时候就需要遍历整个图，并检查每一条边，因而有$O(E+V)$的复杂度。然后对该集合进行操作，显然也是需要$O(E+V)$的时间复杂度。

因而总的时间复杂度就有$O(E+V)$。

## 实现：零出度算法

零出度算法，是一种使用DFS实现的拓扑排序的算法，在这里，它可以看作是Kahn算法的逆向思路。

### 思路

1. 选取图中任意一个节点 A，将其状态标记为 “搜索中”
2. 寻找节点 A 的邻接点（沿着箭头指向寻找相邻的节点）
	1. 如果 A 存在邻接点
		1. 如果 A 的邻接点中存在状态为 “搜索中” 的邻接点，那么表示 DAG 图有环路，不可拓扑排序。
		2. 否则，那么任意选择一个状态为 “未搜索” 的邻接点 B，使用递归对 B 重复做 1 和 2 操作，注意此时 B 的邻接点判断不包含来路（也就是 A 节点）。等到 A 的所有邻接点都被搜索到，递归回溯回 A 节点的时候，那么 A 节点也会被标记为 “已搜索”，并压入结果栈。
	2. 如果 A 不存在邻接点，那么将节点 A 的状态改为 “已完成”，并且将其压入一个结果集的栈中。
3. A 节点及其相邻节点都搜索完毕后，如果还有未搜索的节点，那么任意选取一个节点当做出发点，继续重复 1,2,3 步骤。
4. 直到所有的节点都被搜索并压入栈，那么此时结果栈中，从栈顶到栈底的顺序，就是拓扑排序的顺序。

### 代码实现

```cpp
using Graph = vector<vector<int>>;  // 邻接表

struct TopoSort {
  enum class Status : uint8_t { to_visit, visiting, visited };

  const Graph& graph;
  const int n;
  vector<Status> status;
  vector<int> order;
  vector<int>::reverse_iterator it;

  TopoSort(const Graph& graph)
      : graph(graph),
        n(graph.size()),
        status(n, Status::to_visit),
        order(n),
        it(order.rbegin()) {}

  bool sort() {
    for (int i = 0; i < n; ++i) {
      if (status[i] == Status::to_visit && !dfs(i)) return false;
    }
    return true;
  }

  bool dfs(const int u) {
    status[u] = Status::visiting;
    for (const int v : graph[u]) {
      if (status[v] == Status::visiting) return false;
      if (status[v] == Status::to_visit && !dfs(v)) return false;
    }
    status[u] = Status::visited;
    *it++ = u;
    return true;
  }
};
```

### 复杂度

时间复杂度：$O(E+V)$；空间复杂度：$O(V)$。

## 算法优化

- 对于零入度算法：

	如果有时候，我们只需要知道某个 DAG 图是否可以拓扑排序，而不需要真正得到拓扑排序后的结果，那么可以不需要结果集列表，**只需要统计被删除的节点的数量即可，如果该数量等于 DAG 图的节点数，那么 DAG 图可以被拓扑排序**。

- 对于零出度算法：

	如果有时候，我们只需要知道某个 DAG 图是否可以拓扑排序，而不需要真正得到拓扑排序后的结果，那么可以不需要结果栈，只需要判断整个深度优先搜索过程，没有发生 “搜索中” 节点的相邻节点（不包含来路的节点）也是 “搜索中” 就行。

## 参考资料

1. [拓扑排序 - OI Wiki (oi-wiki.org)](https://oi-wiki.org/graph/topo/)
2. [拓扑排序（Topological Sorting）-CSDN博客](https://blog.csdn.net/lisonglisonglisong/article/details/45543451)
3. [05. 图的拓扑排序知识 | 算法通关手册（LeetCode） (itcharge.cn)](https://algo.itcharge.cn/08.Graph/02.Graph-Traversal/05.Graph-Topological-Sorting/#_1-拓扑排序简介)
