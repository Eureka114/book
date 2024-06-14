# 10.1 K近邻学习

## 前言

- 样本的特征数称为**维数**（dimensionality），当维数非常大时，也就是现在所说的“**维数灾难**”。具体表现在：
	- 在高维情形下，**数据样本将变得十分稀疏**，因为此时要满足训练样本为“**密采样**”的总体样本数目过于庞大。**训练样本的稀疏使得其代表总体分布的能力大大减弱，从而消减了学习器的泛化能力**；
	- 在高维情形下，**计算距离也变得十分复杂**，甚至连计算内积都不再容易，这也是为什么支持向量机（SVM）使用核函数**“低维计算，高维表现”**的原因。

- 缓解维数灾难的一个重要途径就是**降维，即通过某种数学变换将原始高维空间转变到一个低维的子空间**。在这个子空间中，样本的密度将大幅提高，同时距离计算也变得容易。

> - 这样降维之后不是会丢失原始数据的一部分信息吗？
>
> - 这是因为在很多实际的问题中，虽然训练数据是高维的，但是与学习任务相关也许仅仅是其中的一个低维子空间，也称为一个**低维嵌入**，例如：数据属性中存在噪声属性、相似属性或冗余属性等，**对高维数据进行降维能在一定程度上达到提炼低维优质属性或降噪的效果**。

## k近邻学习

- k近邻算法，简称kNN(k-Nearest Neighbor)，是一种经典的监督学习方法，同时也实力担当入选数据挖掘十大算法。
- 其工作机制十分简单粗暴：给定某个测试样本，kNN基于某种**距离度量**在训练集中找出与其距离最近的$k$个带有真实标记的训练样本，然后给基于这$k$个邻居的真实标记来进行预测，类似于前面集成学习中所讲到的基学习器结合策略：分类任务采用投票法，回归任务则采用平均法。以kNN分类为例：

> ![1.png](https://i.loli.net/2018/10/18/5bc851a43873a.png)
>
> 图中有两种类型的样本，一类是蓝色正方形，另一类是红色三角形。而那个绿色圆形是我们待分类的样本。基于kNN算法的思路，我们很容易得到以下结论：
>
> - 如果$k=3$，那么离绿色点最近的有2个红色三角形和1个蓝色的正方形，这3个点投票，于是绿色的这个待分类点属于红色的三角形。  
> - 如果$k=5$，那么离绿色点最近的有2个红色三角形和3个蓝色的正方形，这5个点投票，于是绿色的这个待分类点属于蓝色的正方形。

- 可以发现：**kNN虽然是一种监督学习方法，但是它却没有显式的训练过程**，而是当有新样本需要预测时，才来计算出最近的$k$个邻居，因此**kNN是一种典型的懒惰学习方法**，再来回想一下朴素贝叶斯的流程，训练的过程就是参数估计，因此朴素贝叶斯也可以懒惰式学习，此类技术在**训练阶段开销为零**，待收到测试样本后再进行计算。相应地我们称那些一有训练数据立马开工的算法为“**急切学习**”，可见前面我们学习的大部分算法都归属于急切学习。

- 很容易看出：**kNN算法的核心在于k值的选取以及距离的度量**。k值选取太小，模型很容易受到噪声数据的干扰，例如：极端地取$k=1$，若待分类样本正好与一个噪声数据距离最近，就导致了分类错误；若$k$值太大， 则在更大的邻域内进行投票，此时模型的预测能力大大减弱，例如：极端取$k=$训练样本数，就相当于模型根本没有学习，所有测试样本的预测结果都是一样的。**一般地我们都通过交叉验证法来选取一个适当的$k$值**。

	![2.png](https://i.loli.net/2018/10/18/5bc851a47db9a.png)

- 对于距离度量，**不同的度量方法得到的k个近邻不尽相同，从而对最终的投票结果产生了影响**，因此选择一个合适的距离度量方法也十分重要。在上一篇聚类算法中，在度量样本相似性时介绍了常用的几种距离计算方法，包括**闵可夫斯基距离，曼哈顿距离，VDM**等。在实际应用中，**kNN的距离度量函数一般根据样本的特性来选择合适的距离度量，同时应对数据进行去量纲/归一化处理来消除大量纲属性的强权政治影响**。

- 这里，若给定测试样本$x$，若其最近邻样本为$z$，则最近邻分类器出错的概率就是$x$与$z$类别标记不同的概率，即

$$
	P(err)=1-\sum_{c\in y}P(c\mid x)P(x\mid z).
$$

- 假设样本独立同分布，且对任意$x$和任意小正数$\delta$,在$x$附近$\delta$距离范围内总能找到一个训练样本；换言之,对任意测试样本，总能在任意近的范围内找到上式的训练样本$z$。令$c^*=\arg\max\limits_{c\in y}P(c\mid x)$表示贝叶斯最优分类器的结果，则有：

$$
	\begin{align}
		P(err)&\approx 1-\sum_{c\in y} P^2(c\mid x)\le 1-p^2(c^*\mid x)\\
		&=(1+P(c^*\mid x))(1-P\mid x)\le 2(1-P\mid x).
	\end{align}
$$

**最近邻分类器虽简单，但它的泛化错误率不超过贝叶斯最优分类器的错误率的两倍！**