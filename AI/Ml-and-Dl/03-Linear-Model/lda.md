# 3.4 线性判别分析

线性判别分析（Linear Discriminant Analysis，简称LDA）,其基本思想是：将训练样本投影到一条直线上，使得同类的样例尽可能近，不同类的样例尽可能远。如图所示：

![13.png](https://i.loli.net/2018/10/17/5bc723b863ebb.png)![14.png](https://i.loli.net/2018/10/17/5bc723b85bfa9.png)

想让同类样本点的投影点尽可能接近，不同类样本点投影之间尽可能远，即：让各类的协方差之和尽可能小，不用类之间中心的距离尽可能大。基于这样的考虑，LDA定义了两个散度矩阵。

+ 类内散度矩阵（within-class scatter matrix）

![15.png](https://i.loli.net/2018/10/17/5bc723b8156e1.png)

+ 类间散度矩阵(between-class scaltter matrix)

![16.png](https://i.loli.net/2018/10/17/5bc723b7e9db3.png)

因此得到了LDA的最大化目标：“广义瑞利商”（generalized Rayleigh quotient）。

![17.png](https://i.loli.net/2018/10/17/5bc723b7e8a61.png)

从而分类问题转化为最优化求解w的问题，当求解出w后，对新的样本进行分类时，只需将该样本点投影到这条直线上，根据与各个类别的中心值进行比较，从而判定出新样本与哪个类别距离最近。求解w的方法如下所示，使用的方法为λ乘子。

![18.png](https://i.loli.net/2018/10/17/5bc723b83d5e0.png)

若将w看做一个投影矩阵，类似PCA的思想，则LDA可将样本投影到N-1维空间（N为类簇数），投影的过程使用了类别信息（标记信息），因此LDA也常被视为一种经典的监督降维技术。    
