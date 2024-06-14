# 3.3 对数几率回归

## 引入

1. 3.2节的线性回归模型适合于回归任务。若要做分类任务，则需要使用广义线性模型$y=g^{-1}(\mathbf{w}^T\mathbf{x}+b)$。

   > 即找一个单调可微函数，将分类任务的真实标记$y$与线性回归模型的预测值联系起来。

2. 考虑二分类问题：其输出标志为$y\in\{0,1\}$，而线性回归模型产生的预测值$z\in\mathbb{R}$。找到一种映射$f:\mathbb{R}\to\{0,1\}$，且$f(\cdot)$单调可微。（即找到一个Sigmoid函数）

3. Sigmoid函数：Sigmoid函数即形似**S**的函数。其中，对数几率函数是一种常见的Sigmoid函数。
   
$$
   y=\frac{1}{1+e^{-z}}
$$

![9.png](https://i.loli.net/2018/10/17/5bc722b0c7748.png)

## 对数几率和对率回归

- 对数几率(Log Odds或Logit)：若将对数几率函数作为$g^-(\cdot)$并代入广义线性模型函数，有
  
$$
   \begin{aligned}
   y=\frac{1}{1+e^{-(\mathbf{w}^T\mathbf{x}+b)}}\\
   \ln\frac{y}{1-y}=\mathbf{w}^T\mathbf{x}+b
   \end{aligned}
$$

- 若将y看做样本为正例的概率，（1-y）看做样本为反例的概率，则上式实际上使用线性回归模型的预测结果器逼近真实标记的对数几率。因此这个模型称为“对数几率回归”（logistic regression），也有一些书籍称之为“逻辑回归”。

- 对率回归实际上是一种分类学习方法。它的优点有：

   1. 直接对分类可能性进行建模，无需事先假设数据分布，这样就避免了假设分布不准确所带来的问题。
   2. 不是仅预测出“类别”，而是可得到近似概率预测，这对许多需利用概率辅助决策的任务很有用。
   3. 对率函数是任意阶可导的凸函数，有很好的数学性质，现有的许多数值优化算法都可直接用于求取最优解。

- 如何确定w和b？若将y视作类后验概率估计$$p(y=1\mid x)$$，则
  
$$
   \ln\frac{p(y=1\mid x)}{p(y=0\mid x)}=\mathbf{w}^T\mathbf{x}+b
$$

其中，

$$
   \begin{aligned}
   p(y=1\mid x)=\frac{e^{-(\mathbf{w}^T\mathbf{x}+b)}}{1+e^{-(\mathbf{w}^T\mathbf{x}+b)}}\\
   p(y=0\mid x)=\frac{1}{1+e^{-(\mathbf{w}^T\mathbf{x}+b)}}
   \end{aligned}
$$

我们可通过“极大似然法”(maximum likelihood method)来估计w,b。即，给定数据集$D=\{(\mathbf{x}_1,y_1),(\mathbf{x}_2,y_2),\ldots,(\mathbf{x}_N,y_N)\}$，假设这些样本是独立同分布的，我们可写出似然函数：

$$
   \begin{aligned}
   l(\mathbf{w},b)&=\sum_{i=1}^N\ln p(y_i\mid\mathbf{x}_i;\mathbf{w},b)\\
   \end{aligned}
$$

即令令每个样本属于其真实标记的概率越大越好。令$\beta=(\mathbf{w},b),\hat x=(\mathbf{x};1)$，再令$p_1(\hat x;\beta)=p(y=1\mid \hat x,\beta)$$，$$p_0(\hat x;\beta)=1-p_1(\hat x;\beta)$，则

$$
   \begin{aligned}
   l(\beta)&=\sum_{i=1}^N\left(-y_i\beta^T\hat x_i+\ln(1+e^{\beta^T\hat x_i})\right)
   \end{aligned}
$$

这是关于$\beta$的凸函数，可通过梯度下降法等方法求解。
