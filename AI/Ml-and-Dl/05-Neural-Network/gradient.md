

# 5.4 梯度下降

## 从自然现象中理解梯度下降

在大多数文章中，都以“一个人被困在山上，需要迅速下到谷底”来举例，这个人会“寻找当前所处位置最陡峭的地方向下走”。这个例子中忽略了安全因素，这个人不可能沿着最陡峭的方向走，要考虑坡度。

在自然界中，梯度下降的最好例子，就是泉水下山的过程：

1. 水受重力影响，会在当前位置，沿着最陡峭的方向流动，有时会形成瀑布（梯度下降）；
2. 水流下山的路径不是唯一的，在同一个地点，有可能有多个位置具有同样的陡峭程度，而造成了分流（可以得到多个解）；
3. 遇到坑洼地区，有可能形成湖泊，而终止下山过程（不能得到全局最优解，而是局部最优解）。

## 梯度下降的数学理解

梯度下降的数学公式：

$$
   \theta_{n+1} = \theta_{n} - \eta \cdot \nabla J(\theta)
$$

其中：

- $\theta_{n+1}$：下一个值；
- $\theta_n$：当前值；
- $-$：减号，梯度的反向；
- $\eta$：学习率或步长，控制每一步走的距离，不要太快以免错过了最佳景点，不要太慢以免时间太长；
- $\nabla$：梯度，函数当前位置的最快上升点；
- $J(\theta)$：函数。

### 梯度下降的三要素

1. 当前点；
2. 方向；
3. 步长。

### 为什么说是“梯度下降”？

“梯度下降”包含了两层含义：

1. 梯度：函数当前位置的最快上升点；
2. 下降：与导数相反的方向，用数学语言描述就是那个减号。

亦即与上升相反的方向运动，就是下降。

<img src="https://microsoft.github.io/ai-edu/%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/A2-%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86/%E7%AC%AC1%E6%AD%A5%20-%20%E5%9F%BA%E6%9C%AC%E7%9F%A5%E8%AF%86/img/2/gd_concept.png" ch="500" />

图2-9 梯度下降的步骤

图2-9解释了在函数极值点的两侧做梯度下降的计算过程，梯度下降的目的就是使得x值向极值点逼近。

## 单变量函数的梯度下降

假设一个单变量函数：$J(x)=x^2$，我们的目的是找到该函数的最小值，于是计算其微分：$J'(x) = 2x$。

假设初始位置为：$x_0=1.2$，假设学习率：$\eta=0.3$，则迭代公式为

$$
   x_{n+1} = x_{n} - \eta \cdot \nabla J(x)= x_{n} - \eta \cdot 2x
$$

假设终止条件为 $J(x)<0.01$，迭代过程是：
```
x=0.480000, y=0.230400
x=0.192000, y=0.036864
x=0.076800, y=0.005898
x=0.030720, y=0.000944
```

上面的过程如图2-10所示。

<img src="https://microsoft.github.io/ai-edu/%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/A2-%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86/%E7%AC%AC1%E6%AD%A5%20-%20%E5%9F%BA%E6%9C%AC%E7%9F%A5%E8%AF%86/img/2/gd_single_variable.png" ch="500" />

图2-10 使用梯度下降法迭代的过程

## 双变量的梯度下降

假设一个双变量函数：$J(x,y) = x^2 + \sin^2(y)$

我们的目的是找到该函数的最小值，于是计算其微分：

$$
   {\partial{J(x,y)} \over \partial{x}} = 2x
$$


$$
   {\partial{J(x,y)} \over \partial{y}} = 2 \sin y \cos y
$$

假设初始位置为：$(x_0,y_0)=(3,1)$

假设学习率：$\eta = 0.1$

根据公式(1)，迭代过程是的计算公式：

$$
   (x_{n+1},y_{n+1}) = (x_n,y_n) - \eta \cdot \nabla J(x,y)
$$

$$
   = (x_n,y_n) - \eta \cdot (2x,2 \cdot \sin y \cdot \cos y)
$$

根据公式(1)，假设终止条件为 $J(x,y)<0.01$，迭代过程如表2-3所示。

表2-3 双变量梯度下降的迭代过程

|迭代次数|x|y|J(x,y)|
|:-:|:-:|:-:|:-:|
|1|3|1|9.708073|
|2|2.4|0.909070|6.382415|
|...|...|...|...|
|15|0.105553|0.063481|0.015166|
|16|0.084442|0.050819|0.009711|

迭代16次后，$J(x,y)$ 的值为 $0.009711$，满足小于 $0.01$ 的条件，停止迭代。

上面的过程如表2-4所示，由于是双变量，所以需要用三维图来解释。请注意看两张图中间那条隐隐的黑色线，表示梯度下降的过程，从红色的高地一直沿着坡度向下走，直到蓝色的洼地。

表2-4 在三维空间内的梯度下降过程

|观察角度1|观察角度2|
|--|--|
|<img src="https://microsoft.github.io/ai-edu/%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/A2-%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86/%E7%AC%AC1%E6%AD%A5%20-%20%E5%9F%BA%E6%9C%AC%E7%9F%A5%E8%AF%86/img/2/gd_double_variable.png">|<img src="https://microsoft.github.io/ai-edu/%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/A2-%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86/%E7%AC%AC1%E6%AD%A5%20-%20%E5%9F%BA%E6%9C%AC%E7%9F%A5%E8%AF%86/img/2/gd_double_variable2.png">|

## 学习率η的选择

在公式表达时，学习率被表示为$\eta$。在代码里，我们把学习率定义为`learning_rate`，或者`eta`。针对上面的例子，试验不同的学习率对迭代情况的影响，如表2-5所示。

表2-5 不同学习率对迭代情况的影响

|学习率|迭代路线图|说明|
|:-:|:-:|:-:|
|1.0|<img src="https://microsoft.github.io/ai-edu/%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/A2-%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86/%E7%AC%AC1%E6%AD%A5%20-%20%E5%9F%BA%E6%9C%AC%E7%9F%A5%E8%AF%86/img/2/gd100.png" width="500" height="150"/>|学习率太大，迭代的情况很糟糕，在一条水平线上跳来跳去，永远也不能下降。|
|0.8|<img src="https://microsoft.github.io/ai-edu/%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/A2-%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86/%E7%AC%AC1%E6%AD%A5%20-%20%E5%9F%BA%E6%9C%AC%E7%9F%A5%E8%AF%86/img/2/gd080.png" width="400"/>|学习率大，会有这种左右跳跃的情况发生，这不利于神经网络的训练。|
|0.4|<img src="https://microsoft.github.io/ai-edu/%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/A2-%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86/%E7%AC%AC1%E6%AD%A5%20-%20%E5%9F%BA%E6%9C%AC%E7%9F%A5%E8%AF%86/img/2/gd040.png" width="400"/>|学习率合适，损失值会从单侧下降，4步以后基本接近了理想值。|
|0.1|<img src="https://microsoft.github.io/ai-edu/%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/A2-%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86/%E7%AC%AC1%E6%AD%A5%20-%20%E5%9F%BA%E6%9C%AC%E7%9F%A5%E8%AF%86/img/2/gd010.png" width="400"/>|学习率较小，损失值会从单侧下降，但下降速度非常慢，10步了还没有到达理想状态。|

## 梯度计算方法概要

- 假设$\mathbf{w}$为权重，$\mathbf{b}$为偏置，$\mathbf{E}$为误差，则在梯度下降法中，对$w,b$的更新公式为：

$$
   \begin{aligned}
   \mathbf{w} & = \mathbf{w} - \eta \frac{\partial \mathbf{E}}{\partial \mathbf{w}} \\
   \mathbf{b} & = \mathbf{b} - \eta \frac{\partial \mathbf{E}}{\partial \mathbf{b}}
   \end{aligned}
$$

   其中$\eta$为学习率。

- 现在假设由一个三层的神经网络，要对其中各个神经层的梯度进行求解。

   1. 对各个层的认识。三层分别是输入层、隐藏层和输出层，其中隐藏层和输出层都包含偏置和权重。由于输入层只是单纯地将接收到的数据传入给下面的网络层，因此其没有权重和偏置。

   2. 在输出层中，需要根据误差对权重和偏置的梯度进行计算。另外，传递给输出层的输入数据的梯度也同样要根据误差计算。在进行正向传播的过程中，是对网络层的输出进行传播；而在反向传播的过程中，则是对输入的梯度进行传播。

   3. 在中间层中接收这个输入的梯度，根据输入的梯度对权重和偏置的梯度，以及传递给中间层的输入的梯度进行计算。输入的梯度在网络中是从下层往上层进行回溯传递的。关于对输入的梯度进行传播的理由，我们将在稍后进行讲解。

   4. 此外，即使网络层的数量增加到四层以上，通过对输入的梯度进行传播，除了输出层以外，在所有的网络层都可以使用相同的方法对梯度进行计算。因此，只要知道如何在三层的神经网络中对梯度进行求解，那么无论网络的层数如何增加，一样也能够求解。

   5. 接下来使用数学公式对梯度进行求解。根据网络层中所使用的激励函数、损失雨数的种类的不同，对梯度进行计算的方法也多少有些差异，因此我们先对其中通用的部分进行讲解。

      | 网络层 | 下标 | 神经元数量 |
      | :----: | :--: | :--------: |
      | 输入层 |  i   |     l      |
      | 中间层 |  j   |     m      |
      | 输出层 |  k   |     n      |

## 输出层的梯度

- 用$w_{jk}$代表输出层中的权重，$b_k$为偏置，$u_k=\sum w_{jk}x_k+b_k$，则有：

- 权重的梯度

$$
   \partial{w_{jk}}=\dfrac{\partial E}{\partial{w_{jk}}}=\dfrac{\partial E}{\partial u_k}\cdot\dfrac{\partial u_k}{\partial{w_{jk}}}
$$

其中又有$\dfrac{\partial u_k}{\partial{w_{jk}}}=y_j$（展开u项）。
对于$\dfrac{\partial E}{\partial u_k}$，使用链式法则展开，得到（使用输出层的神经元）

$$
   \dfrac{\partial E}{\partial u_k}=\dfrac{\partial E}{\partial y_k}\cdot\dfrac{\partial y_k}{\partial u_k}
$$

对于前者可以使用损失函数的偏微分计算得到，对于后者可以使用激活函数的偏微分计算得到。令

$$
   \delta_k=\dfrac{\partial E}{\partial u_k}=\dfrac{\partial E}{\partial y_k}\cdot\dfrac{\partial y_k}{\partial u_k}
$$

所以，权重和梯度可以表示为

$$
   \partial{w_{jk}}=\delta_k\cdot y_j
$$

- 偏置的梯度也可以使用相同的求解方法。
  
$$
   \partial{b_k}=\dfrac{\partial E}{\partial{b_k}}=\dfrac{\partial E}{\partial u_k}\cdot\dfrac{\partial u_k}{\partial{b_k}}=\delta_k\cdot1.
$$

## 输出层的输入梯度

- 在输出层中，为了提前满足中间层的计算条件，必须提前计算$\partial y_j=\dfrac{\partial E}{\partial y_j}$（中间层的输出梯度，即输出层的输入梯度）。

- 对于$\partial y_j$​，有
  
$$
   \partial y_j=\sum_{r=1}^n \frac{\partial E}{\partial u_r}\cdot\frac{\partial u_r}{\partial y_j}
$$

对于后一项，有$\dfrac{\partial u_r}{\partial y_j}=w_{jr}$。所以，有

$$
   \partial y_j=\sum_{r=1}^n \delta_r\cdot w_{jr}
$$

其中，$\delta_r=\dfrac{\partial E}{\partial u_r}$。


## 中间层的梯度

- 在中间层中，和前序内容一样，我们有
  
$$
   \partial{w_{ij}}=\dfrac{\partial E}{\partial{w_{ij}}}=\dfrac{\partial E}{\partial u_j}\cdot\dfrac{\partial u_j}{\partial{w_{ij}}}=\frac{\partial E}{\partial u_j}y_i
$$

接下来，我们计算$\dfrac{\partial E}{\partial u_j}$。使用链式法则展开，得到

$$
   \dfrac{\partial E}{\partial u_j}=\dfrac{\partial E}{\partial y_j}\cdot\dfrac{\partial y_j}{\partial u_j}
$$

其中，右侧的第一部分为中间层的输出梯度，也就是之前求得的$\partial y_j$。第二部分为激活函数的偏微分。令

$$
   \delta_j=\dfrac{\partial E}{\partial u_j}=\partial y_j\cdot\dfrac{\partial y_j}{\partial u_j}
$$

这就可以使用在输出层中计算得到的$\partial y_j$对$\delta j$进行求解，实现了神经网络的回溯。
所以，权重的梯度可以表示为

$$
   \partial{w_{ij}}=\delta_j\cdot y_i
$$

- 对偏置的梯度进行求解：
  
$$
   \partial{b_j}=\dfrac{\partial E}{\partial{b_j}}=\dfrac{\partial E}{\partial u_j}\cdot\dfrac{\partial u_j}{\partial{b_j}}=\delta_j\cdot1
$$

其中，$\delta_j=\dfrac{\partial E}{\partial u_j}$，$\dfrac{\partial u_j}{\partial b_j}=1$。

## 对更多的中间层进行梯度的求解

$$
   \partial{y_i}=\sum_{r=1}^m \delta_r\cdot w_{ir}
$$

## 梯度的计算方法——回归

1. 设置方程：损失函数——平方和误差；中间层的激励函数——Sigmoid函数；输出层的激励函数——恒等函数。
2. 计算方法参见上述内容。

## 梯度的计算方法——分类

1. 设置方程：损失函数——交叉熵误差；中间层的激励函数——Sigmoid函数；输出层的激励函数——Softmax函数。
2. 计算方法参见上述内容。
