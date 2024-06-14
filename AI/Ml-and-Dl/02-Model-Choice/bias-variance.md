# 2.5 偏差与方差

## 定义和公式
1. 偏差-方差分解是解释学习器泛化性能的重要工具。在学习算法中，偏差指的是预测的期望值与真实值的偏差，方差则是每一次预测值与预测值得期望之间的差均方。实际上，偏差体现了学习器预测的准确度，而方差体现了学习器预测的稳定性。

2. 对回归样本$x$，令$y_D$为$x$在数据集中的标记，$y$为$x$的真实标记，$f(x;D)$为训练集上学得模型$f$在$x$上的预测输出。以回归任务为例：

   > 学习算法的期望预测为
   >
   > 
   >$$
   >  bar f(x)=\mathbb{E}_D[f(x;D)]
   >$$
   >  
   >
   > 使用样本数相同的不同训练集产生的方差为
   >
   > 
   >$$
   >  var(x)=\mathbb{E}_D\left[\left(f(x;D)-\bar f(x)\right)^2\right]
   >$$
   >  
   >
   > 噪声为（假定噪声期望为0，即$\mathbb{E}_D[y_D-y]=0$​.）
   >
   > 
   >$$
   >  \varepsilon^2=\mathbb{E}_D[(y_D-y)^2]
   >$$
   >  
   >
   > 期望输出与真实标记的差别称为**偏差(bias)**即
   >
   > 
   >$$
   >  bias^2(x)=(\bar f(x)-y)^2
   >$$
   >  
   >
   > 则有
   >
   > 
   >$$
   >  E(f;D)=bias^2(x)+var(x)+\varepsilon^2
   >$$
   >  
   >
   > 也就是说，泛化误差可分解为偏差、方差与噪声之和。

3. 回顾偏差、方差、噪声的含义：

   1. 偏差度量了学习算法的期望预测与真实结果的偏离程度，即刻画了学习算法本身的拟合能力。
   2. 方差度量了同样大小的训练集的变动所导致的学习性能的变化，即刻画了数据扰动所造成的影响。
   3. 噪声则表达了在当前任务上任何学习算法所能达到的期望泛化误差的下界，即刻画了学习问题本身的难度。

## 偏差-方差窘境

   1. 偏差-方差分解说明，泛化性能是学习算法的能力、数据的充分性以及学习任务本身的难度所共同决定的。给定学习任务，为了取得好的**泛化性能**，则需使**偏差较小**，即能够充分拟合数据；并且使方差较小，即使得数据扰动产生的影响小。

   2. 偏差-方差窘境(Bias-variance Dilemma)：方差和偏差具有矛盾性，这就是常说的偏差-方差窘境，随着训练程度的提升，期望预测值与真实值之间的差异越来越小，即偏差越来越小，但是另一方面，随着训练程度加大，学习算法对数据集的波动越来越敏感，方差值越来越大。

      ![13.png](https://i.loli.net/2018/10/17/5bc722234b09f.png)
