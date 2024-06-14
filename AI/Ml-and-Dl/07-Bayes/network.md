# 7.5 贝叶斯网
1. 贝叶斯网(Bayesian Network)也称作“信念网”，是一种概率图模型，用于描述随机变量之间的依赖关系。贝叶斯网是一个有向无环图(Directed Acyclic Graph, DAG)，其中节点表示随机变量，边表示变量之间的依赖关系。这里，对于离散型的随机变量，可以用条件概率表(Conditional Probability Table, CPT)来表示节点的条件概率分布；对于连续型的随机变量，可以用高斯分布来表示节点的条件概率分布。

2. 贝叶斯网的具体构成：贝叶斯网$B$由结构$G$和参数$\Theta$两部分构成，即$B=\langle G,\Theta\rangle$。
   1. 节点：表示随机变量，每个节点都有一个条件概率分布。
   2. 边：表示变量之间的依赖关系，即一个节点的条件概率分布依赖于其父节点的取值。
   3. 条件概率表：用于描述节点的条件概率分布，即给定父节点的取值，子节点的取值的概率。

   > 例：假设节点E直接影响到节点H，即E->H，则用从E指向H的箭头建立结点E到结点H的有向弧(E,H)，权值(即连接强度)用条件概率$P(H\mid E)$来表示。

   >  简言之，把某个研究系统中涉及的随机变量，根据是否条件独立绘制在一个有向图中，就形成了贝叶斯网络。其主要用来描述随机变量之间的条件依赖，用圈表示随机变量(random variables)，用箭头表示条件依赖(conditional dependencies)。

3. 表示：令$G = (L,E)$表示一个有向无环图(DAG)，其中 L 代表图形中所有的节点的集合，而 E 代表有向连接线段的集合，且令$X=\{x_i\mid i\in L\}$​为其有向无环图中的某一节点 i 所代表的随机变量，若节点 X 的联合概率可以表示成：
   
$$
  p(x)=\prod_{i=1}^n p(x_i\mid x_{\pi_i})
$$

其中$x_{\pi_i}$表示节点 X 的父节点集合，即节点 i 的所有父节点的集合，则称X为相对于一有向无环图$G$的贝叶斯网络。

此外，对于任意的随机变量，其联合概率可以由各自的局部概率分布相乘而推出：

$$
  P(x_1,x_2,\cdots,x_k)=P(x_k\mid x_1,x_2,\cdots,x_{k-1})P(x_{k-1}\mid x_1,x_2,\cdots,x_{k-2})\cdots P(x_2\mid x_1)P(x_1)
$$

1. 贝叶斯网络的3种结构形式：

- （前置）D-分离(D-Separation)：$X$和$Y$在给定$Z$的条件下是条件独立的，即$P(X,Y\mid Z)=P(X\mid Z)P(Y\mid Z)$。

- （形式一）Head-to-Head：形如$X$和$Y$都是$Z$的父节点的情况。如图所示。

![1 _1_.png](https://img2.imgtp.com/2024/05/28/AcwIFLo7.png)

此时，我们有$P(a,b,c) = P(a)\cdot P(b)\cdot P(c|a,b)$​成立。化简得：

$$
	\sum_c P(a,b,c) = \sum_c P(a)\cdot P(b)\cdot P(c|a,b) \Rightarrow P(a,b) = P(a)\cdot P(b)
$$

即在c未知的条件下，a、b被阻断(blocked)，是独立的，称之为head-to-head条件独立。

- （形式二）Tail-to-Tail：形如$X$和$Y$都是$Z$的子节点的情况。如图所示。

![1 _2_.png](https://img2.imgtp.com/2024/05/28/gFSp8bYs.png)

考虑$c$未知和$c$已知的情况。

1. 在c未知的时候，有：$P(a,b,c)=P(c)\cdot P(a|c)\cdot P(b|c)$，此时，没法得出$P(a,b) = P(a)P(b)$，即c未知时，a、b不独立。
2. 在c已知的时候，有：$P(a,b\mid c)=P(a,b,c)/P(c)$，此时，有$P(a,b\mid c)=P(a\mid c)\cdot P(b\mid c)$，即c已知时，a、b独立。

所以，在c给定的条件下，a，b被阻断(blocked)，是独立的，称之为tail-to-tail条件独立。

- （形式三）Head-to-Tail：即链式的网络结构。还是分为$c$未知和$c$已知的两种情况。

![1 _3_.png](https://img2.imgtp.com/2024/05/28/DUbbW6Tb.png)

1. 当$c$未知，$P(a,b,c)=P(a)\cdot P(c|a)\cdot P(b|c)$，但无法推出$P(a,b) = P(a)P(b)$，即c未知时，a、b不独立。
2. 当$c$已知，$P(a,b\mid c)=P(a,b,c)/P(c)$，且根据条件概率的有关内容，可以推出$P(a,b\mid c)=P(a\mid c)\cdot P(b\mid c)$，即c已知时，a、b独立。所以，在c给定的条件下，a，b被阻断(blocked)，是独立的，称之为head-to-tail条件独立。
3. 马尔可夫链(Markov Chain)：马尔可夫链是一种特殊的贝叶斯网络，其节点之间的连接是连续的，即$X_1\rightarrow X_2\rightarrow X_3\rightarrow\cdots\rightarrow X_n$，且每个节点的状态只与其前一个节点的状态有关，与其它节点无关。这种链式结构的贝叶斯网络称为马尔可夫链。上面的情况，就是马尔可夫链。

2. 贝叶斯网络的实例：

> 给定如下图所示的贝叶斯网络：
>
> 
>
> ![1 _4_.png](https://img2.imgtp.com/2024/05/28/TheEUOf1.png)
>
> 其中，各个单词、表达式表示的含义如下：
>
> - smoking表示吸烟，其概率用P(S)表示，lung Cancer表示的肺癌，一个人在吸烟的情况下得肺癌的概率用P(C|S)表示，X-ray表示需要照医学上的X光，肺癌可能会导致需要照X光，吸烟也有可能会导致需要照X光（所以smoking也是X-ray的一个因），所以，因吸烟且得肺癌而需要照X光的概率用P(X|C,S)表示。
> - Bronchitis表示支气管炎，一个人在吸烟的情况下得支气管炎的概率用P(B|S)，dyspnoea表示呼吸困难，支气管炎可能会导致呼吸困难，肺癌也有可能会导致呼吸困难（所以lung Cancer也是dyspnoea的一个因），因吸烟且得了支气管炎导致呼吸困难的概率用P(D|S,B)表示。
>
> Lung Cancer简记为C，Bronchitis简记为B，dyspnoea简记为D，且C = 0表示lung Cancer不发生的概率，C = 1表示lung Cancer发生的概率，B等于0（B不发生）或1（B发生）也类似于C，同样的，D=1表示D发生的概率，D=0表示D不发生的概率，便可得到dyspnoea的一张概率表，如上图的最右下角所示。
>
> ![1 _5_.png](https://img2.imgtp.com/2024/05/28/AaOmdk60.png)
>
> 对于上图，在一个人已经呼吸困难（dyspnoea）的情况下，其抽烟（smoking）的概率是多少呢？即：P(s|d=1)=?  推导：
>
> ![1 _6_.png](https://img2.imgtp.com/2024/05/28/4UiaRTuZ.png)
>
> 解释下上述式子推导过程：
>
> - 第二行：对联合概率关于b,x,c求和（在d=1的条件下），从而消去b,x,c，得到s和d=1的联合概率。
> - 第三行：最开始，所有变量都在sigma(d=1,b,x,c)的后面（sigma表示对“求和”的称谓），但由于P(s)和“d=1,b,x,c”都没关系，所以，可以提到式子的最前面。而且P(b|s)和x、c没关系，所以，也可以把它提出来，放到sigma(b)的后面，从而式子的右边剩下sigma(x)和sigma(c)。
> - 此外，图中Variable elimination表示的是变量消除的意思。

### 参考资料

[超详细讲解贝叶斯网络(Bayesian network) - USTC丶ZCC - 博客园 (cnblogs.com)](https://www.cnblogs.com/USTC-ZCC/p/12786860.html)
