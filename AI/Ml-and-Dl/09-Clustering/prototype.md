# 9.4 原型聚类

* 原型聚类亦称基于原型的聚类(prototype-based clustering), 此类算法假设聚类结构能通过一组原型刻画.
* 原型是指样本空间中具有代表性的点.
* 通常算法先对原型进行初始化, 然后对原型进行迭代更新求解.
* 采用不同的原型表示, 不同的求解方式, 将产生不同的算法.

## 9.4.1.k均值算法

* 给定样本集 $D=\{\pmb{x}_1,\pmb{x}_2,...,\pmb{x}_m\}$, $k$ 均值($k$-means)算法针对聚类所得簇划分$C=\{C_1, C_2, ..., C_k\}$最小化平方误差
  
	$$
		E=\sum^k_{i=1}\sum_{x\in C_i}||\pmb{x}-\pmb{\mu}_i||^2_2
	$$

	其中$\pmb{\mu}_i=\frac{1}{|C_i|}\sum_{\pmb{x}\in C_i}\pmb{x}$是簇$C_i$的均值向量.

	* 上式在一定程度上刻画了簇内样本围绕簇均值向量的紧密程度, $E$值越小则簇内样本相似度越高.

* 最小化上式要找到它的最优解需考察样本集$D$所有可能的簇划分, 这是一个$\text{NP}$难问题.

* $k$ 均值算法采用了贪心策略, 通过迭代优化来近似求解上式.

* $k$ 均值算法：

	![](https://img2.imgtp.com/2024/05/29/kYYE9SVd.png)

* 为避免运行时间过长, 通常设置一个最大运行轮数或最小调整幅度阈值, 若达到最大轮数或调整幅度小于阈值, 则停止运行.

## 9.4.2.学习向量量化

* 与$k$均值算法类似, 学习向量量化(Learning Vector Quantization, 简称LVQ)也是试图找到一组原型向量来刻画聚类结构.

* 与一般聚类算法不同的是, LVQ假设数据样本带有类别标记, 学习过程利用样本的这些监督信息来辅助聚类.

* LVQ可看作通过聚类来形成类别子类结构, 每个子类对应一个聚类簇.

* 给定样本集$D=\{(\pmb{x}_1, y_1), (\pmb{x}_2, y_2), ...,(\pmb{x}_m, y_m)\}$, 每个样本$\pmb{x}_j$是由$n$个属性描述的特征向量$(x_{j1}; x_{j2}; ... ;x_{jn})$, $y_j\in \mathcal{Y}$是样本$\pmb{x}_j$的类别标记.

* LVQ的目标是学得一组$n$维原型向量$\{\pmb{p}_1, \pmb{p}_2, ...,\pmb{p}_q\}$, 每个原型向量代表一个聚类簇, 簇标记$t_i\in\mathcal{Y}$.

* LVQ算法描述：

	![](https://img2.imgtp.com/2024/05/29/AVWHXyKK.png)

	* LVQ算法对原型向量进行初始化, 例如对第$q$个簇可从类别标记为$t_q$的样本中随机选取一个作为原型向量.
* 在每一轮迭代中, 算法随机选取一个有标记训练样本, 找出与其最近的原型向量, 并根据两者的类别标记是否一致来对原型向量进行相应的更新.
	* 算法的停止条件可设置为最大运行轮数或原型向量更新幅度很小.

* LVQ的关键是如何更新原型向量.

	* 对样本$\pmb{x}_j$, 若最近的原型向量$\pmb{p}_{i^*}$与$\pmb{x}_j$的类别标记相同, 则令$\pmb{p}_{i^*}$向$\pmb{x}_j$的方向靠拢.
  
	$$
		\pmb{p}'=\pmb{p}_{i^*}+\eta·(\pmb{x}_j-\pmb{p}_{i^*})
	$$

	* $\pmb{p}’$与$\pmb{x}_j$之间的距离为
  
	$$
		\begin{equation}
		\begin{aligned}
			||\pmb{p}’-\pmb{x}_j||_2&=||\pmb{p}_{i^*}+\eta·(\pmb{x}_j-\pmb{p}_{i^*})-\pmb{x}_j||_2\\
			&=(1-\eta)·||\pmb{p}_{i^*}-\pmb{x}_j||_2
		\end{aligned}
		\end{equation}
	$$

	令学习率$\eta\in(0, 1)$, 则原型向量$\pmb{p}_{i^*}$在更新为$\pmb{p}'$之后将更接近$\pmb{x_j}$.

	* 若$\pmb{p}_{i^*}$与$\pmb{x}_j$的类别标记不同, 则更新后的原型向量与$\pmb{x}_j$之间的距离将增大为$(1+\eta)·||\pmb{p}_{i^*}-\pmb{x}_j||_2$从而更远离$\pmb{x}_j$.

* 在学得一组原型向量$\{\pmb{p}_1, \pmb{p}_2, ...,\pmb{p}_q\}$后, 即可实现对样本空间$\mathcal{X}$的簇划分.

* 对任意样本$\pmb{x}$, 它将被划入与其距离最近的原型向量所代表的簇中.

* 每个原型向量$\pmb{p}_i$定义了与之相关的一个区域$R_i$, 该区域中每个样本与$\pmb{p}_i$的距离不大于它与其他原型向量$\pmb{p}_{i'}(i'\neq i)$的距离, 即
  
	$$
		R_i=\{\pmb{x}\in\mathcal{X}|\ ||\pmb{x}-\pmb{p}_i||_2\leqslant||\pmb{x}-\pmb{p}_{i'}||_2, i'\neq i\}
	$$

	* 由此形成了对样本空间$\mathcal{X}$的簇划分$\{R_1, R_2, ..., R_q\}$, 该划分通常称为Voronoi剖分(Voronoi tessellation).
	* 若将$R_i$中样本全用原型向量$\pmb{p}_i$表示, 则可实现数据的有损压缩(lossy compression). 这称为向量量化(vector quantization).

## 9.4.3.高斯混合聚类

* 与$k$均值、LVQ用原型向量来刻画聚类结构不同, 高斯混合(Mixture-of-Gaussian)聚类采用概率模型来表达聚类原型.

* (多元)高斯分布的定义. 对$n$维样本空间$\mathcal{X}$中的随机向量$\pmb{x}$, 若$\pmb{x}$若服从高斯分布, 其概率密度函数为

	$$
		p(\pmb{x})=\frac{1}{(2\pi)^\frac{n}{2}|\pmb{\tiny{\sum}}|^\frac{1}{2}}e^{-\frac{1}{2}(\pmb{x}-\pmb{\mu})^T\pmb{\tiny{\sum}}^{-1}(\pmb{x}-\pmb{\mu})}
	$$

	* 其中$\pmb{\mu}$是$n$维均值向量, $\pmb{\sum}$是的$n\times n$协方差矩阵.
	* 记为$\pmb{x}\sim\mathcal{N}(\pmb{\mu}, \pmb{\sum})$.
	* $\pmb{\sum}$: 对称正定矩阵; $|\pmb{\sum}|$: $\pmb{\sum}$的行列式; $\pmb{\sum}^{-1}$: $\pmb{\sum}$的逆矩阵.
	* 高斯分布完全由均值向量$\pmb{\mu}$和协方差矩阵$\pmb{\sum}$这两个参数确定.

* 为了明确显示高斯分布与相应参数的依赖关系, 将概率密度函数记为$p(\pmb{x}|\pmb{\mu}, \pmb{\tiny{\sum}})$.

* 高斯混合分布的定义
  
	$$
		p_{\mathcal{M}}(\pmb{x})=\sum^k_{i=1}\alpha_i·p(\pmb{x}|\pmb{\mu}_i,\pmb{\tiny{\sum}}_i)
	$$

	* $p_{\mathcal{M}}(·)$也是概率密度函数, $\int p_{\mathcal{M}}(\pmb{x})d\pmb{x}=1$.
	
	* 该分布是由$k$个混合分布组成, 每个混合成分对应一个高斯分布.
	
	* 其中$\pmb{\mu}_i$与$\pmb{\sum}_i$是第$i$个高斯混合分布的参数, 而$\alpha_i>0$为相应的混合系数(mixture coefficient), $\sum^k_{i=1}\alpha_i=1$.
	
	* 假设样本的生成过程由高斯混合分布给出: 首先, 根据$\alpha_1,\alpha_2,..., \alpha_k$定义的先验分布选择高斯混合成分, 其中$\alpha_i$为选择第$i$个混合成分的概率; 然后, 根据被选择的混合成分的概率密度函数进行采样, 从而生成相应的样本.
	
	* 若训练集$D=\{\pmb{x}_1, \pmb{x}_2, ..., \pmb{x}_m\}$由上述过程生成, 令随机变量$z_j\in\{1,2, ..., k\}$表示生成样本$\pmb{x}_j$的高斯混合分布, 其取值未知. $z_j$的先验概率$P(z_j=i)$对应于$\alpha_i(i=1,2,...,k)$.
	
	* 根据贝叶斯定理, $z_j$的后验分布对应于
  
	$$
		\begin{equation}
			\begin{aligned}
				p_\mathcal{M}(z_j=i|\pmb{x}_j)&=\frac{P(z_j=i)·p_\mathcal{M}(\pmb{x}_j|z_j=i)}{p_\mathcal{M}(\pmb{x}_j)}\\
				&=\frac{\alpha_i·p(\pmb{x}_j|\pmb{\mu}_i,\pmb{\sum}_i)}{\sum\limits^k_{l=1}\alpha_l·p(\pmb{x}_j|\pmb{\mu}_l,\pmb{\mathcal{\sum}}_l)}
			\end{aligned}
		\end{equation}
	$$

	换言之, $p_\mathcal{M}(z_j=i|\pmb{x}_j)$给出了样本$\pmb{x}_j$由第$i$个高斯混合成分生成的后验概率. 为方便叙述, 将其简记为$\gamma_{ji}\ (i=1, 2, ..., k)$.
	
	* 当高斯混合分布已知时, 高斯混合聚类将把样本集$D$划分为$k$个簇$C=\{C_1, C_2, ..., C_k\}$, 每个样本$\pmb{x}_j$的簇标记$\lambda_j$如下确定:
  
	$$
	\lambda_j=\mathop{\arg\max}_\limits{i\in\{1,2,...,k\}}\ \gamma_{ji}
	$$
	
	从原型聚类的角度来看, 高斯混合聚类是采用概率模型(高斯分布)对原型进行刻画, 簇划分则由原型对应后验概率确定.
	
	* 对于高斯混合分布的定义, 模型参数$\{(\alpha_i, \pmb{\mu}_i, \pmb{\sum}_i)|1\leqslant i\leqslant k\}$, 在给定样本集$D$的求解, 可采用极大似然估计, 即最大化(对数)似然
  
	$$
		\begin{equation}
			\begin{aligned}
				LL(D)&=\ln\Bigg(\prod^m_{j=1}p_\mathcal{M}(\pmb{x}_j)\Bigg)\\
				&=\sum^m_{j=1}\ln\bigg(\sum^k_{i=1}\alpha_i·p(\pmb{x}_j|\pmb{\mu}_i, \sum_i)\bigg)
			\end{aligned}
		\end{equation}
	$$

	采用EM算法进行迭代优化求解.
	
	* 若参数$\{(\alpha_i, \pmb{\mu}_i, \pmb{\sum}_i)|1\leqslant i\leqslant k\}$ 能使上式最大化, 则$\frac{\partial LL(D)}{\partial\pmb{\mu}_i}=0$有

	$$
		\sum^m_{j=1}\frac{\alpha_i·p(\pmb{x}_j|\pmb{\mu}_i,\sum_i)}{
		\sum^k_{l=1}\alpha_l·p(\pmb{x}_j|\pmb{\mu}_l,\sum_l)
		}(\pmb{x}_j-\pmb{\mu}_i)=0
	$$
	
	* 由$p_\mathcal{M}(z_j=i|\pmb{x}_j)=\frac{\alpha_i·p(\pmb{x}_j|\pmb{\mu}_i,\pmb{\sum}_i)}{\sum\limits^k_{l=1}\alpha_l·p(\pmb{x}_j|\pmb{\mu}_l,\pmb{\mathcal{\sum}}_l)}$以及, $\gamma_{ji}=p_\mathcal{M}(z_j=i|\pmb{x}_j)$, 有

	$$
		\pmb{\mu}_i=\frac{\sum\limits^m_{j=1}\gamma_{ji}\pmb{x}_j}{\sum\limits^m_{j=1}\gamma_{ji}}
	$$

	即各混合成分的均值可通过样本加权平均来估计, 样本权重是每个样本属于该成分的后验概率. 
	
	* 类似的, 由$\frac{\partial LL(D)}{\partial\sum_i}=0$可得

	$$
		\sum_\nolimits i=\frac{\sum\limits^m_{j=1}\gamma_{ji}(\pmb{x}_j-\pmb{\mu}_i)(\pmb{x}_j-\pmb{\mu}_i)^T}{\sum\limits^m_{j=1}\gamma_{ji}}
	$$

* 对于混合系数$\alpha_i$, 除了要最大化$LL(D)$, 还需满足$\alpha_i\geqslant 0$, $\sum^k_{i=1}\alpha_i=1$.

* 考虑$LL(D)$的拉格朗日形式:

	$$
	LL(D)+\lambda\bigg(\sum^k_{i=1}\alpha_i-1\bigg)
	$$

	其中$\lambda$为拉格朗日乘子, 由上式对$\alpha_i$的导数为0, 有

	$$
	\sum^m_{j=1}\frac{p(x_j|\pmb\mu_i,\sum_i)}{\sum\limits^k_{l=1}\alpha_l·p(x_j|\pmb\mu_l,\sum_l)}+\lambda=0
	$$

	两边同乘以$\alpha_i$, 对所有混合成分求和可知$\lambda=-m$, 有

	$$
	\alpha_i=\frac{1}{m}\sum^m_{j=1}\gamma_{ji}
	$$
	
	即每个高斯成分的混合系数由样本属于该成分的平均后验概率确定.
	
	* 即上述推导即可获得高斯混合模型的EM算法: 在每步迭代中, 先根据当前参数来计算每个样本属于每个高斯成分的后验概率$\gamma_{ji}$ (E步), 再根据$\pmb{\mu}_i=\frac{\sum^m_{j=1}\gamma_{ji}\pmb{x}_j}{\sum^m_{j=1}\gamma_{ji}}$, $\sum_i=\frac{\sum^m_{j=1}\gamma_{ji}(\pmb{x}_j-\pmb{\mu}_i)(\pmb{x}_j-\pmb{\mu}_i)^T}{\sum^m_{j=1}\gamma_{ji}}$和$\alpha_i=\frac{1}{m}\sum^m_{j=1}\gamma_{ji}$更新模型参数$\{(\alpha_i,\pmb{\mu}_i,\sum_i)|1\leqslant i\leqslant k\}$ (M步).
	
	* 高斯混合聚类算法描述:
	
		![](https://img2.imgtp.com/2024/05/29/aiUcxmd8.png)
	
		* 第3-5行EM算法的E步, 第6-11行EM算法的M步.
	* 算法的停止条件可设置为最大迭代轮数或似然函数$LL(D)$增长很少甚至不再增长, 第14-17行根据高斯混合分布确定簇划分.