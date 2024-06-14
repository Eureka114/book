# 9.6.层次聚类

* 层次聚类(hierarchical clustering)试图在不同层次对数据集进行划分, 从而形成树形的聚类结构.

* 数据集的划分可采用自底向上的聚合策略, 也可采用自顶向下的拆分策略.

* AGNES(AGglomerative NESting)是一种采用自底向上聚合策略的层次聚类算法.

	* 先将数据集中的每个样本看作一个初始聚类簇, 然后在算法运行的每一步中找出距离最近的两个聚类簇进行合并, 该过程不断重复, 直至达到预设的聚类簇个数.

* AGNES的关键是如何计算聚类簇之间的距离.

	* 每个簇是一个样本集合, 只需采用关于集合的某种距离即可. 
	* 集合间的距离计算常采用豪斯多夫距离(Hausdorff distance).
	* 给定聚类簇$C_i$与$C_j$可通过下面的式子来计算距离:
		* 最小距离: $d_\min(C_i, C_j)=\min\limits_{\pmb{x}\in C_i, \pmb{z}\in C_j}\text{dist}(\pmb{x}, \pmb{z})$
		* 最大距离: $d_\max(C_i, C_j)=\max\limits_{\pmb{x}\in C_i, \pmb{z}\in C_j}\text{dist}(\pmb{x}, \pmb{z})$
		* 平均距离: $d_\text{avg}(C_i, C_j)=\frac{1}{|C_i||C_j|}\sum\limits_{\pmb{x}\in C_i}\sum\limits_{\pmb{z}\in C_j}\text{dist}(\pmb{x}, \pmb{z})$
	* 最小距离由两个簇的最近样本决定, 最大距离由两个簇的最远样本决定, 而平均距离则由两个簇的所有样本共同决定.
	* 当聚类簇距离由$d_\min$、$d_\max$或$d_\text{avg}$计算时, AGNES 算法被相应地称为单链接(single-linkage)、全链接(complete-linkage)或均链接(average-linkage)算法.

* AGNES 算法描述

	![](https://img2.imgtp.com/2024/05/29/fTJOsOAp.png)

	* $d$ 通常使用$d_\min$, $d_\max$, $d_\text{avg}$.
* $i^*<j^*$.