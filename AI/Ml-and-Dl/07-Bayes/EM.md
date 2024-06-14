# 7.6 EM算法

## EM算法介绍

期望最大化算法（Expectation Maximization Algorithm, EM）是一种迭代算法，用于含有隐变量的概率模型参数的极大似然估计。EM算法主要应用于训练集样本不完整即存在隐变量时的情形（例如某个属性值未知），通过其独特的“两步走”策略能较好地估计出隐变量的值。

## EM算法的思想

1. EM算法是一种迭代式的方法，其基本思想是：若样本服从的分布参数 $\theta$ 已知，则可以根据已观测到的训练样本推断出隐变量 $Z$ 的期望值（E步），若 $Z$ 的值已知则运用最大似然法估计出新的 $\theta$ 值（M步）。重复这个过程直到 $Z$ 和 $\theta$ 值不再发生变化。

2. 具体例子：设我们想估计A和B这两个参数，在开始状态下二者都是未知的，但如果知道了A的信息就可以得到B的信息，反过来知道了B也就得到了A。可以考虑首先赋予A某种初值，以此得到B的估计值，然后从B的当前值出发，重新估计A的取值，这个过程一直持续到收敛为止。即：

	> - 基于$\Theta^t$推断隐变量$Z$的期望，记为$Z^t$；（离散值：取概率的最大值；连续值：计算期望值）
	> - 基于已观测变量$X$和$Z^t$对参数作极大似然估计，记作$\Theta^{t+1}$；
	> - 以上步骤，反复迭代，直到收敛。

## EM算法的数学推导

 “边际似然”(Marginal Likelihood)：当样本属性已知时，采用极大化对数似然，后对每个参数求偏导计算出参数的值。而当存在隐变量时，由于隐变量的存在，无法直接计算似然函数，此时需要引入边际似然的概念。
   
$$
    LL(\Theta\mid X)=\ln P(X\mid\Theta)=\ln\sum_Z P(X,Z\mid\Theta)
$$

现在与最大似然不同的只是似然函数式中多了一个未知的变量Z，也就是说我们的目标是找到适合的θ和Z让L(θ)最大，这样我们也可以分别对未知的θ和Z求偏导，再令其等于0。

化简：我们有：

$$
   \begin{aligned}
   \sum_i \log p(x^i;\theta)&=\sum_i \log \sum_{z^i} p(x^i,z^i;\theta)\\
    &=\sum_i \log \sum_{z^i} q(z^i) \frac{p(x^i,z^i;\theta)}{q(z^i)}\\
    &\geq \sum_i \sum_{z^i} q(z^i) \log \frac{p(x^i,z^i;\theta)}{q(z^i)}\\
   \end{aligned}
$$

其中，因为对数函数是上凸函数，所以在最后一步，使用了Jensen不等式，将上式“和的对数”变为“对数的和”，这样就非常容易求导了。

接着，求解$q_i,\theta$：

$$
   \begin{aligned}
   \frac{\partial}{\partial \theta} \sum_i \sum_{z^i} q(z^i) \log \frac{p(x^i,z^i;\theta)}{q(z^i)}&=0\\
   \frac{\partial}{\partial q(z^i)} \sum_i \sum_{z^i} q(z^i) \log \frac{p(x^i,z^i;\theta)}{q(z^i)}&=0
   \end{aligned}
$$

通过求解上述两个方程，我们可以得到$q_i,\theta$的迭代公式。

简单的理解：固定θ计算Q的过程就是在建立$L(\theta)$的下界，即通过琴生不等式得到的下界（E步）；固定Q计算θ则是使得下界极大化（M步），从而不断推高边缘似然$L(\theta)$。从而循序渐进地计算出$L(\theta)$取得极大值时隐变量Z的估计值。

EM算法也可以看作一种“坐标下降法”，首先固定一个值，对另外一个值求极值，不断重复直到收敛。

![6.png](https://i.loli.net/2018/10/18/5bc843c34e7ff.png)

## EM算法的流程

这里有不同的版本，互相了解，学习。

**版本一：**

![7.png](https://i.loli.net/2018/10/18/5bc843c0e19db.png)

**版本二：**

![8.png](https://i.loli.net/2018/10/18/5bc843c34775b.png)

