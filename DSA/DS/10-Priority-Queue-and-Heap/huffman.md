# 编码树、Huffman树——二叉树的应用实例

## 简介与二进制编码

1. 通讯理论中的一个基本问题是，如何在**尽可能低**的成本下，以**尽可能高**的速度，**尽可能忠实**地实现信息在空间和时间上的**复制与转移**。在现代通讯技术中，无论采用电、磁、光或其它任何形式，在信道上传递的信息大多**以二进制比特**的形式表示和存在，而每一个具体的编码方案都对应于一棵**二叉编码树**。

2. 通讯系统可以帮助人们将一段信息从发送端传送给接收端。最常见的信息形式是字符串，即由来自某一有限字符集$\Sigma$的若干个字符组成的一个序列$M = (x_1, \ldots,x_n),\ x_i\in\Sigma,\ 1\le i\le n$。在将$M$加载至信道上并发送之前，首先需要对$M$进行**编码（Encoding）**。通常采用的都是二进制编码⎯⎯对于$\Sigma$​中的每个字符`c`，分别指定一个二进制串`e(c)`，这可以描述为如下单射函数： 

   
$$
   	e():\Sigma\mapsto \{0,1\}^*
$$
   
3. **解码（Decoding）**：接收端在收到该二进制中，可以利用对应的逆函数进行解码，这样能看到对应的函数内容。

   
$$
   	e^{-1}():\{0,1\}^*\mapsto\Sigma
$$
   
4. 实例：以英文字符集$\Sigma =\{A,B,C, \ldots,Z\}$为例，若要传送字符串`MAIN`，则一种可行的编码方式为

   > `e('N')="00"`, `e('A')="010"`, `e('I')="011"`, `e('M')="1"`.
   >
   > "1 010 011 00"-> "MAIN"
   >
   > ```
   >                            Root
   >      +-----------------------+------------------+
   > 		0										   M(1)
   >  +---+-------------+                   
   > 	N(00)             1
   >   			  +---+----+
   >   		 (010)A        I(011)
   > ```
   >
   > 

5. 二进制编码：对于任意给定的文本， 通过查阅编码表逐一将其中的字符转译为二进 制编码，这些编码依次串接起来即得到了全文的编码。

6. 二进制解码：由编码器生成的二进制流经信道送达之后，接收方可以按照事先约定的编码表（表5.1）， 依次扫描各比特位，并经匹配逐一转译出各字符，从而最终恢复出原始的文本。

7. **解码歧义**：编码方案确定之后，尽管编码结果必然确定，但解码过程和结果却不见得唯一。如，把`M`编码为`11`，把`S`编码为`111`，则`111111`既可以表示`MMM`，又可以表示`SS`。所以，为了避免这种情况，我们需要一种**前缀无歧义编码（Prefix-Free Code, PFC）**。

## PFC编码

1. 此类编码算法，可以明确地将二进制编码串，分割为一系列与各原始字符对应的片段，从而实现无歧义的解码。得益于这一特点，此类算法在整个解码过程中，对信息比特流的扫描不必回溯。
2. PFC编码可以使用**二叉编码树**来实现。任一编码方案都可描述为一棵二叉树：从根节点出发，其中，每一“父亲-左孩子”关系对应于一个二进制位'0'，每一“父亲-右孩子”关系对应于一个二进制位'1'。于是，若令每个字符分别对应于一匹叶子，则从根节点通往每匹叶子的路径，就对应于相应字符的二进制编码。因此， 这样的一棵树也称作**二叉编码树**。
3. **根通路串（Root Path String）**：由从根节点到每个节点的唯一通路，可以为各节点`v`赋予一个互异的二 进制串，称作根通路串，记作`rps(v)`。`|rps(v)|=depth(v)`就是`v`的深度。若将$\Sigma$中的字符分别映射至二叉树的节点，则字符`x`的二进制编码串即可取`rps(v(x))`，简记为`rps(x)`。
4. **PFC编码树和编码方案**：只要所有字符都对应于叶节点，歧义现象即自然消除——这也是实现PFC编码的简明策略。
5. 基于PFC编码树的**解码**：从头开始扫描接收到的二进制信息流，从根节点开始，根据各比特位不断进入下一层节点；到达叶子节点后，输出其对应的字符，然后重新回到根节点，并继续扫描二进制流。**实际上，这一个过程可以在接收过程中实时进行，而不必等到所有的比特位都到达之后再开始解码。（在线算法）**

## 最优编码树

1. 通讯效率的问题：我们希望使用尽可能少的比特位来表示字符串。

2. 平均编码长度：若将字符`c`在二叉编码树中对应的叶子的深度记为`depth(c)`，则有

   > 每一个字符$c\in\Sigma$的编码长度为$|e(c)|=\text{depth}(c)$。（观察即可得出规律）

3. 定义1：

   > 对于任一字符集$\Sigma$的任一编码方式$e()$，$\Sigma$中各字符的编码长度总和$\Sigma|e(c)|$称作$e()$（或其 $c\in\Sigma$对应的二叉编码树）的**编码总长度**；单个字符的**平均编码长度**为$\Sigma|e(c)|/|\Sigma|$。

    平均编码长度是反映**编码效率**的一项重要指标，我们希望这一指标尽可能地小。

4. 定义2（最优编码树）：

   > 对于任一字符集$\Sigma$，若在所有的编码方式中，某一编码方式$e()$使得平均编码长度最短，则称$e()$为$\Sigma$的一种最优编码，与之对应的编码树称作$\Sigma$的一棵**最优编码树**。 

   对于同一字符集$\Sigma$，所有深度不超过$|\Sigma|$的编码树仅有有限课，故长度最小者必然存在，但可能并不唯一。

5. 最优编码树的性质：在**最优二叉编码树**中，

   1. （双子性）每个内部节点的度数均为2；

      > 证明：（反证法）假设在某棵最优二叉编码树`T`中存在一个度数为1的内部节点`p`，不妨设`p`唯一的孩子为节点`c`。于是，只要将节点`p`删除，并代之以子树`c`，则可以得到原字符集的另一棵编码树`T'`。不难看出，除了子树`c`中每匹叶子的编码长度均减少1之外，其余叶子的编码长度不变，因此与`T`相比，`T'`的平均编码长度必然更短——这与`T`的最优性矛盾。（**换句话说，度数为1的节点相当于多给它的子树套了一层，完全没有用处）**

   2. （平衡性）各叶子之间的深度差**不超过1**。

      > 证明：（反证法）假设某棵最优二叉编码树T中存在深度相差超过1的两匹叶子`x`和`y`。不妨设`x`更深，并令`p`为`x`的父亲。于是根据**双子性**，作为内部节点的`p`必然还有另一个孩子`q`。注意，子树`q`中**至少含有一匹叶子**。
      >
      > 现在，将叶子`y`与子树`p`交换，得到一棵新的树`T'`。易见，`T'`依然是原字符集的一棵二叉编码树。更重要的是，除了`x`、`y`以及子树`q`中的叶子外，其余叶子的深度均保持不变。**经过这一交换操作之后，`x`的深度减少1，`y`的深度增加1，而`q`中各叶子的深度都将减少1。**因此，与`T`相比，`T'`的平均编码长度必然更短——这与T的最优性矛盾。 **（换句话说，让一个变深，让一群变浅）**

   3. 推论：（用来构造最优编码树）

      > 基于由$2|\Sigma|-1$个节点构成的完全二叉树`T`，将$\Sigma$中的字符任意分配给$T$的$|\Sigma|$匹叶子，即可得到$\Sigma$的一棵最优编码树。

## 完全二叉编码树

1. 一种最直接的思想是，构造**完全二叉编码树**来实现最优编码。这种方法，使得所有字母的编码长度都一样，看似是比较完善的一种方案。但是，这种方案没有考虑到字符出现的频率。

2. 例子：英文字母的出现概率：某些字母，如`e,t`等，出现的频率是某些字母，如`z,j`等频率的数百倍。这样，我们应该从另一个角度衡量每个字符的编码长度。

3. 平均带权编码长度：若假设字符`c`出现的概率为$p(c)\ge0,\ \sum\limits_{c\in\Sigma} p(c) = 1$，将其在二叉编码树中对应的叶子的深度记作`depth(c)`，则（定义）：

   > 1. 每个字符  $c \in \Sigma$  的**带权编码长度**为  $|e(c)|=\text{depth}(c) \times p(c)  $。
   >
   > 2. 对于任一字符集  $\Sigma$  的任一编码方式$e()$, $\Sigma$中各字符的平均带权编码长度总和  $\sum\limits_{c \in \Sigma}|e(c)|$  称作 $e()$ （或其对应的二叉编码树）的**平均带权编码长度**。

   不难理解，在考虑字符出现频率时，如上定义的平均带权编码长度就是反映编码效率的一项重要指标，我们同样也希望这一指标尽可能地小。

   ```
   如：对于e('M')="00", e('A')="001", e('I')="01", e('N')="1"来说，
   编码"00000100000110 1"="MAMANI",平均带权编码长度=15/6=2.5。
   |e('M')|=3{深度}*(2/6){频率}=1{带权编码长度}
   |e('M')|+|e('A')|+|e('I')|+|e('N')|=2.5{平均带权编码长度}
   ```

4. **完全二叉编码树 ≠ 平均带权编码最短**

   还是从上面的例子分析，若我们使用**完全二叉编码树**，对于拥有4个元素的字符集，不论**字符出现频率**的大小，其对应的平均带权编码长度都是2。但是，当某一个字符数量较多的时候，我们可以调整编码树，不使用完全二叉编码树，让字符频率高的享有较短编码，这样可以进一步降低平均带权编码长度。

## 最优带权编码树

1. 定义：对于任一字符集$\Sigma$，在字符出现频率分布为$p()$时，若某一编码方式$e()$使得平均带权编码长度达到最短，则称$e()$为（按照$p()$分布的）$\Sigma$ 的一种最优带权编码，其对应的编码树称作（按照$p()$分布的）$\Sigma$的一棵最优带权编码树。其必然存在，而且通常也不唯一。

2. 性质：

   1. （双子性）在最优带权编码树中，内部节点的度数均为2。证明方法如上。

   2. （层次性）对于字符出现概率为$p()$的任一字符集$\Sigma$，若字符`x`和`y`在所有字符中的出现概率最低，则必然存在某棵最优带权编码树，使得`x`和`y`在其中同处于最底层，而且互为兄弟。


   > 证明：
   >
   > 任取一棵最优带权编码树`T`。
   >
   > 根据最优带权编码树的双子性，在`T`的最低层节点中，必然可以找到一对兄弟`a`和`b`。现在，我们交换节点`a`和`x`（如果它们不是同一节点的话），并且交换节点`b`和`y`（如果它们不是同一节点的话），从而得到同一字符集的另一棵编码树`T'`。交换前，`a`,`b`是兄弟，而`x`和`y`关系未知。交换后，`x`和`y`为兄弟。
   >
   > 另外，根据字符`x`和`y`的权重最小性，经过这样的交换，`T'`对应的平均带权编码长度 绝不会增加。于是根据`T`的最优性，`T'`必然也是一棵最优编码树。

## Huffman编码树

1. 定义：Huffman编码树是一棵满二叉树，其中的每一个叶子节点都对应一个在给定的字符集中出现的字符，并且是一棵满足层次性的最优带权编码树。

2. Huffman编码树的构造算法——推论奠基

   设在字符的某一出现概率分布$p()$下，字符集$\Sigma$中出现概率最低的两个字符为`x`和`y`。现考察另一字符集$\Sigma^{\prime}=(\Sigma \backslash\{x,y\}) \cup\{z\}$（即剔除了x和y，增加了z），并将新增字符$z$的出现概率设为$p(z) = p(x) + p(y)$，其余字符的出现概率保持不变。若取T'为$\Sigma$'对应的一棵Huffman编码树，则根据Huffman编码树的层次性，不难得出如下推论： 

   > 将$T'$中与字符$z$对应的叶子替换为一个内部节点，并在其下设置分别对应于$x$和$y$的两匹叶子，则所得到就是$\Sigma$的一棵Huffman编码树。
   >
   > 证明：（正向思维） 首先，$T'$满足Huffman编码树的层次性，并且$z$已经是一匹叶子。现在在$z$下面增加$x$和$y$两匹叶子，则必然满足最优带权编码树的双子性和层次性两个性质，即为$\Sigma$的一棵Huffman编码树。

3. Huffman编码树的构造过程——具体实现

   1. **初始化**：由给定的n个权值构造 ![n](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 棵只有一个根节点的二叉树，得到一个二叉树集合（森林）F。
   2. **选取与合并**：从二叉树集合F中选取根节点权值 **最小的两棵** 二叉树分别作为左右子树构造一棵新的二叉树，这棵新二叉树的根节点的权值为其左、右子树根结点的权值和。
   3. **删除与加入**：从F中删除作为左、右子树的两棵二叉树，并将新建立的二叉树加入到F中。
   4. 重复 2、3 步，当集合中只剩下一棵二叉树时，这棵二叉树就是霍夫曼树。

4. Huffman编码树的构造过程——例子

   ![img](https://oi-wiki.org/ds/images/huffman-tree-2.png)

5. 树的带权路径长度

   设二叉树具有n个带权叶结点，从根结点到各叶结点的路径长度与相应叶节点权值的乘积之和称为 **树的带权路径长度(Weighted Path Length of Tree，WPL)**。设$w_i$为二叉树第$i$个叶结点的权值，$l_i$为从根结点到第$i$个叶结点的路径长度，则 WPL 计算公式如下：

   
$$
   WPL=\sum_{i=1}^{n}w_il_i
$$
   

   关于Huffman树的构造算法，还需要考虑一些退化情况。比如，有些字符的出现频率可能相等，或者虽然最初的字符权重互异，但经过若干次合并之后，森林F也可能会出现权重相等的子树。于是，在挑选权重最小的两棵子树时，将有可能出现歧义。这里，随便选中一个权重最小的子树即可，因为最后计算出来的$WPL$相等。

6. 霍夫曼编码：构建好Huffman树后，按照一定的次序（如左边为0，右边为1），从根节点向叶子节点延伸编码，最后字符所对应的一个二进制串即为该字符的Huffman编码。

7. 基于优先队列的Huffman树构造算法

   1. 若使用普通的数据结构，构建森林$O(n)$，挑选出权重最小的两棵树$O(n)$，合二为一$O(1)$，每一次迭代后，森林的规模会减一，一共要经过n-1次迭代后，森林中才会剩下一棵树。所以，总的时间复杂度为$O(n^2)$。
   2. 使用优先队列方法：始终将森林中的所有树（根）组织为一个优先队列，比如基于二叉堆实现的优先队列。这样，只要连续地调用`delMin()`方法两次，就可以找出当前权重最小的两棵树。在将这两棵树合并为一棵新树之后，可以调用`insert()`方法将其重新插入优先队列。这一过程将反复进行，每迭代一次，森林的规模就会减小1。因此，经过n-1次迭代，森林中将只包含一棵树，即Huffman编码树。 算法的效率上，两次`delMin()`、一次`insert()`操作和一次树的合并，可以在$O(1+3\log n)$时间内完成。所以，总共的时间为$(n-1)\times O(1+3\log n)=O(n\log n)$。