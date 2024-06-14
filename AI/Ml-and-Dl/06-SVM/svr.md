# 6.5 支持向量回归
现在我们来看看支持向量机的回归版本，支持向量回归（Support Vector Regression，SVR）。SVR是SVM的一个应用，用于回归问题。SVR的目标是找到一个函数f(x)使得预测值与真实值之间的误差最小。

对于回归问题，给定训练数据 $D=\{ (x_1,y_1),(x_2,y_2),...,(x_m,y_m)\}$ ，希望学得一个回归模型 $f(x)=w^{T}x+b$ 使得 $f(x)$ 与$y$尽可能接近，w和b是模型参数。

对样本 $(x,y)$ 传统回归模型通常直接基于模型输出 $f(x)$ 与真实输出y之间的差别来计算损失，当且仅当$f(x)$ 与y完全一样时，损失才为0。与此不同，支持向量回归（SVR）假设我们能容忍$f(x)$与$y$之间最多有$\epsilon$的误差，仅当f(x)与y之间的差的绝对值大于$\epsilon$时才计算损失。

于是，SVR问题为：（此处已经引入松弛变量$\xi_i,\xi_i^*$）

$$
    \begin{aligned}
    \min_{w,b,\xi,\xi^{*}} \quad & \frac{1}{2}||w||^2+C\sum_{i=1}^{m}(\xi_i+\xi_i^{*}) \\
    s.t. \quad & y_i-w^{T}x_i-b\leq \epsilon+\xi_i \\
    & w^{T}x_i+b-y_i\leq \epsilon+\xi_i^{*} \\
    & \xi_i,\xi_i^{*}\geq 0
    \end{aligned}
$$

引入拉格朗日乘子$\alpha_i,\alpha_i^{*},\mu_i,\mu_i^{*}$，得到拉格朗日函数：（这四个值均大于等于0）

$$
    \begin{aligned}
    L(w,b,\xi,\xi^{*},\alpha,\alpha^{*},\mu,\mu^{*}) & =\frac{1}{2}||w||^2+C\sum_{i=1}^{m}(\xi_i+\xi_i^{*})+\sum_{i=1}^{m}\alpha_i(f(x_i)-y_i-\epsilon-\xi_i^{*})\\&+\sum_{i=1}^{m}\alpha_i^{*}(y_i-f(x_i)-\epsilon-\xi_i^{*})
    -\sum_{i=1}^{m}\mu_i\xi_i-\sum_{i=1}^{m}\mu_i^{*}\xi_i^{*}
    \end{aligned}
$$

后求偏导数，得到：

$$
    \begin{aligned}
    \frac{\partial L}{\partial w} & =w-\sum_{i=1}^{m}(\alpha_i-\alpha_i^{*})x_i=0 \\
    \frac{\partial L}{\partial b} & =\sum_{i=1}^{m}(\alpha_i-\alpha_i^{*})=0 \\
    \frac{\partial L}{\partial \xi_i} & =C-\alpha_i-\mu_i=0 \\
    \frac{\partial L}{\partial \xi_i^{*}} & =C-\alpha_i^{*}-\mu_i^{*}=0
    \end{aligned}
$$

代入并整理，得到SVR的对偶问题：

$$
    \begin{aligned}
    \max_{\alpha,\alpha^{*}} \quad & \sum_{i=1}^{m}y_i(\alpha_i^*-\alpha_i)-\sum_{i=1}^{m}\epsilon(\alpha_i+\alpha_i^{*})-\frac{1}{2}\sum_{i=1}^{m}\sum_{j=1}^{m}(\alpha_i^{*}-\alpha_i)(\alpha_j^{*}-\alpha_j)x_i^{T}x_j \\
    s.t. \quad & \sum_{i=1}^{m}(\alpha_i-\alpha_i^{*})=0,0\leq \alpha_i,\alpha_i^{*}\leq C
    \end{aligned}
$$

KKT条件为：

$$
    \begin{cases}
    \alpha_i(\epsilon+\xi_i-y_i+w^{T}x_i+b) =0 \\
    \alpha_i^{*}(\epsilon+\xi_i^{*}+y_i-w^{T}x_i-b) =0 \\
    \mu_i\xi_i =0 \\
    \mu_i^{*}\xi_i^{*} =0 \\
    \alpha_i,\alpha_i^{*} \in [0,C] \\
    \mu_i,\mu_i^{*} \geq 0
    \end{cases}
$$

SVR的解如下：

$$
    f(x)=\sum_{i=1}^{m}(\alpha_i^{*}-\alpha_i)x_i^{T}x+b
$$

其中$\alpha_i,\alpha_i^{*}$是拉格朗日乘子，满足KKT条件。

SVR的优化问题与SVM的优化问题类似，但是SVR的目标是最小化预测值与真实值之间的误差，而SVM的目标是最大化分类间隔。

实践中，我们采用更加鲁棒的方法：选取多个或所有满足条件$\alpha_i$的样本求解b后取平均值。

同样也可以引入核技巧，把$x$用$\phi(x)$来代替。得到的最终的模型是：

$$
    f(x)=\sum_{i=1}^{m}(\alpha_i^{*}-\alpha_i)K(x_i,x)+b
$$

其中$K(x_i,x)$是核函数。