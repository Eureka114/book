# 高级搜索树——伸展树
## 伸展树的简介
伸展树(Splay Tree)是一种二叉搜索树，它通过一系列的旋转操作将被访问的节点调整到树根，从而提高后续访问的速度。伸展树的基本操作包括查找、插入和删除，这些操作的平均时间复杂度为 $O(\log n)$。伸展树是一种自调整的数据结构，它在实际应用中的性能表现优于其他平衡二叉搜索树，如 AVL 树和红黑树。

> 为什么叫伸展树？
>
> 每一次插入一个新元素时，它先像普通的二叉树一样插入到对应的位置，之后，该节点会通过若干次旋转一步一步地向根节点推送并成为新的根节点。由于在左、右旋中，整棵树的形状会发生巨大的改变，且呈现出“伸展”的姿态，故名为“伸展树”。

## 为什么要提出伸展树？
1. 对于AVL树而言，虽然它的查找、插入和删除操作的时间复杂度都是 $O(\log n)$，但是，其“平衡因子”必须时刻保持在 $\{-1, 0, 1\}$ 这三个值之间，这就导致了AVL树的旋转操作比较频繁，而且实现起来比较复杂。

2. 局部性：在信息处理的过程中，大量存在着访问的局部性，即刚被访问过的数据很可能在不久之后再次被访问。对于BST来说，在时间上，最近被访问的节点很可能在不久之后再次被访问，而在空间上，下一个将要访问的节点，很可能与刚被访问的节点在空间上相邻。所以，对AVL树而言，连续的$m$次查找，其时间复杂度为$O(m\log n)$。我们可否在局部加速？

	有以下的策略可供借鉴：

	> 自适应链表：节点一旦被访问，随即移动到最前端；（如果时间足够长，在某一段时间内，所有被访问的元素，都会不约而同地移到列表的前端的位置。）
	>
	> 模仿：BST的节点一旦被访问，随即调整到树根。（即尽量降低它的深度）

## 逐层伸展

1. 策略：节点v一旦被访问，则随即被推送至根。（`v`若是左孩子，就做一次`zig()`旋转，使得`v`和其父节点的位置互换；若是右孩子，就做一次`zag()`旋转。）反复地使用，直到`v`成为根节点。这里，与其说“推”，不如说“爬”，“**一步一步地往上爬**”。

2. 实质：就是自下而上的逐层单旋转，使得节点v逐渐向根节点靠拢。

3. 效率分析：伸展过程的效率取决于**树的初始形态**和**节点的访问次序**。在最坏情况下，逐层伸展的时间复杂度为$O(n)$，但是，由于节点v在树中的深度是随机的，所以，平均情况下，逐层伸展的时间复杂度为$O(\log n)$。

4. 最坏情况：考虑一棵退化为链表的二叉搜索树。不妨设此时树向左偏，即根节点为最大元素。当访问完该元素时，该节点会被推送至根节点。此时，若从小到大依次访问，则每一个节点所需要的访问成本大致为$n,n-1,\ldots,2,1$。现在，树的形状已经还原成初始形状。访问成本的总和为$\Omega(n^2)$，分摊下来，逐层伸展的时间复杂度为$\Omega(n)$。

	![img](https://img2.imgtp.com/2024/05/25/GcFgSaY6.png)

5. 思考：此时的效率和List/Vector等同，时间复杂度均为$O(n)$；和AVL树的$O(\log n)$相比，也比较差。我们必须寻找一种更好的伸展策略：双层伸展。

## 双层伸展

1. 自调整二叉搜索树：该算法由D. D. Sleator和R. E. Tarjan于1985年提出。构思的精髓：每一次向上追溯两层，而非一层。反复考察祖孙三代：`g=parent(p), p=parent(v),v`，并根据三者的相对位置，进行两次适当的旋转操作，使得`v`上升两层，成为（子）树的根节点。

2. “子孙异侧”：使用`zig-zag`或`zag-zig`操作，进行调整：和之前所讲的AVL树的双旋操作完全等效，并且，如此调整的效果，和逐层调整别无二致。

3. “子孙同侧”：使用`zig-zig`或`zag-zag`操作，进行调整。注意，这里和之前所讲的AVL树的`zig-zig`或`zag-zag`操作的次序不同。

  > 之前所讲的`zig-zig`和`zag-zag`都是先父节点开始旋转，而后再是祖父节点，即从下往上的调整、旋转策略；而Tarjan给出的调整策略是，先从祖父节点开始旋转，这样，节点`p`和`v`都会上升一层；接下来，再对子树新的树根`p`（即原来的父节点）进行一次旋转，使得`v`继续上升一层。
  >
  > ![img2.imgtp.com/2024/05/25/1qlgA5Sx.png](https://img2.imgtp.com/2024/05/25/1qlgA5Sx.png)
  >
  > 这引发了局部树的拓扑结构的不同。

4. 画龙点睛之笔：Tarjan提出的这个方法，使得节点访问之后，对应的路径长度随即减半，就像含羞草的折叠效果。而且，更重要的是，最坏的情况不会持续发生。此时，伸展操作仅需分摊$O(\log n)$时间。

5. 特例：如果`v`只有父亲，而没有祖父，则此时必有:`v.parent()==T.root()`。这种情况下，只需一次旋转即可。

6. “最坏情况”：还是考虑前述的最坏情况，现在访问`1`，则原树的高度会变低一半：继续访问最低的节点`3`，会发现，原树的高度继续变低一半。事实上，我们有：伸展树中较深的节点一旦被访问到，对应分支的长度立即减半。更准确地，Tarjan等人采用势能分析法已经证明了，在改用“双层伸展”的策略后，伸展树的单词操作可以在分摊的$O(\log n)$时间内完成。

## 算法实现

下面是伸展树的具体实现代码。（以下内容来自于[OI-wiki](https://oi-wiki.org/ds/splay)）

### 结构

节点维护的信息：

|     `rt`     |   根节点编号   |
| :----------: | :------------: |
|    `tot`     |    节点个数    |
|   `fa[i]`    |      父亲      |
| `ch[i][0/1]` | 左、右儿子编号 |
|   `val[i]`   |    节点权值    |
|   `cnt[i]`   | 权值出现的次数 |
|   `sz[i]`    |    子树大小    |

### 操作

`maintain(x)`：在改变节点位置后，将节点`x`的`size`更新。

`get(x)`：判断节点`x`是父亲节点的左儿子还是右儿子。

`clear(x)`：销毁节点`x`。

```cpp
void maintain(int x) { sz[x] = sz[ch[x][0]] + sz[ch[x][1]] + cnt[x]; }

bool get(int x) { return x == ch[fa[x]][1]; }

void clear(int x) { ch[x][0] = ch[x][1] = fa[x] = val[x] = sz[x] = cnt[x] = 0; }
```

### 旋转

旋转过程：（左、右旋均可）

```cpp
void rotate(int x) {
  int y = fa[x], z = fa[y], chk = get(x);
  ch[y][chk] = ch[x][chk ^ 1];//y.leftson()->x.rightson(); x.rightson().parent->y;
  if (ch[x][chk ^ 1]) fa[ch[x][chk ^ 1]] = y;
  ch[x][chk ^ 1] = y;
  fa[y] = x;//x.rightson()->y; y.parent()->x;
  fa[x] = z;
  if (z) ch[z][y == ch[z][1]] = x;//if y.parent()==true z.anyson()->x; x.parent()->z;
  maintain(y);
  maintain(x);
}
```

### Splay操作

```cpp
void splay(int x) {
  for (int f = fa[x]; f = fa[x], f; rotate(x))
    if (fa[f]) rotate(get(x) == get(f) ? f : x);
  rt = x;
}
```

### 插入操作

```cpp
void ins(int k) {
  if (!rt) {
    val[++tot] = k;
    cnt[tot]++;
    rt = tot;
    maintain(rt);
    return;
  }
  int cur = rt, f = 0;
  while (1) {
    if (val[cur] == k) {
      cnt[cur]++;
      maintain(cur);
      maintain(f);
      splay(cur);
      break;
    }
    f = cur;
    cur = ch[cur][val[cur] < k];
    if (!cur) {
      val[++tot] = k;
      cnt[tot]++;
      fa[tot] = f;
      ch[f][val[f] < k] = tot;
      maintain(tot);
      maintain(f);
      splay(tot);
      break;
    }
  }
}
```

### 查询x的排名

```
int rk(int k) {
  int res = 0, cur = rt;
  while (1) {
    if (k < val[cur]) {
      cur = ch[cur][0];
    } else {
      res += sz[ch[cur][0]];
      if (!cur) return res + 1;
      if (k == val[cur]) {
        splay(cur);
        return res + 1;
      }
      res += cnt[cur];
      cur = ch[cur][1];
    }
  }
```

### 查询第k名

```cpp
int kth(int k) {
  int cur = rt;
  while (1) {
    if (ch[cur][0] && k <= sz[ch[cur][0]]) {
      cur = ch[cur][0];
    } else {
      k -= cnt[cur] + sz[ch[cur][0]];
      if (k <= 0) {
        splay(cur);
        return val[cur];
      }
      cur = ch[cur][1];
    }
  }
}
```

### 查询前驱、后继

```cpp
int pre() {
  int cur = ch[rt][0];
  if (!cur) return cur;
  while (ch[cur][1]) cur = ch[cur][1];
  splay(cur);
  return cur;
}
int nxt() {
  int cur = ch[rt][1];
  if (!cur) return cur;
  while (ch[cur][0]) cur = ch[cur][0];
  splay(cur);
  return cur;
}
```

### 删除操作

```cpp
void del(int k) {
  rk(k);
  if (cnt[rt] > 1) {
    cnt[rt]--;
    maintain(rt);
    return;
  }
  if (!ch[rt][0] && !ch[rt][1]) {
    clear(rt);
    rt = 0;
    return;
  }
  if (!ch[rt][0]) {
    int cur = rt;
    rt = ch[rt][1];
    fa[rt] = 0;
    clear(cur);
    return;
  }
  if (!ch[rt][1]) {
    int cur = rt;
    rt = ch[rt][0];
    fa[rt] = 0;
    clear(cur);
    return;
  }
  int cur = rt, x = pre();
  fa[ch[cur][1]] = x;
  ch[x][1] = ch[cur][1];
  clear(cur);
  maintain(rt);
}
```

最后，将上述内容包含在一个结构体或者类中即可。


## 参考文献：
1. Sleator, D. D., & Tarjan, R. E. (1985). Self-adjusting binary search trees. Journal of the ACM, 32(3), 652–686. https://doi.org/10.1145/3828.3835