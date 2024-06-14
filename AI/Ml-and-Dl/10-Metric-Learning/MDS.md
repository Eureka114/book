# 10.2 MDS算法

## 维数灾难和降维

- 在高维情形下出现的数据样本稀疏、距离计算困难等问题，是所有机器学习方法共同面临的严重障碍，被称为“维数灾难”(Curse of Dimensionality)。
- 缓解维数灾难的一个重要途径是降维(Dimension Reduction)，亦称“维数约简”，即通过某种数学变换将原始高维属性空间转变为一个低维“子空间”(Subspace)，在这个子空间中样本密度大幅提高，距离计算也变得更为容易。

## 多维缩放

- 不管是使用核函数升维还是对数据降维，我们都希望**原始空间样本点之间的距离在新空间中基本保持不变**，这样才不会使得原始空间样本之间的关系及总体分布发生较大的改变。**“多维缩放”（MDS）**正是基于这样的思想，**MDS要求原始空间样本之间的距离在降维后的低维空间中得以保持**。
- 假定$m$个样本在原始空间中任意两两样本之间的距离矩阵为$D \in \mathbb{R}^{m \times m}$，我们的目标便是获得样本在低维空间中的表示$\mathbf{Z} \in \mathbb{R}^{d^{\prime} \times m}, d^{\prime} \leqslant d$，且任意两个样本在低维空间中的欧式距离等于原始空间中的距离，即$\|z_i-z_j\|=dist_{ij}$。因此接下来我们要做的就是根据已有的距离矩阵$D$来求解出降维后的坐标矩阵$Z$。

> 令$B=Z^TZ \in \mathbb{R}^{m \times m}$，其中$B$为降维后样本的内积矩阵，$b_{ij}=z_i^Tz_j$。有
>
>  
> $$
> \begin{aligned}
>     \text{dist}_{i j}^{2} 
>  &=\|z_i\|^2+\|z_j\|^2-2 z_i^T z_j \\
>  &=b_{i i}+b_{j j}-2 b_{i j} 
> \end{aligned}
> $$
> 
>
> 该式表示高维距离=低维欧式距离。



- 令降维后的样本坐标矩阵$Z$被中心化，**中心化是指将每个样本向量减去整个样本集的均值向量，故所有样本向量求和得到一个零向量**。这样易知：矩阵$B$的每一列以及每一列求和均为0，因为提取公因子后都有一项为所有样本向量的和向量。


$$
    B=\left[\begin{array}{l}{z_1} \\ \cdots \\ {z_m}\end{array}\right] \times \left[\begin{array}{lll} z_1 & \cdots & z_m\end{array}\right] = \left[\begin{array}{cccc}
    {z_1 z_1} & {z_1 z_2} & {\dots} & {z_1 z_m} \\ 
    {z_2 z_1} & {z_2 z_2} & {\dots} & {z_2 z_m} \\ 
    {\dots} & {\dots} & {\dots} & {\dots} 
    \\ {z_m z_1} & {z_m z_2} & {\dots} & {z_m z_m}
    \end{array}\right]
$$


其中$\sum\limits_{i=1}^{m} z_{i j}=\sum\limits_{j=1}^{m} z_{i j}=0$ 。

- 根据上面矩阵B的特征，我们很容易得到：

	
$$
\begin{aligned}
    \sum_{i=1}^m \text{dist}_{i j}^{2}=\text{tr}(B)+m b_{j j} \sim *1/m\\
    \sum_{j=1}^m \text{dist}_{i j}^2=\text{tr}(B)+m b_{i i} \sim *1/m \\
    \sum_{i=1}^m \sum_{j=1}^m \text{dist}_{i j}^{2}=2m \cdot \text{tr}(B) \sim *1/m^2
\end{aligned}
$$


其中$\displaystyle \text{tr}(B)=\sum_{i=1}^m \|z_i\|^2$  。

- 这时根据(1)--(4)式我们便可以计算出$b_{ij}$，即

	
$$
    b_{ij}=式(1)-式(2)*(1/m)-式(3)*(1/m)+式(4)*(1/m^2)
$$


再逐一地计算每个$b_{ij}$，就得到了降维后低维空间中的内积矩阵$B(B=Z'Z)$，只需对$B$进行特征值分解便可以得到$Z$。

​	

MDS的算法流程如下图所示：

> 输入：距离矩阵$D \in \mathbb{R}^{m \times m}$，其元素$dist_{ij}$为样本$x_i$到$x_j$的距离；  
> 低维空间维度$d'$  
> 过程：  
> 1. 根据式(10.7)~(10.9)计算$dist_{i \cdot}^2,dist_{\cdot j}^2,dist_{\cdot \cdot}^2$；  
> 2. 根据式(10.10)计算矩阵$B$（**低维内积矩阵**）；  
> 3. 对矩阵$B$做特征值分解（**特征值分解求解**）；  
> 4. 取$\mathbf{\Lambda}$为$d'$个最大特征值所构成的对角矩阵，$\tilde{\mathbf{V}}$为相应的特征向量矩阵。  
> 5. 输出：矩阵$\tilde{\mathbf{V}} \tilde{\mathbf{\Lambda}}^{1 / 2} \in \mathbb{R}^{m \times d'}$，每行是一个样本的低维坐标（**并没有得到投影向量**）。