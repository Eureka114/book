# 6.4 软间隔与正则化

1. 前面的讨论中，我们主要解决了两个问题：当数据线性可分时，直接使用最大间隔的超平面划分；当数据线性不可分时，则通过核函数将数据映射到高维特征空间，使之线性可分。
2. 然而在现实问题中，对于某些情形还是很难处理，例如数据中有**噪声**的情形，噪声数据（**outlier**）本身就偏离了正常位置，但是在前面的SVM模型中，我们要求所有的样本数据都必须满足约束，如果不要这些噪声数据还好，当加入这些outlier后导致划分超平面被挤歪了。
![](https://i.loli.net/2018/10/17/5bc730ccce68e.png)
## 软间隔

1. 缓解该问题的一个办法是允许支持向量机在一些样本上出错。为此，要引入 “软间隔”(soft margin)的概念：
   > 1. 允许某些数据点不满足约束y(w'x+b)≥1；
   > 2. 同时又使得不满足约束的样本尽可能少。

   于是，优化目标变为：

$$
   \min_{\mathbf{w},b} \frac12\lVert\mathbf{w}\rVert^2+C\sum_{i=1}^m\text{loss}_{0/1}(y_i(\mathbf{w}^T\mathbf{x_i}+b)-1)
$$

   其中，$\text{loss}_{0/1}(z)$是0/1损失函数，当z小于0时，$\text{loss}_{0/1}(z)=1$，否则为0。

2. **替代损失**：然而，上述的损失函数的数学性质不佳。（非凸、非连续），人们使用其他的一些函数来代替上述函数，称为替代损失。替代损失函数一般都有较好的数学性质。以下是常用的替代损失函数：

> - hinge 损失：
>
> $$
>    l_\text{hinge}(z)=\max{(0,1-z)}
> $$
>
> - 指数损失：
>
> $$
>    l_\text{exp}(z)=e^{-z}
> $$
>
> - 对率损失：
>
> $$
>    l_\text{log}(z)=\log(1+\exp(-z))
> $$
>

3. 在支持向量机中，我们所使用的是hinge损失函数。引入**松弛变量**，则目标函数和约束条件可以改写为
   
$$
   \min_{\mathbf{w},b,\xi}\frac12\lVert w\rVert^2+C\sum_{i=1}^m\xi_i
$$


$$
   \text{s.t.}{y_i(\mathbf{w}^T\mathbf{x}_i)+b}\ge1-\xi_i,\xi_i\ge0
$$

   这就是常用的**软间隔支持向量机**。

1. 这仍然是一个二次规划的问题。类似上述内容，我们通过拉格朗日乘数法得到对应的拉格朗日函数：
   
$$
   L(\mathbf{w},b,\xi,\alpha,\mu)=\frac12\lVert\mathbf{w}\rVert^2+C\sum_{i=1}^m\xi_i-\sum_{i=1}^m\alpha_i\left[1-\xi_i-y_i(\mathbf{w}^T\mathbf{x}_i+b)\right]-\sum_{i=1}^m\mu_i\xi_i
$$

   其中，$\alpha_i\ge0,\mu_i\ge0$是拉格朗日乘数。
   令$\dfrac{\partial L}{\partial\mathbf{w}}=0,\dfrac{\partial L}{\partial b}=0,\dfrac{\partial L}{\partial\xi_i}=0$，得到

$$
   \mathbf{w}=\sum_{i=1}^m\alpha_iy_i\mathbf{x}_i
$$


$$
   \sum_{i=1}^m\alpha_iy_i=0
$$


$$
   C=\alpha_i+\mu_i
$$

   代入拉格朗日函数，得到对偶问题：

$$
   \max_{\alpha}\sum_{i=1}^m\alpha_i-\frac12\sum_{i=1}^m\sum_{j=1}^m\alpha_i\alpha_jy_iy_j\mathbf{x}_i^T\mathbf{x}_j
$$


$$
   \text{s.t.}\sum_{i=1}^m\alpha_iy_i=0,0\le\alpha_i\le C
$$

   这是一个凸二次规划问题，可以通过现有的优化算法求解。
1. 对于软间隔支持向量机，KKT条件为：
   
$$
   \begin{cases}
    \alpha_i\ge0, \mu_i\ge0\\
   \alpha_i(y_i(\mathbf{w}^T\mathbf{x}_i+b)-1+\xi_i)=0\\
   \xi_i>0,\mu_i\xi_i=0\\
   y_i(\mathbf{w}^T\mathbf{x}_i+b)-1+\xi_i\ge0
   \end{cases}
$$

   于是，对任意训练样本，总有$\alpha_i=0$或$y_if(x_i)=1-\xi_i$，即只有支持向量对应的拉格朗日乘数不为0。由此可以看出，软间隔支持向量机的决策函数仍然是由支持向量决定的。

## 正则化
问题：是否可以使用其他的损失函数来替代呢？
1. 以使用对率损失为例，我们可以得到对应的优化目标：
   
$$
   \min_{\mathbf{w},b}\frac12\lVert\mathbf{w}\rVert^2+C\sum_{i=1}^m\log(1+\exp(-y_i(\mathbf{w}^T\mathbf{x}_i+b)))
$$

   这是一个非凸优化问题，一般使用梯度下降等方法求解。
   对率回归的优势主要在于其输出具有自然的概率意义，而支持向量机的输出是一个符号。此外，对率回归能直接用于多分类问题，而支持向量机需要进行一些变换。但是，对率损失是光滑的单调递减函数，不能导出类似支持向量的概念，因此对率回归的解依赖于更多的训练样本，其预测开销也更大。
1. 正则化：我们可以用更一般的形式来表示支持向量机的优化目标：
   
$$
   \min_{f} \Omega (f)+C\sum_{i=1}^m\text{loss}(f(\mathbf{x}_i),y_i)
$$
   其中，$\Omega(f)$是结构风险，第二项是“经验风险”，描述模型和训练数据之间的契合程度。而C是一个调节参数，用来平衡两者之间的关系。这种形式的优化目标称为**正则化**。
1. $L_p$范数正则化：它是常用的正则化项，其中，$L_1$范数正则化可以使得模型更稀疏，$L_2$范数正则化可以使得模型更平滑。$L_1$范数正则化可以用于特征选择，$L_2$范数正则化可以用于防止过拟合。