# 6.2 对偶问题

目标：通过求解上文提及的式子来得到搭建个划分超平面所对应的模型

$$
   f(\textbf{w})=\textbf{w}^T\textbf{x}+b,
$$

其中，$\textbf{w},b$是参数。该式子本身是一个凸二次规划问题，可以直接使用现成的工具求解。但是，我们可以通过**对偶问题**来得到更好的理解。

> 为什么要将原问题转化为对偶问题？
>
> 1. 因为使用对偶问题更容易求解；
> 2. 因为通过对偶问题求解出现了向量内积的形式，从而能更加自然地引出核函数。

## 对偶问题和拉格朗日乘子法
1. 对偶问题，顾名思义，可以理解成优化等价的问题，更一般地，是将一个原始目标函数的最小化转化为它的对偶函数最大化的问题。
2. 首先，我们将有约束的原始目标函数转换为无约束的新构造的拉格朗日目标函数：
   
$$
   L\left( \mathbf{w},b,\mathbf{\alpha } \right) =\frac{1}{2}\lVert \mathbf{w} \rVert ^2-\sum_{i=1}^N{\alpha _i\left( y_i\left( \mathbf{w}\cdot \mathbf{x}_{\mathbf{i}}+b \right) -1 \right)} 
$$
   
   其中，$\alpha_i\ge 0$是拉格朗日乘子。不难验证，若有一个约束条件不满足，则$\max L=\infty$；当当所有约束条件都满足时，L的最大值为$\frac12\lVert w\rVert^2$。因此，原始问题可以转化为求解
   
$$
   \min_{\mathbf{w},b}\max_{\mathbf{\alpha}}L\left( \mathbf{w},b,\mathbf{\alpha } \right)
$$

   的问题。
3. 由于上述问题是先求最大值再求最小值的问题，而现在我们首先就要面对带有需要求解的参数$\mathbf{w}$和$b$的方程，而$\alpha_i$​又是不等式约束，这个求解过程不好做。所以，我们需要使用**拉格朗日函数对偶性**，将最小和最大的位置交换一下：
   
$$
   \max_{\mathbf{\alpha}}\min_{\mathbf{w},b}L\left( \mathbf{w},b,\mathbf{\alpha } \right)
$$

   这样就将原问题的求最小变成了对偶问题求最大（用对偶这个词还是很形象），接下来便可以先求L对w和b的极小，再求L对α的极大。
4. 首先求$L$对$w,b$的极小：分别求$\dfrac{\partial L}{\partial w},\dfrac{\partial L}{\partial b}$，有
   
$$
   \begin{aligned}
   \dfrac{\partial L}{\partial w} &= w-\sum_{i=1}^N{\alpha_iy_ix_i}=0\\
   \dfrac{\partial L}{\partial b} &= -\sum_{i=1}^N{\alpha_iy_i}=0
   \end{aligned}
$$

   代入$L$中，得到
   
$$
   \begin{aligned}
   L\left( \mathbf{w},b,\mathbf{\alpha } \right) &=\frac{1}{2}\lVert \mathbf{w} \rVert ^2-\sum_{i=1}^N{\alpha _i\left( y_i\left( \mathbf{w}\cdot \mathbf{x}_{\mathbf{i}}+b \right) -1 \right)} \\
   &=\frac{1}{2}\lVert \sum_{i=1}^N{\alpha_iy_ix_i} \rVert ^2-\sum_{i=1}^N{\alpha _i\left( y_i\left( \sum_{j=1}^N{\alpha_jy_jx_j}\cdot \mathbf{x}_{\mathbf{i}}+b \right) -1 \right)} \\
   &=\frac{1}{2}\sum_{i=1}^N{\sum_{j=1}^N{\alpha_i\alpha_jy_iy_jx_i\cdot x_j}}-\sum_{i=1}^N{\alpha _i\left( y_i\sum_{j=1}^N{\alpha_jy_jx_j\cdot \mathbf{x}_{\mathbf{i}}}+b \right)}+\sum_{i=1}^N{\alpha_i} \\
   &=\frac{1}{2}\sum_{i=1}^N{\sum_{j=1}^N{\alpha_i\alpha_jy_iy_jx_i\cdot x_j}}-\sum_{i=1}^N{\alpha _i\left( y_i\sum_{j=1}^N{\alpha_jy_jx_j\cdot \mathbf{x}_{\mathbf{i}}} \right)}+\sum_{i=1}^N{\alpha_i} \\
   &=\frac{1}{2}\sum_{i=1}^N{\sum_{j=1}^N{\alpha_i\alpha_jy_iy_jx_i\cdot x_j}}-\sum_{i=1}^N{\sum_{j=1}^N{\alpha _i\alpha_jy_iy_jx_i\cdot x_j}}+\sum_{i=1}^N{\alpha_i} \\
   &=\sum_{i=1}^N{\alpha_i}-\frac{1}{2}\sum_{i=1}^N{\sum_{j=1}^N{\alpha_i\alpha_jy_iy_jx_i\cdot x_j}}
   \end{aligned}
$$
   
   > 上述求解过程要满足KKT条件（KKT条件是在满足一些有规则的条件下，一个非线性规划问题能有最优化解法的一个必要和充分条件）。
5. 然后求$L$对$\alpha$的极大：通过SMO算法，可以得到$\alpha$的解，从而得到$w,b$​的解，进而得到模型。
   
$$
   \begin{aligned}
    \max_{\mathbf{\alpha}}\sum_{i=1}^N{\alpha_i}-\frac{1}{2}\sum_{i=1}^N{\sum_{j=1}^N{\alpha_i\alpha_jy_iy_jx_i\cdot x_j}},\\ s.t.\quad \sum_{i=1}^N{\alpha_iy_i}=0,\quad \alpha_i\ge 0
   \end{aligned}
$$

6. 通过求解上述对偶问题，我们可以得到$\alpha$的解，从而得到$w,b$​的解，进而得到模型。
   
$$
   \begin{aligned}
      w &= \sum_{i=1}^N{\alpha_iy_ix_i},\\
      b &= -\frac{1}{2}\left( \max_{i:y_i=-1}{\mathbf{w}\cdot \mathbf{x}_i}+\min_{i:y_i=1}{\mathbf{w}\cdot \mathbf{x}_i} \right)
   \end{aligned}
$$

   这里实际上只需计算新样本与支持向量的内积，因为对于非支持向量的数据点，其对应的拉格朗日乘子一定为0，根据最优化理论（K-T条件），对于不等式约束$y(w'x+b)-1\ge0$，满足：
   
$$
   \partial_i\left(y_i(w^T+b)-1\right)=0
$$
   
   > 这里，至少有一个的拉格朗日乘子大于0。用反证法可以证明。

## SMO算法

SMO（Sequential Minimal Optimization）算法是一种求解支持向量机（SVM）优化问题的有效方法。其基本思想是：如果所有的变量的解都满足此最优化问题的KKT条件，那么这个最优化问题的解就得到了。因为KKT条件是该最优化问题的充分必要条件。但是SMO算法并不是一次处理所有变量，而是每次只选择两个变量，固定其他变量，然后针对这两个变量构建一个二次规划问题。这个二次规划问题相对于原始问题要简单很多，可以直接求解，不需要借助于数值优化方法。求解出最优解后，再用这个最优解更新那两个变量，这就完成了一次迭代。SMO算法不断地进行这样的迭代，直到所有变量满足KKT条件为止，这时就找到了原始问题的最优解。

SMO算法的主要步骤如下：

1. 选择一对需要优化的变量，这里有一些启发式的方法可以选择违反KKT条件最严重的变量。
2. 固定其他变量，只考虑这两个变量，将问题简化为二次规划问题求解。
3. 更新这两个变量。
4. 检查是否所有变量都满足KKT条件，如果满足，则结束；否则，返回步骤1。

这种方法的优点是每次只需要处理两个变量的优化问题，大大简化了问题的复杂性。