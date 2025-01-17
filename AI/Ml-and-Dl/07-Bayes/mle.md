# 7.2 极大似然估计
## 使用
1. 极大似然估计(Maximum Likelihood Estimation, MLE)是一种根据数据采样来估计概率分布的经典方法。它是建立在极大似然原理上的一种估计方法。

2. 极大似然原理的直观解释是：在所有可能的概率分布中，我们应该选择那个使得观测数据出现的概率最大的概率分布作为真实分布。如，一个随机试验如有若干个可能的结果A，B，C，... ，若在一次试验中，结果A出现了，那么可以认为实验条件对A的出现有利，也即出现的概率P(A)较大。

3. 极大似然估计的策略：先假定总体具有某种确定的概率分布，再基于训练样本对概率分布的参数进行估计。运用到类条件概率$P(x|c)$中，假设$P(x|c)$服从一个参数为 $\theta$ 的分布，问题就变为根据已知的训练样本来估计 $\theta$ 。极大似然法的核心思想就是：估计出的参数使得已知样本出现的概率最大，即使得训练数据的似然最大。

4. 极大似然估计：

	令$D_c$表示训练集中第c类样本组成的集合，假设这些样本是独立同分布的，则参数$\theta c$对于数据集$D_c$的似然为

$$
	L(\theta c|D_c) = P(D_c|\theta c) = \prod_{x \in D_c} P(x|\theta c)
$$

一般我们使用对数似然函数来简化计算：

$$
\begin{aligned}
    LL(\theta_c) & = \log P(\theta c|D_c) \\
    & = \sum_{x \in D_c} \log P(x|\theta c)
\end{aligned}
$$

此时，参数$\theta c$的极大似然估计$\hat{\theta c}$即为：

$$
\hat{\theta c} = \arg\max_{\theta c} LL(\theta c)
$$

   故，贝叶斯分类器的训练过程就是参数估计。

## 总结
1. 总结极大似然法估计参数的过程，一般为：
   1. 写出似然函数；
   2. 对似然函数取对数，并整理；
   3. 求导数，令其偏导数为0，得到似然方程组；
   4. 解似然方程组，得到所有参数即为所求。

2. 一个例子：假设样本属性都是连续值，$P(x|c)$服从一个多维高斯分布，则通过MLE计算出的参数分别为：
   
$$
    \begin{aligned}
        p(x|c) &\sim N(\mu_c, \sigma^2_c)\\
        \hat \mu_c &= \frac{1}{|D_c|} \sum_{x \in D_c} x\\
        \hat \sigma^2_c &= \frac{1}{|D_c|} \sum_{x \in D_c} (x - \hat \mu_c)(x - \hat \mu_c)^T
    \end{aligned}
$$

其中，$\hat \mu_c$为样本均值（向量），$\hat \sigma^2_c$为样本协方差（矩阵）。

上述结果看起来十分合乎实际，但是采用最大似然法估计参数的效果很大程度上依赖于作出的假设是否合理，是否符合潜在的真实数据分布，而这就需要大量的经验知识。
