# 4.3 剪枝

1. **过拟合 (Over-Fitting) **：从决策树的构造流程中我们可以直观地看出：不管怎么样的训练集，决策树总是能很好地将各个类别分离开来，这时就会遇到之前提到过的问题：过拟合，即太依赖于训练样本。
2. **剪枝 (Pruning) **：决策树算法对付过拟合的主要手段。剪枝的策略有两种如下：
   1. 预剪枝（pre-pruning）：在构造的过程中先评估，再考虑是否分支。
   2. 后剪枝（post-pruning）：在构造好一颗完整的决策树后，自底向上，评估分支的必要性。
3. 评估指的是性能度量，即决策树的泛化性能。之前提到：可以使用测试集作为学习器泛化性能的近似，因此可以将数据集划分为训练集和测试集。预剪枝表示在构造数的过程中，对一个节点考虑是否分支时，首先计算决策树不分支时在测试集上的性能，再计算分支之后的性能，若分支对性能没有提升，则选择不分支（即剪枝）。后剪枝则表示在构造好一颗完整的决策树后，从最下面的节点开始，考虑该节点分支对模型的性能是否有提升，若无则剪枝，即将该节点标记为叶子节点，类别标记为其包含样本最多的类别。

![8.png](https://i.loli.net/2018/10/17/5bc728ec80d34.png)

![9.png](https://i.loli.net/2018/10/17/5bc728ec9e330.png)

![10.png](https://i.loli.net/2018/10/17/5bc728ec9d497.png)

上图分别表示不剪枝处理的决策树、预剪枝决策树和后剪枝决策树。预剪枝处理使得决策树的很多分支被剪掉，因此大大降低了训练时间开销，同时降低了过拟合的风险，但另一方面由于剪枝同时剪掉了当前节点后续子节点的分支，因此预剪枝“贪心”的本质阻止了分支的展开，在一定程度上带来了欠拟合的风险。而后剪枝则通常保留了更多的分支，因此采用后剪枝策略的决策树性能往往优于预剪枝，但其自底向上遍历了所有节点，并计算性能，训练时间开销相比预剪枝大大提升。

# 4.4 连续与缺失值
## 连续值处理
- 现实学习任务中常会遇到连续属性，因此有必要讨论如何在决策树中使用连续属性。对于连续值的属性，若每个取值作为一个分支则显得不可行，因此需要进行离散化处理，常用的方法为二分法，基本思想为：给定样本集$D$与连续属性$a$，二分法试图找到一个划分点t将样本集$D$在属性$a$上分为两个子集$\{D^+_t\},\{D^-_t\}$。其中，$\{D^+_t\}$包含那些在属性a上取值大于t的样本，$\{D^-_t\}$包含那些在属性a上取值不大于t的样本。

- 对于相邻属性取值的$a^i,a^{i+1}$，t在其左闭右开区间上取任意值所划分的结果相同。所以，我们可考察包含n-1个元素的候选划分点集合（取其中位点进行划分）。具体操作如下：
   > 1. 首先将α的所有取值按升序排列，所有相邻属性的均值作为候选划分点（n-1个，n为α所有的取值数目）。
   >
   > 2. 计算每一个划分点划分集合D（即划分为两个分支）后的信息增益。
   >
   > 3. 选择最大信息增益的划分点作为最优划分点。
   >
   我们有：

$$
\begin{aligned}
   \text{Gain}(D,a)&=\max_{t\in T_a}\text{Gain}(D,a,t)\\
   &=\max_{t\in T_a}\text{Ent(D)}-\sum_{\lambda\in\{-,+\}}\frac{|D_t^\lambda|}{|D|}\text{Ent}(D_t^\lambda)
\end{aligned}
$$

- 需要注意的是，与离散属性不同，若当前结点划分属性为连续属性，该属性还 可作为其后代结点的划分属性。

## 缺失值处理

- 现实中常会遇到不完整的样本，即某些属性值缺失。有时若简单采取剔除，则会造成大量的信息浪费，因此在属性值缺失的情况下需要解决两个问题：
   1. 如何选择划分属性：若样本在属性a上的值缺失，我们如何选择划分属性？
   2. 给定划分属性，若某样本在该属性上缺失值，如何划分到具体的分支上？

- 给定训练集D和属性a，并令$\bar D$表示D中在属性a上没有缺失值的样本子集。假定为样本集中的每一个样本都赋予一个权重，根节点中的权重初始化为1，则定义

   样本子集所占比例（无缺失值样本所占的比例）

$$
   \rho=\frac{\sum_{x\in \bar D}w_x}{\sum_{x\in D}w_x}
$$

样本子集每个类别的比例（无缺失值样本中第k类所占的比例）

$$
   \tilde{p}_k=\frac{\sum_{x\in \bar D}w_x}{\sum_{x\in D}w_x},(1\le k\le|y|)
$$

每个分支所含样本的比例（无缺失值样本中在属性a上取值$a^v$的样本所占的比例）

$$
   \tilde{r}_v=\frac{\sum_{x\in \bar D_v}w_x}{\sum_{x\in D}w_x},(1\le v\le V)
$$

   其中，$\tilde{p}_k$和$\tilde{r}_v$的总和均为1。

- 对于问题1，显然我们可以根据$\bar D$来判断属性a的优劣。通过在样本集D中选取在属性a上没有缺失值的样本子集，计算在该样本子集上的信息增益，最终的信息增益等于该样本子集划分后信息增益乘以样本子集占样本集的比重。即：
  
$$
   \begin{aligned}
      \text{Gain}(D,a)&=\rho\text{Gain}(\bar D,a)\\
      &=\rho\left(\text{Ent}(D)-\sum_{v=1}^V\tilde{r}_v\text{Ent}(D_v)\right)
   \end{aligned}
$$
   
- 对于问题2，若样本在划分属性上的取值已知，则划入相应的分支，样本权值不变；若样本在划分属性上的取值未知，则将该样本同时划入所有分支，并变更该样本的权重。
  
$$
   w_x\leftarrow w_x\tilde{r}_v
$$

# 4.5 多变量决策树
1. **多变量决策树**：在决策树的构造过程中，我们通常是选择一个属性进行划分，但有时候多个属性的组合可能更好地划分数据集。多变量决策树就是考虑多个属性的组合来划分数据集的决策树。多变量决策树的构造过程与单变量决策树类似，只是在选择划分属性时，需要考虑多个属性的组合。

2. **多变量决策树的构造**：多变量决策树的构造过程与单变量决策树类似，只是在选择划分属性时，需要考虑多个属性的组合。具体地，我们可以考虑所有属性的组合，然后选择最优的属性组合进行划分。但是，由于属性组合的数量可能很大，因此我们通常采用启发式方法来选择属性组合。常用的启发式方法有：**前向选择**、**后向选择**和**双向选择**。
   1. **前向选择**：从空集开始，逐步添加属性，直到不能再添加属性为止。具体地，我们首先计算每个属性的信息增益，然后选择信息增益最大的属性作为第一个属性，然后逐步添加属性，直到信息增益不再增加。
   2. **后向选择**：从所有属性开始，逐步删除属性，直到不能再删除属性为止。具体地，我们首先计算每个属性的信息增益，然后选择信息增益最大的属性作为第一个属性，然后逐步删除属性，直到信息增益不再增加。
   3. **双向选择**：前向选择和后向选择的结合。具体地，我们首先计算每个属性的信息增益，然后选择信息增益最大的属性作为第一个属性，然后逐步添加属性，直到信息增益不再增加，然后逐步删除属性，直到信息增益不再增加。
   
3. **多变量决策树的优缺点**：多变量决策树的优点是可以更好地划分数据集，提高决策树的泛化性能。但是，多变量决策树的缺点是计算复杂度较高，因为需要考虑多个属性的组合。因此，多变量决策树通常用于数据集较小的情况。

4. **多变量决策树的应用**：多变量决策树在实际应用中有很多场景，例如：金融风控、医疗诊断、工业生产等。在这些场景中，多变量决策树可以更好地划分数据集，提高决策树的泛化性能。

   > 其与传统的单变量决策树不同的是，在多变量决策树的学习过程中，不是为每一个非叶节点寻找一个最优划分属性，而是试图建立一个合适的线性分类器。
