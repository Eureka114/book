# 6.3 核函数

## 线性不可分问题

- 由于上述的超平面只能解决线性可分的问题，对于线性不可分的问题，例如：异或问题，我们需要使用**核函数**将其进行推广。

- 一般地，解决线性不可分问题时，常常采用**映射**的方式，将低维原始空间映射到高维特征空间，使得数据集在高维空间中变得线性可分，从而再使用线性学习器分类。如果原始空间为有限维，即属性数有限，那么总是存在一个高维特征空间使得样本线性可分。

- 若$\phi(x)$​代表一个映射，则在特征空间中的划分函数变为：

$$
   f\left( \mathbf{x} \right) =\mathbf{w}^T\phi(\mathbf{x})+b
$$

   按照同样的方法，先写出新目标函数的拉格朗日函数，接着写出其对偶问题，求L关于w和b的极大，最后运用SOM求解α。
- 原始问题的对偶问题为：

$$
   \begin{aligned}
   \max_{\mathbf{\alpha}}\sum_{i=1}^N{\alpha_i}-\frac{1}{2}\sum_{i=1}^N{\sum_{j=1}^N{\alpha_i\alpha_jy_iy_j\phi(\mathbf{x}_i)^T\phi(\mathbf{x}_j)}},\\
   s.t.\quad \sum_{i=1}^N{\alpha_iy_i}=0,\quad \alpha_i\ge 0
   \end{aligned}
$$

- 原分类函数变为：
   
$$
   f\left( \mathbf{x} \right) =\sum_{i=1}^N{\alpha_iy_i\phi(\mathbf{x}_i)^T\phi(\mathbf{x})}+b
$$

## 核函数
- 求解上述问题的关键在于计算$\phi(\mathbf{x}_i)^T\phi(\mathbf{x}_j)$​，这个计算量是非常大的，因为它是在高维空间中进行的。为了避免这个问题，我们引入了**核函数**的概念。我们设想这样一个函数：
   
$$
   \kappa(\mathbf{x}_i,\mathbf{x}_j)=\left\langle(\mathbf{x}_i),\phi(\mathbf{x}_j)\right\rangle=\phi(\mathbf{x}_i)^T\phi(\mathbf{x}_j)
$$

   其中$\kappa(\mathbf{x}_i,\mathbf{x}_j)$​是一个核函数，它的作用是直接计算两个样本在高维空间中的内积，而不需要显式地写出映射函数$\phi(\mathbf{x})$​。这样，我们就可以直接在原始空间中计算内积，而不需要显式地写出映射函数$\phi(\mathbf{x})$​。

- 核函数定理：一个对称函数$\kappa(\mathbf{x}_i,\mathbf{x}_j)$​是一个**合法**的核函数的充要条件是，对于任意的$\mathbf{x}_1,\mathbf{x}_2,\cdots,\mathbf{x}_m$​，其对应的核矩阵

$$
   K=\left[\begin{matrix}
   \kappa(\mathbf{x}_1,\mathbf{x}_1) & \kappa(\mathbf{x}_1,\mathbf{x}_2) & \cdots & \kappa(\mathbf{x}_1,\mathbf{x}_m)\\
   \kappa(\mathbf{x}_2,\mathbf{x}_1) & \kappa(\mathbf{x}_2,\mathbf{x}_2) & \cdots & \kappa(\mathbf{x}_2,\mathbf{x}_m)\\
   \vdots & \vdots & \ddots & \vdots\\
   \kappa(\mathbf{x}_m,\mathbf{x}_1) & \kappa(\mathbf{x}_m,\mathbf{x}_2) & \cdots & \kappa(\mathbf{x}_m,\mathbf{x}_m)
   \end{matrix}\right]
$$

   是**半正定**的。
   该定理表明，只要一个对称函数所对应的核矩阵是半正定的，那么这个函数就是一个合法的核函数。事实上，对于一个半正定核矩阵，总能够找到一个与之对应的映射$\phi$。换言之，任何一个函数都隐式定义了一个“再生核希尔伯特空间”(RKHS)的特征空间。
- 常用的核函数有：
   1. 线性核函数：$\kappa(\mathbf{x}_i,\mathbf{x}_j)=\mathbf{x}_i^T\mathbf{x}_j$​
   
   2. 多项式核函数：$\kappa(\mathbf{x}_i,\mathbf{x}_j)=(\mathbf{x}_i^T\mathbf{x}_j)^d$，$d\ge 1$为多项式的次数​
   
   3. 高斯核函数：$\kappa(\mathbf{x}_i,\mathbf{x}_j)=\exp\left(-\frac{\left\|\mathbf{x}_i-\mathbf{x}_j\right\|^2}{2\sigma^2}\right)$ $\sigma>0$为高斯核的带宽。​
   
   4. Sigmoid核函数：$\kappa(\mathbf{x}_i,\mathbf{x}_j)=\tanh(\alpha\mathbf{x}_i^T\mathbf{x}_j+c)$ $\beta>0,\theta<0$
   
   5. 拉普拉斯核：$\kappa(\mathbf{x}_i,\mathbf{x}_j)=\exp\left(-\frac{\left\|\mathbf{x}_i-\mathbf{x}_j\right\|}{\sigma}\right)$ $\sigma>0$
   
   6. 此外，还可以通过函数组合得到。
      1. 线性组合：$\kappa(\mathbf{x}_i,\mathbf{x}_j)=\sum_{i=1}^N{\alpha_i\kappa_i(\mathbf{x}_i,\mathbf{x}_j)}$
      2. 核函数的直积：$\kappa_1\otimes\kappa_2(\mathbf{x}_i,\mathbf{x}_j)=\kappa_1(\mathbf{x}_i,\mathbf{x}_j)\kappa_2(\mathbf{x}_i,\mathbf{x}_j)$
      3. 若$\kappa(\mathbf{x},\mathbf{z})$为核函数，则$\kappa(\mathbf{x},\mathbf{z})=g(x)\kappa_1(\mathbf{x},\mathbf{z})g(\mathbf{z})$也是核函数。