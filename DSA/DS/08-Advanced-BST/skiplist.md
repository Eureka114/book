# 跳表

跳表 (Skip List) 是由 William Pugh 于1989年发明的一种查找数据结构，支持对数据的快速查找，插入和删除。跳跃表以有序的方式在层次化的链表中保存元素， 效率和平衡树媲美 —— 查找、删除、添加等操作都可以在对数期望时间下完成， 并且比起平衡树来说， 跳跃表的实现要简单直观得多。

跳表的期望空间复杂度为 $O(n)$，跳表的查询，插入和删除操作的期望时间复杂度都为 $O(\log n)$。

![](https://redisbook.readthedocs.io/en/latest/_images/skiplist.png)

## 基本思想

跳表是一种类似于链表的数据结构。更加准确地说，跳表是对**有序链表**的改进。有序所有的**有序链表**的默认为升序排序。

跳跃表是节点（这里的节点是Quad-Node）的二维集合。

最底层的链表涵盖了所有的数据，而上层的链表通过省略某些节点，达到了建立索引的效果。并且，越上层的链表，其索引性就越强。

> Skip List is a two-dimensional collection of nodes arranged horizontally into levels and vertically into towers.
>
> - Each level is a list $S_i$;
>
> - Each tower contains nodes storing the same entry across consecutive lists (**consecutive** levels).
> - 此外，还要引入两个哨兵节点——`MIN_INF, MAX_INF`.

> A Skip List is implemented with quad-nodes that stores:
>
> - **Entry**;
> - Link to the **previous** and **next** node, `prev,next`;
> - Link to the node **above** and **below**, `above,below`.

## 具体实现

### 查询(Skip Search)

搜索操作`skipSearch(Key k)`的原理如下：

- Start at the first node of the top list `MIN_INF`, then repeat the following steps:
- At the current node `p`, we compare `k` with `key(next(p))`:
	- If `k=key(next(p))`: return `value(next(p))`;
	- if `k<key(next(p))`: move down to the next list;
	- if `k>key(next(p))`: move to the right(Scan forward);
	- If we drop down past the bottom list, `k` not found (return null).

```cpp
V& find(const K& key) {
  SkipListNode<K, V>* p = head;

  // 找到该层最后一个键值小于 key 的节点，然后走向下一层
  for (int i = level; i >= 0; --i) {
    while (p->forward[i]->key < key) {
      p = p->forward[i];
    }
  }
  // 现在是小于，所以还需要再往后走一步
  p = p->forward[0];

  // 成功找到节点
  if (p->key == key) return p->value;

  // 节点不存在，返回 INVALID
  return tail->value;
}
```

### 插入(Insertion)

插入算法使用了一种随机算法。总的来说，插入算法先使用搜索算法，搜索到要插入的位置，然后把内容插入到最底层的链表；然后，使用某些随机算法，决定其向上传播的次数。

实现原理如下：

- Run `skipSearch(k)` to find its position of insertion. if a node is **found** with key k, then overwrite its associated value with v.
- **Otherwise**, when the **next move** is to drop down past the bottom list,`insert(k,v)` immediately after node `p` in $S_i=S_0$.
- Then filp a coin to determine the height of the tower for inserting `(k,v)`:
	- If it is `tail`, stop here;
	- If it is `head`, insert the entry at the appropriate node position in $S_{i+1}$;
	- Go back to the First Step.

```cpp
void insert(const K &key, const V &value) {
  // 用于记录需要修改的节点
  SkipListNode<K, V> *update[MAXL + 1];

  SkipListNode<K, V> *p = head;
  for (int i = level; i >= 0; --i) {
    while (p->forward[i]->key < key) {
      p = p->forward[i];
    }
    // 第 i 层需要修改的节点为 p
    update[i] = p;
  }
  p = p->forward[0];

  // 若已存在则修改
  if (p->key == key) {
    p->value = value;
    return;
  }

  // 获取新节点的最大层数
  int lv = randomLevel();
  if (lv > level) {
    lv = ++level;
    update[lv] = head;
  }

  // 新建节点
  SkipListNode<K, V> *newNode = new SkipListNode<K, V>(key, value, lv);
  // 在第 0~lv 层插入新节点
  for (int i = lv; i >= 0; --i) {
    p = update[i];
    newNode->forward[i] = p->forward[i];
    p->forward[i] = newNode;
  }

  ++length;
}

int randomLevel() {
  int lv = 1;
  // MAXL = 32, S = 0xFFFF, PS = S * P, P = 1 / 4
  while ((rand() & S) < PS) ++lv;
  return min(MAXL, lv);
}
```



### 删除(Deletion)

删除算法就是删除键值为`key`的节点。删除节点的过程就是先执行一遍查询的过程，中途记录要删的节点是在哪一些节点的后面，最后再执行删除。每一层最后一个键值小于 `key` 的节点，就是需要进行修改的节点。删除时，先删除最底下的节点。

- `skipSearch(k)`: if no entry with key is found, return `null`.
- **Otherwise**, remove the value at node where k is found, and remove all values of the nodes exactly above this node, keeping only one list containing guard nodes `MIN_INF` and  `MAX_INF`.

```cpp
bool erase(const K &key) {
  // 用于记录需要修改的节点
  SkipListNode<K, V> *update[MAXL + 1];

  SkipListNode<K, V> *p = head;
  for (int i = level; i >= 0; --i) {
    while (p->forward[i]->key < key) {
      p = p->forward[i];
    }
    // 第 i 层需要修改的节点为 p
    update[i] = p;
  }
  p = p->forward[0];

  // 节点不存在
  if (p->key != key) return false;

  // 从最底层开始删除
  for (int i = 0; i <= level; ++i) {
    // 如果这层没有 p 删除就完成了
    if (update[i]->forward[i] != p) {
      break;
    }
    // 断开 p 的连接
    update[i]->forward[i] = p->forward[i];
  }

  // 回收空间
  delete p;

  // 删除节点可能导致最大层数减少
  while (level > 0 && head->forward[level] == tail) --level;

  // 跳表长度
  --length;
  return true;
}
```

## 效率分析

### 时间复杂度

#### 当$p=\dfrac{1}{2}$时

Use $C(j)$ to denote the expected # of step took for going up $j$ levels, then:

$$
\begin{aligned}
C(j)&=1+0.5C(j-1)+0.5C(j)\\
C(j)&=2j
\end{aligned}
$$

> Explain: the **expected # of step** took for going up j levels should be:
>
> - Make one step, then make either:
> 	- $C(j-1)$ steps if the first step went up, $p=\dfrac12$;
> 	- $C(j)$ steps if the first step went left, $p=\dfrac12$;
> - Where $j$ is the # of the levels for the skip list.

Then **estimate** the # of level `h`:

- **For the insertion algorithm**, the probability that a given entry is at level `i` is equal to the probability of getting `i` consecutive heads when flipping a coin: $\dfrac{1}{2^i}$. So, $P_i$ that level `i` has at least one entry is at most $P_i\le \dfrac{n}{2^i}$.

> Explain: Because the probability that any of n different events occurs is at most the sum of probabilities that each occurs.

- For a constant $c>1$， the probability of a skip list with level at least $h=c\log n$ is:

	$$
	P_{h}\le\frac{1}{n^{c-1}}.
	$$

	which is, the probability that level $h$ is smaller than $c\log n$ is $P=1-\left(\dfrac{1}{n^{c-1}}\right)$.

- And $\lim\limits_{n\to\inf} P=1$, h **smaller than** $c\log n$ i s **extremely likely to happen**. Hence, the # of levels h is $O(\log n)$.

- **For the put operation**: time cost includes `skipSearch`——$O(\log n)$; Finding the correct position to promote the node is $O(1)$. So it still costs $O(\log n)$.

- **For the deletion operation**:  time cost includes `skipSearch`——$O(\log n)$; Finding the node-to-delete is $O(1)$. So it still costs $O(\log n)$.

#### 更一般的情况

**从后向前**分析查找路径，这个过程可以分为从最底层爬到第 $L(n)$ 层和后续操作两个部分。在分析时，假设一个节点的具体信息在它被访问之前是未知的。

假设当前我们处于一个第 $i$ 层的节点 $x$，我们并不知道 $x$ 的最大层数和 $x$ 左侧节点的最大层数，只知道 $x$ 的最大层数至少为 $i$。如果 $x$ 的最大层数大于 $i$，那么下一步应该是向上走，这种情况的概率为 $p$；如果 $x$ 的最大层数等于 $i$，那么下一步应该是向左走，这种情况概率为 $1-p$。

令 $C(i)$ 为在一个无限长度的跳表中向上爬 $i$ 层的期望代价，那么有：

$$
\begin{aligned}
C(0) &= 0 \\
C(i) &= (1-p)(1+C(i)) + p(1+C(i-1))
\end{aligned}
$$

解得 $C(i)=\dfrac{i}{p}$。

由此可以得出：在长度为 $n$ 的跳表中，从最底层爬到第 $L(n)$ 层的期望步数存在上界 $\dfrac{L(n) - 1}{p}$。

现在只需要分析爬到第 $L(n)$ 层后还要再走多少步。易得，到了第 $L(n)$ 层后，向左走的步数不会超过第 $L(n)$ 层及更高层的节点数总和，而这个总和的期望为 $\dfrac{1}{p}$。所以到了第 $L(n)$ 层后向左走的期望步数存在上界 $\dfrac{1}{p}$。同理，到了第 $L(n)$ 层后向上走的期望步数存在上界 $\dfrac{1}{p}$。

所以，跳表查询的期望查找步数为 $\dfrac{L(n) - 1}{p} + \dfrac{2}{p}$，又因为 $L(n)=\log_{\frac{1}{p}}n$，所以跳表查询的 **期望时间复杂度** 为 $O(\log n)$。

在最坏的情况下，每一层有序链表等于初始有序链表，查找过程相当于对最高层的有序链表进行查询，即跳表查询操作的 **最差时间复杂度** 为 $O(n)$。

插入操作和删除操作就是进行一遍查询的过程，途中记录需要修改的节点，最后完成修改。易得每一层至多只需要修改一个节点，又因为跳表期望层数为 $\log_{\frac{1}{p}}n$，所以插入和修改的 **期望时间复杂度** 也为 $O(\log n)$。

### 空间复杂度

对于一个节点而言，节点的最高层数为 $i$ 的概率为 $p^{i-1}(1 - p)$。所以，跳表的期望层数为 $\sum\limits_{i>=1} ip^{i - 1}(1-p) = \dfrac{1}{1 - p}$，且因为 $p$ 为常数，所以跳表的 **期望空间复杂度** 为 $O(n)$。

在最坏的情况下，每一层有序链表等于初始有序链表，即跳表的 **最差空间复杂度** 为 $O(n \log n)$。

## 跳表的随机访问优化

访问跳表中第 $k$ 个节点，相当于访问初始有序链表中的第 $k$ 个节点，很明显这个操作的时间复杂度是 $O(n)$ 的，并不足够优秀。

跳表的随机访问优化就是对每一个前向指针，再多维护这个前向指针的长度。假设 $A$ 和 $B$ 都是跳表中的节点，其中 $A$ 为跳表的第 $a$ 个节点，$B$ 为跳表的第 $b$ 个节点 $(a < b)$，且在跳表的某一层中 $A$ 的前向指针指向 $B$，那么这个前向指针的长度为 $b - a$。

现在访问跳表中的第 $k$ 个节点，就可以从顶层开始，水平地遍历该层的链表，直到当前节点的位置加上当前节点在该层的前向指针长度大于等于 $k$，然后移动至下一层。重复这个过程直至到达第一层且无法继续行操作。此时，当前节点就是跳表中第 $k$ 个节点。

这样，就可以快速地访问到跳表的第 $k$ 个元素。可以证明，这个操作的时间复杂度为 $O(\log n)$。