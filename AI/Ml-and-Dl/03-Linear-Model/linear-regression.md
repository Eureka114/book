# 3.1 基本形式

- 给定有d个属性描述的示例$x = (x_{1};x_{2};...;x_{d})$​。线性模型试图学得一个通过属性的**线性组合**来进行预测的函数，即：
$$
   f(x)=\sum_{i=1}^{d}{w_ix_i}+b
$$

一般使用向量形式，记为$f(x)=\mathbf{w}^T\mathbf{x}+b$（即$f(x)=\vec w\cdot\vec x+b$），其中$\vec w=(w_1,w_2,\ldots,w_d)$。

- 线性模型中 f(x) 可以是各种“**尺度**”上的函数，例如：

| f(x)为实数域上的实值函数  |  线性回归模型  |
| :-----------------------: | :------------: |
|      f(x)为离散的值       | 线性多分类模型 |
|        f(x)为对数         |  对数线性模式  |
| f(x)进行sigmoid非线性变换 |  对数几率回归  |

   

# 3.2 线性回归

## 概念

- 线性回归问题就是试图学到一个线性模型尽可能准确地预测新样本的输出值，在这类问题中，往往我们会先得到一系列的有标记数据；有但输入的情形，也有多输入的情形。

- 有时这些输入的属性值并不能直接被我们的学习模型所用，需要进行相应的处理，对于连续值的属性，一般都可以被学习器所用，有时会根据具体的情形作相应的预处理，例如：归一化等；对于离散值的属性，可作下面的处理：

   - 若属性值之间存在“序关系”，则可以将其转化为连续值，例如：身高属性分为“高”“中等”“矮”，可转化为数值：{1, 0.5, 0}。
   - 若属性值之间不存在“序关系”，则通常将其转化为向量的形式，例如：性别属性分为“男”“女”，可转化为二维向量：{（1，0），（0，1）}。

## 线性回归（输入属性只有一个时适用）

- 欧几里得距离：即“欧氏距离”，为
  
$$
   D(x,y)=\sqrt{\sum\limits_{i=1}^n(x_i^2-y_i^2)}
$$

- 最小二乘法(Least Square Method, LSM)：基于均方误差最小化来进行模型求解的方法称为最小二乘法。在线性回归中，最小二乘法就是试图找到一条直线，使所有样本到直线上的欧氏距离之和最小。按照这一原则，有
  
$$
\begin{aligned}
   (w*,b*) & = \arg\min\limits_{(w,b)}\sum_{i=1}^{m}(f(x_i)-y_i)^2 \\
            & = \arg\min\limits_{(w,b)}\sum_{i=1}^{m}(y_i-wx_i-b)^2
\end{aligned}
$$

- 参数估计(parameter estimation)：求解w, b使得$E_(e,b)=\sum\limits_{i=1}^m{(y_i-wx_i+b)^2}$的过程，，称为线性回归模型的最小二乘“参数估计”。
  
$$
\begin{aligned}
      \frac{\partial E_{(w,b)}}{\partial w}& =2\left(w\sum_{i=1}^{m}{x_i}^2-\sum\limits_{i=1}^{m}(y_i-b)x_i\right)\\
      \frac{\partial E_{(w,b)}}{\partial b}& =2\left(mb-\sum\limits_{i=1}^{m}(y_i-wx_i)\right)
\end{aligned}
$$

令上式为0，得到w，b的闭式(Closed-form)解：

$$
\begin{aligned}
      w=&\frac{\sum\limits_{i=1}^{m}y_i(x_i-\bar x)}{\sum\limits_{i=1}^{m}x_i^2-\frac{1}{m}(\sum\limits_{i=1}^{m}x_i)^2}\\
      b=&\frac{1}{m}\sum\limits_{i=1}^{m}(y_i-wx_i)
\end{aligned}
$$

## 多元线性回归（输入属性有多个时使用）
- 当输入属性有多个的时候，例如对于一个样本有d个属性{(x1,x2...xd), y}，则$y=wx+b$需要写成
  
   $$
   y_i=\sum_{k=1}^{d}{w_kx_{ik}}+b
   $$
   
- 通常对于多元问题，常常使用矩阵的形式来表示数据。

   在本问题中，将具有m个样本的数据集表示成矩阵X，将系数w与b合并成一个列向量，这样每个样本的预测值以及所有样本的均方误差最小化就可以写成下面的形式：

$$
   \mathbf{X}\cdot\hat \omega=\begin{pmatrix}  x_{11}&  x_{12}&  \cdots &  x_{1d}& 1\\  x_{21}&  x_{22}&  \cdots &  x_{2d}& 1\\  \vdots &  \vdots &\ddots &  \vdots & \vdots\\  x_{m1}&  x_{m2}&  \cdots &  x_{md}&1\end{pmatrix}\cdot\begin{pmatrix} \omega_1\\ \omega_2\\ \vdots\\ \omega_d\\b\end{pmatrix}=\begin{pmatrix} f(x_1)\\ f(x_2)\\ \vdots\\ f(x_m)\end{pmatrix}
$$

   其中，$f(x_i)=\sum\limits_{k=1}^{d}\omega_kx_{ik}+b$，$i\in[1,m]$。

$$
   \hat \omega^*=\arg\min_{\hat \omega}(\mathbf y-\mathbf {X\hat \omega})^T(\mathbf y-\mathbf {X\hat \omega})
$$
      
   同样地，我们使用最小二乘法对w和b进行估计，令均方误差的求导等于0，需要注意的是，当一个矩阵的行列式不等于0时，我们才可能对其求逆，因此对于下式，我们需要考虑矩阵（X的转置*X）的行列式是否为0，若不为0，则可以求出其解，若为0，则需要使用其它的方法进行计算，书中提到了引入正则化，此处不进行深入。
   ![6.png](https://i.loli.net/2018/10/17/5bc722b0cde33.png)
## 对数线性回归
1. 有时像上面这种原始的线性回归可能并不能满足需求，例如：y值并不是线性变化，而是在指数尺度上变化。这时我们可以采用线性模型来逼近y的衍生物，如$\ln y$，这时衍生的线性模型如下所示，实际上就是相当于将指数曲线投影在一条直线上，如下图所示：
   
      ![7.png](https://i.loli.net/2018/10/17/5bc722b103cbf.png)
   
2. 广义线性模型(Generalized Linear Model)：更一般地，考虑单调可微函数$g(\cdots)$，令
   
$$
   y=g^{-1}(\mathbf{w}^T\mathbf{x}+b),
$$

此时得到的模型称为“广义线性模型"，其中，函数$g(\cdot)$称为“联系函数”(Linked Function)。则对数线性回归是广义线性模型的特例。