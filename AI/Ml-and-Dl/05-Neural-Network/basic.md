# 5.1 神经网络初步

## 神经元模型

1. 神经网络中最基本的单元是神经元模型（neuron）。
2. 在生物神经网络的原始机制中，每个神经元通常都有多个树突（dendrite），一个轴突（axon）和一个细胞体（cell body），树突短而多分支，轴突长而只有一个；在功能上，树突用于传入其它神经元传递的神经冲动，而轴突用于将神经冲动传出到其它神经元，当树突或细胞体传入的神经冲动使得神经元兴奋时，该神经元就会通过轴突向其它神经元传递兴奋。神经元的生物学结构如下图所示。

![1.png](https://i.loli.net/2018/10/17/5bc72cbb6cc11.png)

3. 一直沿用至今的“M-P神经元模型”正是对这一结构进行了抽象，也称“阈值逻辑单元“，其中树突对应于输入部分，每个神经元收到n个其他神经元传递过来的输入信号，这些信号通过带权重的连接传递给细胞体，这些权重又称为连接权（connection weight）。细胞体分为两部分，前一部分计算总输入值（即输入信号的加权和，或者说累积电平），后一部分先计算总输入值与该神经元阈值的差值，然后通过激活函数（activation function）的处理，产生输出从轴突传送给其它神经元。

   其中，输出与激活函数的关系是
$
   y=f\left(\sum_{i=1}^n w_ix_i-\theta\right)
$
   M-P神经元模型如下图所示：

![2.png](https://i.loli.net/2018/10/17/5bc72cbb7be44.png)

## 激励函数

1. 激励函数就是使神经元产生兴奋的函数。传递给神经元的输入与权重的乘积的总和，与偏置相加得到的结果（或者减去阈值），会被激励函数转换成用于表示神经元兴奋状态的信号。

2. 如果不使用激励函数，神经元的运算就只是单独地对乘积相加，这样，神经网络也失去了对复杂问题进行处理的能力。

3. 代表性的激励函数如下：
   -  阶跃函数：形如
  
$$
   y=\left\{\begin{matrix} 
   0,x\le 0\\ 1,x>0
   \end{matrix}\right.
$$

   的函数为阶跃函数。其可以使用0或1来非常简单地表示神经元的兴奋状态，但是其缺点是无法对处于0到1之间的状态进行表示。
   
   - Sigmoid函数：Sigmoid函数是在0到1之间平滑变化的函数，如$y=\dfrac{1}{1+\exp(-x)}$。该函数变化较为平滑，且可以将0到1之间的情况表示出来。其还比较方便使用微分进行计算。$\dfrac{\mathrm dy}{\mathrm dx}=(1-y)y$，其中，$y=\dfrac{1}{1+\exp(-x)}$。
   
   - tanh函数：即双曲正切函数。$y=\tanh x=\dfrac{\exp(x)-\exp(-x)}{\exp(x)+\exp(-x)}$。它是一个平衡性很好的中心对称的激励函数。
   
   - ReLU函数和Leaky ReLU函数：线性整流函数(Rectified Linear Unit, ReLU)是形如

$$
   y=\left\{\begin{matrix} 
   0,x\le 0\\ x,x>0
   \end{matrix}\right.
$$

   的函数。ReLU 函数较为简单，即使网络层次数量增加也可以比较稳定地进行学习。ReLU 函数的导数为上文提及的阶跃函数。ReLU 函数主要被当作输出层以外的激励函数使用。当$x$为负值时，$\text{Relu}(x)=0$，否则，$\text{Relu}(x)=x$。这又是Relu函数的一大优点。

   而Leaky ReLU函数，是形如

$$
   y=\left\{\begin{matrix} 
   0.01x,x\le 0\\ x,x>0
   \end{matrix}\right.
$$

   的函数。如ReLU函数相比，Leaky ReLU可以有效避免神经元“死亡”的问题。这带来的好处是：在反向传播过程中，对于LeakyReLU激活函数输入小于零的部分，也可以计算得到梯度(而不是像ReLU一样值为0)，这样就避免了上述梯度方向锯齿问题。
   
   > **神经元“死亡”(Dying ReLU Problem)**
   >
   > ReLU 函数也有缺点。尽管稀疏性可以提升计算高效性，但同样可能阻碍训练过程。通常，激活函数的输入值有一项偏置项(bias)，假设bias变得太小，以至于输入激活函数的值总是负的，那么反向传播过程经过该处的梯度恒为0，对应的权重和偏置参数此次无法得到更新。如果对于所有的样本输入，该激活函数的输入都是负的，那么该神经元再也无法学习，称为神经元”死亡“问题。
   
   - 恒等函数：形如 $f(x)=x$ 的函数为恒等函数。这里的恒等，指的是函数的表达式在任何情况下都相同。其在处理回归问题时经常被用到。由于输出的范围没有限制且是连续的，所以当需要处理连续数值中存在的回归问题，使用它十分合适。
   
   - SoftMax函数：是用于多类分类问题的激活函数，在多类分类问题中，超过两个类标签则需要类成员关系。对于长度为$K$的任意实向量，Softmax函数可以将其压缩为长度为$K$，值在 $[ 0 , 1 ]$ 范围内，并且向量中元素的总和为1的实向量。其函数形如
   
$$
   y=\frac{\exp(x)}{\sum\limits_{k=1}^n\exp(x_k)}
$$

   Softmax函数与正常的max函数不同：max函数仅输出最大值，但Softmax函数确保较小的值具有较小的概率，并且不会直接丢弃。我们可以认为它是 $\arg\max$ 函数的概率版本或“soft”版本。Softmax函数的分母结合了原始输出值的所有因子，这意味着Softmax函数获得的各种概率彼此相关。

   Softmax激活函数的特点：

   1. 在零点不可微。
   2. 负输入的梯度为零，这意味着对于该区域的激活，权重不会在反向传播期间更新，因此会产生永不激活的死亡神经元。
