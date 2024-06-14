# 神经网络中的三大概念
1. 神经网络训练的最基本的思想就是：先“猜”一个结果，称为预测结果 $a$，看看这个预测结果和事先标记好的训练集中的真实结果 $y$ 之间的差距，然后调整策略，再试一次，这一次就不是“猜”了，而是有依据地向正确的方向靠近。如此反复多次，一直到预测结果和真实结果之间相差无几，亦即 $|a-y|\rightarrow 0$，就结束训练。
2. 在神经网络训练中，我们把“猜”叫做初始化，可以随机，也可以根据以前的经验给定初始值。即使是“猜”，也是有技术含量的。这里，这三大概念就蕴含在神经网络训练的全过程中。
3. 例子：猜数游戏。两个人玩猜数游戏，甲根据乙的反馈调整自己每一次猜测的值。这个例子可以被看作成一个深度学习的模型。
	- 目的：甲猜出乙给出的数字；
	- 初始化：甲猜5；
	- 前向计算：甲每次猜的新数字；
	- 损失函数：乙在根据甲猜的数来和自己心中想的数做比较，得出“大了”或“小了”的结论；（方向：“大”或“小”；程度：“太”、“有点儿”，但是十分的模糊）
	- 反向传播：乙告诉甲“小了”、“大了”；
	- 梯度下降：甲根据乙的反馈中的含义自行调整下一轮的猜测值。
## 反向传播
1. 反向传播（BP, back propagation）是一种用来训练人工神经网络的常见方法，它是一种与最优化方法（如梯度下降法）结合使用的。在训练过程中，神经网络根据输入数据和目标输出计算损失函数，然后通过反向传播算法计算损失函数对每一个参数的梯度。这些梯度信息指导了参数的更新方向，使得损失函数逐渐减小。反向传播算法是目前用来训练人工神经网络的最常用且最有效的算法。
## 梯度下降
1. 梯度下降是迭代法的一种,可以用于求解最小二乘问题(线性和非线性都可以)。在求解机器学习算法的模型参数，即无约束优化问题时，梯度下降（Gradient Descent）是最常采用的方法之一，另一种常用的方法是最小二乘法。在求解损失函数的最小值时，可以通过梯度下降法来一步步的迭代求解，得到最小化的损失函数和模型参数值。
2. 梯度：对于可微的数量场$f(x,y,z)$，以$\left(\dfrac{\partial f}{\partial x},\dfrac{\partial f}{\partial y},\dfrac{\partial f}{\partial z}\right)$为分量的向量场称为$f$的梯度或斜量。
3. 梯度下降法(Gradient Descent)是一个最优化算法，常用于机器学习和人工智能当中用来递归性地逼近最小偏差模型。
## 损失函数
1. 损失函数（Loss Function）或代价函数（Cost Function）是将随机事件或其有关随机变量的取值映射为非负实数以表示该随机事件的“风险”或“损失”的函数，通常用$L(Y,f(x))$表示。在应用中，损失函数通常作为学习准则与优化问题相联系，即通过最小化损失函数求解和评估模型。例如在统计学和机器学习中被用于模型的参数估计（parametric estimation）。损失函数越小，模型的鲁棒性就越好。