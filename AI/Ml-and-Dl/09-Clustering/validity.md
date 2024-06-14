# 9.3 性能度量

* 聚类性能度量亦称聚类有效性指标(validity index)；与监督学习中的性能度量作用类似。

* 对聚类结果，我们需通过某种性能度量来评估其好坏。

* 明确最终将要使用的性能度量，则可直接将其作为聚类过程的优化目标。

* 同一簇的样本尽可能彼此相似，不同簇的样本尽可能不同。 聚类结果的簇内相似度(intra-cluster similarity)高且簇间相似度(inter-cluster similarity)低。

* 聚类性能度量大致有两类。 

	1. 将聚类结果与某个参考模型(reference model)进行比较，称为外部指标(external index)。
		* 可以将领域专家给出的划分结果作为参考模型。
	2. 直接考察聚类结果而不用任何参考模型，称为内部指标(internal index)。

* 对数据集$D=\{\pmb{x}_1,\pmb{x}_2,\ldots,\pmb{x}_m\}$，假定通过聚类给出的簇划分为$C=\{C_1,C_2,\ldots,C_k\}$，参考模型给出的簇划分为$C^*=\{C^*_1,C^*_2,\ldots,C^*_s\}$。

	* 通常$k \neq s$。

	* 令$\pmb\lambda$与$\pmb\lambda^*$分别表示与$C$和$C^*$对应的簇标记向量。

	* 将样本两两配对考虑，定义
  
    $$
		\begin{equation}
			\begin{aligned}
		    a&=|SS|,\quad SS=\{(\pmb{x}_i, \pmb{x}_j)|\lambda_i=\lambda_j, \lambda_i^*=\lambda_j^*,i<j\}, \\
		    b&=|SD|,\quad SD=\{(\pmb{x}_i, \pmb{x}_j)|\lambda_i=\lambda_j, \lambda_i^*\neq\lambda_j^*,i<j\}, \\
		    c&=|DS|,\quad DS=\{(\pmb{x}_i, \pmb{x}_j)|\lambda_i\neq\lambda_j, \lambda_i^*=\lambda_j^*,i<j\}, \\
		    d&=|DD|,\quad DD=\{(\pmb{x}_i, \pmb{x}_j)|\lambda_i\neq\lambda_j, \lambda_i^*\neq\lambda_j^*,i<j\}, \\
			\end{aligned}
		\end{equation}
    $$

		其中集合$SS$包含了在$C$中隶属于相同簇且在$C^*$中也隶属于相同簇的样本对，集合$SD$包含了在$C$中隶属于相同簇但在$C^*$中隶属于不同簇的样本对，由于每个样本对$(\pmb{x}_i，\pmb{x}_j)(i<j)$仅能出现在一个集合中，因此有$a+b+c+d=m(m-1)/2$成立。

* 基于上式可以导出常用的聚类性能度量外部指标: 

	* Jaccard系数(Jaccard Coefficient，简称$\text{JC}$)
  
    $$
		JC=\frac{a}{a+b+c}
    $$

	* FM指数(Folkeds and Mallows Index，简称$\text{FMI}$)
  
    $$
		FMI=\sqrt{\frac{a}{a+b}·\frac{a}{a+c}}
    $$

	* Rand指数(Rand Index，简称$\text{RI}$)
  
    $$
		RI=\frac{2(a+d)}{m(m-1)}
    $$

		上述性能度量的结果值均在$[0，1]$区间，值越大越好。

* 考虑聚类结果的簇划分为$C=\{C_1,C_2,\ldots,C_k\}$，定义
  
$$
	\begin{equation}
		\begin{aligned}
			\text{avg}(C)&=\frac{2}{|C|(|C|-1)}\sum_\nolimits{{1\leq i<j\leq|C|}}\text{dist}(\pmb{x}_i, \pmb{x}_j), \\
			\text{diam}(C)&=\max_\nolimits{{1\leq i<j\leq|C|}}\text{dist}(\pmb{x}_i, \pmb{x}_j), \\
			\text d_\min(C_i, C_j)&=\min_\nolimits{\pmb{x}_i\in C_i, \pmb{x}_j\in C_j}\text{dist}(\pmb{x}_i, \pmb{x}_j), \\
			\text d_{\text{cen}}(C_i, C_j)&=\text{dist}(\pmb{\mu}_i, \pmb{\mu}_j),
		\end{aligned}
	\end{equation}
$$

$\text{dist}(·,·)$用于计算两个样本之间的距离，距离越大则样本的相似度越低； $\pmb{\mu}$代表簇$C$的中心点$\pmb{\mu}=\frac{1}{|C|}\sum_\nolimits{1\leq i\leq|C|}\pmb{x}_i$。 

* $\text{avg}(C)$对应于簇$C$内样本间的平均距离。

* $\text{diam}(C)$对应于簇$C$内样本间的最远距离。

* $d_\min(C_i,C_j)$对应于簇$C_i$与簇$C_j$最近样本间的距离。

* $d_\text{cen}(C_i,C_j)$对应于簇$C_i$与簇$C_j$中心点间的距离。

	---

	

* 基于上式可导出常用的聚类性能度量的内部指标:

	* DB指数(Davies-Bouldin Index，简称$\text{DBI}$)
  
    $$
		DBI=\frac{1}{k}\sum^k_{i=1}\max_{j\neq i}\Bigg(\frac{\text{avg}(C_i)+\text{avg}(C_j)}{d_{\text{cen}}(C_i, C_j)}\Bigg)
    $$

	* Dunn指数(Dunn Index，简称$\text{DI}$)
  
    $$
		DI=\min_{1\leq i\leq k}\Bigg\{\min_{j\neq i}\Bigg(\frac{d_\min(C_i, C_j)}{\max_\nolimits{1\leq l \leq k}\text{diam}(C_l)}\Bigg)\Bigg\}
    $$

		显然，DBI的值越小越好，DI的值越大越好。