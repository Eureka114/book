# 散列冲突

## 为什么要对哈希冲突制定对策

1. 散列表的基本构思，可以概括为：开辟物理地址连续的桶数组`ht[]`，借助散列函数`hash()`，将词条关键码`key`映射为桶地址`hash(key)`，从而快速地确定待操作词条的物理位置。  

2. 然而遗憾的是，无论散列函数设计得如何巧妙，也不可能保证不同的关键码之间互不冲突。 在实际应用中，不发生任何冲突的概率远远低于我们的想象。  

   1. 考查如下问题：某课堂的所有学生中，是否有某两位生日（birthday，而非date of birth） 相同？这种情况也称作生日巧合。那么，发生生日巧合事件的概率是多少？  若将全年各天视作365个桶，并将学生视作词条，则可按生日将他们组织为散列表。如此， 上述问题便可转而表述为：若长度为365的散列表中存有n个词条，则至少发生一次冲突的概率有多大？

     > 至少有两位同学生日相同的可能性$P_{365}(n) = ?$
     >
     > 
     > $$
     > \begin{aligned}
     > P_{365}(1) = 0, P_{365}(2) = 1/365,&\cdots, P_{365}(22) = 47.6\%, \\P_{365}(23) = 50.7\%,&\cdots P_{365}(100)=99.999969\%
     > \end{aligned}
     > $$
   
   
   
   2.   不难理解，对于更长的散列表，只需更低的装填因子，即有50%的概率会发生一次冲突。鉴 于实际问题中散列表的长度M往往远大于365，故“不会发生冲突”只是一厢情愿的幻想。因此， 我们必须事先制定一整套有效的对策，以处理和排解时常发生的冲突。

## 解决方案

对于哈希冲突，我们可以使用开散列法、闭散列法。其中，开散列法包含多槽位法、独立链法、公共溢出区法；闭散列法包括线性试探法、查找链法，懒惰删除法、重散列法、平方试探法、双向平方试探法、双散列法等。

## 开散列法（封闭定址）

在这种方法中，每个数组位置都存储一个链表。当发生哈希冲突时，新的键值对会被添加到对应位置的链表中。实例：Java的HashMap就是采用封闭定址的策略。

封闭定址法可以处理任意数量的冲突，但是如果链表过长，查找效率会降低。

### 多槽位法

多槽位法(Multiple Slots)：将每个桶单元细分为若干个槽位，解决桶内冲突。只要槽位的数目不太多，仍然可以保证$O(1)$的时间效率。

缺点：slots过多，空间浪费；slots过少，仍然发生冲突。

### 独立链法（拉链法）

拉链法(Separate Chaining)：是在每个存放数据的地方开一个链表（每个桶存放一个指针），如果有多个键值索引到同一个地方，只用把他们都放到那个位置的链表里就行了。查询的时候需要把对应位置的链表整个扫一遍，对其中的每个数据比较其键值与查询的键值是否一致。如果索引的范围是 $1\ldots M$，哈希表的大小为 $N$，那么一次插入/查询需要进行期望 $O(\frac{N}{M})$ 次比较。

优点：无需为每个桶预备多个槽位；任意多次的冲突都可解决；删除操作实现简单、统一 。

缺点：指针本身占用空间；节点的动态分配和回收需耗时间；**空间未必连续分布，系统缓存很难生效**。

实现：

```cpp
//cpp
const int SIZE = 1000000;
const int M = 999997;

struct HashTable {
  struct Node {
    int next, value, key;
  } data[SIZE];

  int head[M], size;

  int f(int key) { return (key % M + M) % M; }//哈希函数

  int get(int key) {//首先通过哈希函数找到对应的链表头，然后进行遍历查找
    for (int p = head[f(key)]; p; p = data[p].next)
      if (data[p].key == key) return data[p].value;
    return -1;
  }

  int modify(int key, int value) {//修改给定键的值：找到链表头后遍历链表查找键
    for (int p = head[f(key)]; p; p = data[p].next)
      if (data[p].key == key) return data[p].value = value;
  }

  int add(int key, int value) {//添加新的键值对
    if (get(key) != -1) return -1;
    data[++size] = (Node){head[f(key)], value, key};
    head[f(key)] = size;
    return value;
  }
};
```

```python
# python
M = 999997
SIZE = 1000000

class Node:
    def __init__(self, next=None, value=None, key=None):
        self.next = next
        self.value = value
        self.key = key


data = [Node() for _ in range(SIZE)]
head = [0] * M
size = 0

def f(key):
    return key % M

def get(key):
    p = head[f(key)]
    while p:
        if data[p].key == key:
            return data[p].value
        p = data[p].next
    return -1

def modify(key, value):
    p = head[f(key)]
    while p:
        if data[p].key == key:
            data[p].value = value
            return data[p].value
        p = data[p].next

def add(key, value):
    if get(key) != -1:
        return -1
    size = size + 1
    data[size] = Node(head[f(key)], value, key)
    head[f(key)] = size
    return value
```

### 补充内容

这里再提供一个封装过的模板，可以像 map 一样用，并且较短。

```cpp
struct hash_map {  // 哈希表模板

  struct data {
    long long u;
    int v, nex;
  };  // 前向星结构

  data e[SZ << 1];  // SZ 是 const int 表示大小
  int h[SZ], cnt;

  int hash(long long u) { return (u % SZ + SZ) % SZ; }

  // 这里使用 (u % SZ + SZ) % SZ 而非 u % SZ 的原因是
  // C++ 中的 % 运算无法将负数转为正数

  int& operator[](long long u) {
    int hu = hash(u);  // 获取头指针
    for (int i = h[hu]; i; i = e[i].nex)
      if (e[i].u == u) return e[i].v;
    return e[++cnt] = (data){u, -1, h[hu]}, h[hu] = cnt, e[cnt].v;
  }

  hash_map() {
    cnt = 0;
    memset(h, 0, sizeof(h));
  }
};
```

在这里，hash 函数是针对键值的类型设计的，并且返回一个链表头指针用于查询。在这个模板中我们写了一个键值对类型为 `(long long, int)` 的 hash 表，并且在查询不存在的键值时返回 -1。函数 `hash_map()` 用于在定义时初始化。

## 闭散列法（开放定址）

在这种方法中，所有的键值对都直接存储在哈希表数组中（连续的空间）。当发生哈希冲突时，会寻找其他位置来存储新的键值对。只要有必要，任何散列桶都可以接纳任何词条。

开放定址法可以避免链表的额外内存开销，但是如果数组填充度过高，会导致冲突频繁，降低效率。

### 查找链/试探链

在闭散列法下面，我们为每一个桶都事先约定若干个备用桶（优先级逐次下降），它们构成了一个查找链。

查找算法：沿试探链，逐个转向下一桶单元，直到**命中成功**，或者**抵达一个空桶**（存在则必能找到？）而**失败**。

如何约定试探链？

### 线性试探法

1. 线性试探(Linear Probing)：一旦冲突，则试探后一紧邻的桶；直到命中（成功），或抵达空桶（失败）。

2. 优点：在散列表内部解决冲突；无需附加的指针、链表或溢出区等；整体结构保持简洁 。而且，只要还有空桶，迟早会找到。试探链连续，数据局部性良好。

3. 缺点：新增非同义词之间的冲突；数据堆积（clustering）现象严重。比如，一个孤立的数和一组连续的数插入到桶中。如果第一个数是孤立的，那么后面的一群连续的数都会发生冲突。不过，可以通过装填因子而有限控制冲突与堆积。

4. 具体实现：

   ```cpp
   const int N = 360007;  // N 是最大可以存储的元素数量
   
   class Hash {
    private:
     int keys[N];
     int values[N];
   
    public:
     Hash() { memset(values, 0, sizeof(values)); }
   
     int& operator[](int n) {
       // 返回一个指向对应 Hash[Key] 的引用
       // 修改成不为 0 的值 0 时候视为空
       int idx = (n % N + N) % N, cnt = 1;
       while (keys[idx] != n && values[idx] != 0) {
         idx = (idx + cnt * cnt) % N;
         cnt += 1;
       }
       keys[idx] = n;
       return values[idx];
     }
   };
   ```


### 懒惰删除法

1. 按照开放定址的策略：先后插入、相互冲突的一组词条，都将存放于同一查找链中。

2. 插入与删除：对于插入，新词条若尚不存在，则存入试探终止处的空桶。但试探链可能因而彼此串接、重叠。对于删除，也不能简单地清除命中的桶，否则经过它的试探链都将因此断裂，导致后续词条丢失——明明存在，却访问不到。

3. 懒惰删除(Lazy Removal)：仅做标记，不对试探链做更多调整。此后，带标记的桶，角色因具体的操作而异：

   1. 查找词条时，被视作“**必不**匹配的**非空**桶”，试探链在此得以延续。
   2. 插入词条时，被视作“**必然**匹配的**空闲**桶”，可以用来存放新词条。

4. 实现：

   ```typescript
   class LazyHashTable<K, V> {
       private table: Map<K, V>;
       private deleted: Set<K>;
   
       constructor() {
           this.table = new Map<K, V>();
           this.deleted = new Set<K>();
       }
   
       // 添加元素
       add(key: K, value: V): void {
           //如果键在已删除集合中，需要从已删除集合中移除
           if (this.deleted.has(key)) {
               this.deleted.delete(key);
           }
           this.table.set(key, value);
       }
   
       // 获取元素
       get(key: K): V | undefined {//联合类型，表示返回类型可以是V或undefined
           //如果键在已删除集合中，返回 undefined
           if (this.deleted.has(key)) {
               return undefined;
           }
           return this.table.get(key);
       }
   
       // 修改元素
       modify(key: K, value: V): void {
           //如果键在已删除集合中，不做任何操作
           if (this.deleted.has(key)) {
               return;
           }
           if (this.table.has(key)) {
               this.table.set(key, value);
           }
       }
   
       // 删除元素（懒惰删除）
       delete(key: K): void {
           //如果键在哈希表中，添加到已删除集合
           if (this.table.has(key)) {
               this.deleted.add(key);
           }
       }
   
       //重哈希（移除所有已删除的元素）
       rehash(): void {
           for (let key of this.deleted) {
               this.table.delete(key);
           }
           // 清空已删除集合
           this.deleted.clear();
       }
   }
   ```


### 平方试探法

1. 相较于开散列法，闭散列法拥有很好的性能。然而，对于闭散列的线性试探。试探位置的间距太近。所以，要适当地拉开试探的间距。

2. 所谓平方试探法，就是以平方数为距离，确定下一试探桶单元，即`[hash(key)+(i++)^2] % M`。

3. 优点：数据聚集现象有所缓解；试探链上，各桶间距线性递增；一旦冲突，可“聪明”地跳离是非之地。

4. 弊端：(1) 若涉及外存：I/O次数会增加。

   > 估算：
   >
   > 在通常情况下，缓存的规模在若干个KB左右。若这里为1KB，每一次只记录相应的引用（4字节），故每一个缓存页面都足以容纳至少256个桶单元。所以，每一次做I/O兑换，都要连续的发生16$(256=16^2)$次冲突。

   (2) 且试探问题的加大，可能会使很多的桶单元仍为空，而无法被试探出来。如，$M=12$，有$i^2$ mod 12={0,1,4,9}。其中$i\in\mathbb{N}$。

   > 由二次剩余的相关知识，我们有：
   >
   > 若$M$为合数，则$n^2\mod M$可能的取值必然少于$\left\lceil\dfrac{M}{2}\right\rceil$种。若$M$为素数，则$n^2\mod M$可能的取值恰好等于$\left\lceil\dfrac{M}{2}\right\rceil$种。


### 双向平方试探法

1. 引入：考查单向平方试探法，设散列表长度为素数M > 2。试证明：

   1. 任意关键码所对应的查找链中，前$\left\lceil\dfrac{M}{2}\right\rceil$个桶必然互异。

      > 证明：反证。假设存在$0\le a<b<\left\lceil\dfrac{M}{2}\right\rceil$，使得查找链上的第a个位置与第b个位置冲突，于是$a^2$和$b^2$必然同属于关于M的某一同余类，亦即： 
      >
      > 
      > $$
      > a^2\equiv b^2 (\text{mod }M)
      > $$
      > 
      >
      > 于是便有：
      >
      > 
      > $$
      > a^2-b^2=(a+b)(a-b)\equiv 0 (\text{mod }M)
      > $$
      > 
      >
      > 然而，无论是$(a + b)$还是$(a - b)$，绝对值都严格小于$M$，故均不可能被$M$整除——这与M 是素数的条件矛盾。

   2. 在装填因子尚未增至 50%之前，插入操作必然成功（而不致因无法抵达空桶而失败）。

      > 由上可知，查找链的前$\left\lceil \frac{M}{2} \right\rceil$项关于M，必然属于不同的同余类，也因此互不冲突。在装填因子尚不足50%时，这$\left\lceil \frac{M}{2} \right\rceil$项中至少有一个是空余的，因此不可能发生无法抵达空桶的情况。

   3. 在装填因子超过 50%之后，若适当调整各个桶的位置，则下一插入操作必然因无法抵达空桶而失败。

      > （只需证明：{0^2,1^2,2^2,...}关于M的同余项恰好只有$\left\lceil \frac{M}{2} \right\rceil$个）
      >
      > 证明：
      >
      > 任取$\left\lceil\dfrac{M}{2}\right\rceil\le c<M-1$，并考察查找链上的第c项。可以证明 ，总是存在$0\le d< \left\lceil \dfrac{M}{2} \right\rceil$ ，且查找链上的第$d$项和第$c$项冲突。实际上，我们只要令$d=M-c\ne c$，则有
      >
      > 
      > $$
      > c^2-d^2=(c+d)(c-d)=M(c-d)\equiv 0(\text{mod }M)
      > $$
      > 
      >
      > 于是$c^2$和$d^2$关于M同属一个同余类，作为散列地址相互冲突。

   4. 当M为合数时，即使在装填因子未达到50%之前，平方试探也有可能因无法抵达空桶而失败。

      > 在此时，对于$0\le a<b<\left\lceil\dfrac{M}{2}\right\rceil$，即便$a\pm b\equiv0(\text{mod }M)$​均不成立，也依然可能有
      >
      > 
      > $$
      > a^2-b^2=(a+b)(a-b)\equiv 0 (\text{mod }M).
      > $$

   

2. 由单向变双向：双向平方试探。其策略是，自冲突位置起，交替地沿着两个方向试探，均按平方来确定距离。

3. 考察子试探链：正向和反向的子试探链，各自包含$\left\lceil \frac{M}{2} \right\rceil$个互异的桶。对于形如$M=4k+3$的素数，可以保证两个子试探链彼此独立，即试探链的前$M$项均互异。而形如$m=4k+1$的素数，两个子试探链彼此数据相同，只是排列的顺序不同。所以，**表长必须取4k+3型的素数**。（由费马平方和定理可证得）

   > 如：
   >
   > n=7: 5, 3, 6,0,1,4,2; （从-25到+25）
   >
   > n=11: 8, 6, 2, 7, 10, 0, 1, 4, 9, 5, 3（从-36到+36）

### 公共溢出区法

1. 单独开辟一块连续空间，发生冲突的词条，顺序存入此区域。
2. 优点：结构简单，算法易于实现。
3. 缺点：但是，不冲突则已，一旦发生冲突，则最坏情况下，处理冲突词条所需的时间将正比于溢出区的规模。

### 重散列法

1. 随着装填因子增大，冲突概率、排解难度都将激增——此时，不如“集体搬迁”至一个更大的散列表。
2. 即：若装填因子高于50%，则重散列。

### 双散列法

1. 预先约定第二散列函数，当冲突时，则由其确定偏移量并确定下一试探位置。（更一般地，偏移增量同时还与key相关。）
