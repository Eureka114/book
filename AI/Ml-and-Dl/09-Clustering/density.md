# 9.5.密度聚类

* 密度聚类亦称基于密度的聚类(density-based clustering), 此类算法假设聚类结构能通过样本分布的紧密程度确定.

* 密度聚类算法从样本密度的角度来考虑样本之间的可连接性, 并基于可连接样本不断扩展聚类簇以获得最终的聚类结果.

* DBSCAN(Density-Based Spatial Clustering of Applications with Noise)是一种著名的密度聚类算法, 它基于一组邻域(neighborhood)参数$(\epsilon, MinPts)$来刻画样本分布的紧密程度.

* 给定数据集$D=\{\pmb{x}_1,\pmb{x}_2,...,\pmb{x}_m\}$, 定义下面这几个概念:

	* $\epsilon$-邻域: 对$\pmb{x}_j\in D$, 其$\epsilon$-邻域包含样本集$D$中与$\pmb{x}_j$的距离不大于$\epsilon$的样本, 即$N_\epsilon(\pmb{x}_j)=\{\pmb{x}_i\in D|\text{dist}(\pmb{x}_i,\pmb{x}_j)\leqslant\epsilon\}$;
	* 核心对象(core object): 若$\pmb{x}_j$的$\epsilon$-领域至少包含$MinPts$个样本, 即$|{N_\epsilon(\pmb{x}_j)}|\geqslant MinPts$, 则是一个核心对象$\pmb{x}_j$;
	* 密度直达(directly density-reachable): 若$\pmb{x}_j$位于$\pmb{x}_i$的$\epsilon$-领域中, 且$\pmb{x}_i$是核心对象, 则称$\pmb{x}_j$由$\pmb{x}_i$密度直达;
		* 密度直达关系通常不满足对称性.
	* 密度可达(density-reachable): 对$\pmb{x}_i$与$\pmb{x}_j$, 若存在样本序列$\pmb{p}_1, \pmb{p}_2, ..., \pmb{p}_n$, 其中$\pmb{p}_1=\pmb{x}_i$, $\pmb{p}_n=\pmb{x}_j$且$\pmb{p}_{i+1}$由$\pmb{p}_i$密度直达, 则称$\pmb{x}_j$由$\pmb{x}_i$密度可达.
		* 密度可达关系满足直递性, 但不满足对称性.
	* 密度相连(density-connected): 对$\pmb{x}_i$与$\pmb{x}_j$, 若存在$\pmb{x}_k$使得$\pmb{x}_i$与$\pmb{x}_j$均由$\pmb{x}_k$密度可达, 则称$\pmb{x}_i$与$\pmb{x}_j$密度相连.
		* 密度相连关系满足对称性.

* DBSCAN将簇定义为: 有密度可达关系导出的最大的密度相连样本集合.

	* $D$中不属于任何簇的样本被认为是噪声(noise)或者异常(anomaly)样本.
	* 给定邻域参数$(\epsilon, MinPts)$, 簇$C\subseteq D$是满足以下性质的非空样本子集:
		* 连接性(connectivity): $\pmb{x}_i\in C$, $\pmb{x}_j\in C\Rightarrow\pmb{x}_i$与$\pmb{x}_j$密度相连
		* 最大性(maximality): $\pmb{x}_i\in C$, $\pmb{x}_j$由$\pmb{x}_i$密度可达 $\Rightarrow\pmb{x}_j\in C$

* 若$\pmb{x}$为核心对象, 由$\pmb{x}$密度可达的所有样本组成的集合记为$X=\{\pmb{x}'\in D|\pmb{x}'$ 由 $\pmb{x}$ 密度可达$\}$, 则可证明$X$即为满足连续性和最大性的簇.

* DBSCAN 算法任选数据集中的一个核心对象为种子(seed), 再由此出发确定相应的聚类簇.

* DBSCAN 算法描述

	![](https://img2.imgtp.com/2024/05/29/tUc56TB1.png)
