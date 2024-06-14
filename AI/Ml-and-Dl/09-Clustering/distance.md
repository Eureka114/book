# 9.2 距离度量

## 距离度量的基本性质

对于给定的两个样本点$x_i$和$x_j$，假设它们的特征分别为$x_{i1},x_{i2},\ldots,x_{ip}$和$x_{j1},x_{j2},\ldots,x_{jp}$，则这两个样本点之间的距禽度量可以通过距离函数$d(x_i,x_j)$来度量。距离度量是定义在特征空间中的两个样本点之间的距离，是一个实数，满足下面的性质：

- 非负性：$d(x_i,x_j)\geq 0$；

- 同一性：$d(x_i,x_j)=0$当且仅当$x_i=x_j$；

- 对称性：$d(x_i,x_j)=d(x_j,x_i)$；

- 直递性（三角不等式）：$d(x_i,x_j)\leq d(x_i,x_k)+d(x_k,x_j)$。

## 闵可夫斯基距离
给定样本空间$X$中的两个样本点$x_i$和$x_j$，它们的特征分别为$x_{i1},x_{i2},\ldots,x_{ip}$和$x_{j1},x_{j2},\ldots,x_{jp}$，则这两个样本点之间的闵可夫斯基距离定义为

$$
    d_{mk}(x_i,x_j)=\left(\sum_{u=1}^p|x_{iu}-x_{ju}|^p\right)^{\frac{1}{p}}
$$

对于$p\ge1$时，闵可夫斯基距离满足距离度量的基本性质。

> 上式即为$x_i-x_j$的$L_p$范数$\lVert x_i-x_j\rVert_p$。

- 当$p=1$时，称为曼哈顿距离（Manhattan distance）；

$$
    d_{man}(x_i,x_j)=\sum_{u=1}^p|x_{iu}-x_{ju}|
$$

- 当$p=2$时，称为欧氏距离（Euclidean distance）；

$$
    d_{ed}(x_i,x_j)=\sqrt{\sum_{u=1}^p(x_{iu}-x_{ju})^2}
$$

- 当$p=\infty$时，称为切比雪夫距离（Chebyshev distance）。

$$
    d_{ch}(x_i,x_j)=\max_{u}|x_{iu}-x_{ju}|
$$

> 计算$\lim_{n\to\infty}\sqrt[n]{a_{1}^{n}+a_{2}^{n}+\ldots+a_{k}^{n}}$，其中$a_{i}>0(i=1,2,\ldots,k)$。
>
> 本题考**夹逼定理**的运用
>
> 设$A=\max_{1\leq i\leq k}\left\{ a_{i} \right\}$，则有
>
> 
> $$
>   \sqrt[n]{A^{n}}<\sqrt[n]{a_{1}^{n}+a_{2}^{n}+\ldots+a_{k}^{n}}<\sqrt[n]{kA^{n}}\\
    \sqrt[n]{A^{n}}=A,\lim_{n\to\infty}\sqrt[n]{k}=1
    $$
>
> 由三明治法则可得
> 
> $$
>   \lim_{n\rightarrow\infty}\sqrt[n]{a_{1}^{n}+a_{2}^{n}+\ldots+a_{k}^{n}}=\max_{1\leq i\leq k}\left\{ a_{i} \right\}=A
> $$

## 数据属性和距离

- 我们常常将属性划分为两种类型：连续属性（数值属性，numerical attribute）和离散属性（列名属性，nominal attribute）。

- 对于连续值的属性，一般都可以被学习器所用，有时会根据具体的情形作相应的预处理，例如：归一化等；而对于离散值的属性，需要作下面进一步的处理：

	> （有序属性，ordinal attribute）若属性值之间**存在序关系**，则可以将其转化为连续值，例如：身高属性“高”“中等”“矮”，可转化为{1，0。5，0}。
	> （无序属性，non-ordinal attribute）若属性值之间**不存在序关系**，则通常将其转化为向量的形式，例如：性别属性“男”“女”，可转化为{(1,0)，(0,1)}。

- 在进行距离度量时，易知**连续属性和存在序关系的离散属性都可以直接参与计算**，因为它们都可以反映一种程度，我们称其为“**有序属性**”。这时，可以使用闵可夫斯基距离直接计算。

- 而对于无序属性，我们一般采用VDM(Value Difference Metric)进行距离的计算。

- 令$m_{u, a}$表示在属性$u$上取值为$a$的样本数，$m_{u, a, i}$表示在第$i$个样本簇中在属性$u$上取值为$a$的样本数，$k$为样本簇数，则属性$u$ 上两个离散值$a$和$b$之间的VDM距离为

	$$
	    \text{VDM}_p(a,b)=\sum^k_{i=1}\Bigg|\frac{m_{u,a,i}}{m_{u,a}}-\frac{m_{u,b,i}}{m_{u,b}}\Bigg|^p
	$$

	样本类别已知时$k$通常设置为类别数。

- 将闵可夫斯基聚类和VDM结合即可处理混合属性。

- 假定有$n_c$个有序属性、$n-n_c$个无序属性，不失一般性，令有序属性排列在无序属性之前，则

	$$
	    \text{MinkovDM}_p(\pmb{x}_i, \pmb{x}_j)=\Bigg(\sum^{n_c}_{u=1}|x_{iu}-x_{ju}|^p+\sum^n_{u=n_c+1}\text{VDM}_p(x_{iu},x_{ju})\Bigg)^\frac{1}{p}
	$$


- 当样本空间中不同属性的重要性不同时，可使用加权距离(weighted distance)。 以加权闵可夫斯基距离为例:

	$$
	    \text{dist}_\text{wmk}(\pmb{x}_i, \pmb{x}_j)=(w_1|x_{iu}-x_{ju}|^p+\ldots+w_n|x_{nu}-x_{nu}|^p)^{\frac{1}{p}}
	$$

	其中权重$w_i\geq0(i=1,2,\ldots，n)$表征不同属性的重要性，通常$\sum^n_{i=1}w_i=1$。

- 通常我们是基于某种形式的距离来定义相似度度量(similarity measure)，距离越大，相似度越小。

- 相似度度量的距离未必一定要满足距离度量的所有基本性质，尤其是直递性。

- 不满足直递性的距离称为非度量距离(non-metric distance)。

- 在现实任务中，也可基于数据样本来确定合适的距离计算式，这可通过距离度量学习(distance metric learning)来实现。