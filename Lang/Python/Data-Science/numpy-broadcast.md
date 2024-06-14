# NumPy 数组广播的温和介绍

## 数组运算的局限性

您可以直接在 NumPy 数组上执行算术运算，例如加法和减法。

例如，可以将两个数组相加以创建一个新数组，其中每个索引的值都加在一起。

例如，数组 a 可以定义为[1,2,3]，数组 b 可以定义为[1,2,3]，加在一起将产生一个值为[2,4,6]的新数组。

```
a = [1, 2, 3]
b = [1, 2, 3]
c = a + b
c = [1 + 1, 2 + 2, 3 + 3]
```

严格地说，算术只能在具有相同尺寸和尺寸的相同尺寸的数组上执行。

这意味着长度为 10 的一维数组只能与另一个长度为 10 的一维数组进行算术运算。

对数组算术的这种限制确实非常有限。值得庆幸的是，NumPy 提供了一个内置的解决方法，允许在具有不同大小的数组之间进行算术运算。

## 数组广播

广播是 NumPy 用于允许具有不同形状或大小的数组之间的数组运算的方法的名称。

尽管该技术是针对 [NumPy](https://docs.scipy.org/doc/numpy-1.13.0/user/basics.broadcasting.html) 开发的，但它在其他数值计算库中也被广泛采用，例如 [Theano](http://deeplearning.net/software/theano/tutorial/broadcasting.html) ， [TensorFlow](https://www.tensorflow.org/performance/xla/broadcasting) 和 [Octave](https://www.gnu.org/software/octave/doc/v4.2.1/Broadcasting.html) 。

广播通过实际上沿着最后的不匹配维度复制较小的数组来解决不同形状的数组之间的算术问题。

> 术语广播描述了 Numpy 如何在算术运算期间处理具有不同形状的数组。受某些约束的影响，较小的数组在较大的数组上“广播”，以便它们具有兼容的形状。

- [广播](https://docs.scipy.org/doc/numpy-1.13.0/user/basics.broadcasting.html)，SciPy.org

NumPy 实际上并没有复制较小的数组;相反，它使存储器和计算上有效地使用存储器中的现有结构，实际上实现了相同的结果。

该概念还渗透了线性代数符号，以简化简单操作的解释。

> 在深度学习的背景下，我们也使用一些不太常规的符号。我们允许添加矩阵和向量，产生另一个矩阵：$\mathbf{C} =\mathbf{A} + \mathbf{b}$，其中$\mathbf{C}_{i,j}=\mathbf{A}_{i,j}+\mathbf{B}_j$。换句话说，向量 b 被添加到矩阵的每一行。这种简化消除了在添加之前定义将 b 复制到每行中的矩阵的需要。将 b 隐式复制到许多位置称为广播。

- 第 34 页，[深度学习](http://amzn.to/2qJRxrv)，2016 年。

## 在 NumPy 广播

我们可以通过查看 NumPy 中的三个例子来制作广播。

本节中的示例并非详尽无遗，而是与您可能看到或实现的广播类型相同。

### 标量和一维数组

单个值或标量可用于具有一维数组的算术。

例如，我们可以设想一维数组“a”，其中三个值[a1，a2，a3]被添加到标量“b”。

```
a = [a1, a2, a3]
b
```

标量需要通过将其值重复 2 次来跨一维数组进行广播。

```
b = [b1, b2, b3]
```

然后可以直接添加两个一维数组。

```
c = a + b
c = [a1 + b1, a2 + b2, a3 + b3]
```

以下示例在 NumPy 中演示了这一点。

```python
# scalar and one-dimensional
from numpy import array
a = array([1, 2, 3])
print(a)
b = 2
print(b)
c = a + b
print(c)
```

运行该示例首先打印定义的一维数组，然后是标量，然后是结果，其中标量被添加到数组中的每个值。

```
[1 2 3]

2

[3 4 5]
```

### 标量和二维数组

标量值可用于具有二维数组的算术。

例如，我们可以想象一个二维数组“A”，其中 2 行和 3 列添加到标量“b”。

```
     a11, a12, a13
A = (a21, a22, a23)

b
```

标量将需要在二维数组的每一行上进行广播，方法是将其复制 5 次。

```
     b11, b12, b13
B = (b21, b22, b23)
```

然后可以直接添加两个二维数组。

```
C = A + B

     a11 + b11, a12 + b12, a13 + b13
C = (a21 + b21, a22 + b22, a23 + b23)
```

The example below demonstrates this in NumPy.

```python
# scalar and two-dimensional
from numpy import array
A = array([[1, 2, 3], [1, 2, 3]])
print(A)
b = 2
print(b)
C = A + b
print(C)
```

运行该示例首先打印定义的二维数组，然后打印标量，然后将值为“2”的加法结果添加到数组中的每个值。

```
[[1 2 3]
 [1 2 3]]

2

[[3 4 5]
 [3 4 5]]
```

### 一维和二维数组

一维数组可用于具有二维数组的算术。

例如，我们可以想象一个二维数组“A”，其中 2 行 3 列添加到具有 3 个值的一维数组“b”。

```
     a11, a12, a13
A = (a21, a22, a23)

b = (b1, b2, b3)
```

通过创建第二副本以产生新的二维数组“B”，在二维数组的每一行上广播一维数组。

```
     b11, b12, b13
B = (b21, b22, b23)
```

The two two-dimensional arrays can then be added directly.

```
C = A + B

     a11 + b11, a12 + b12, a13 + b13
C = (a21 + b21, a22 + b22, a23 + b23)
```

以下是 NumPy 中的一个有效例子。

```python
# one-dimensional and two-dimensional
from numpy import array
A = array([[1, 2, 3], [1, 2, 3]])
print(A)
b = array([1, 2, 3])
print(b)
C = A + b
print(C)
```

运行该示例首先打印定义的二维数组，然后打印定义的一维数组，接着是结果 C，其中实际上二维数组中的每个值都加倍。

```
[[1 2 3]
 [1 2 3]]

[1 2 3]

[[2 4 6]
 [2 4 6]]
```

## 广播的局限性

广播是一种方便的快捷方式，在使用 NumPy 数组时在实践中非常有用。

话虽如此，它并不适用于所有情况，实际上强加了一个严格的规则，必须满足广播要执行。

算术（包括广播）只能在数组中每个维度的形状相等或者维度大小为 1 时执行。**维度以相反的顺序考虑，从尾随维度开始。**例如，在二维情况下查看行之前的列。

当我们考虑 NumPy 在比较数组时实际填充缺少尺寸为“1”的尺寸时，这更有意义。

因此，具有 2 行和 3 列的二维数组“A”与具有 3 个元素的向量“b”之间的比较：

```
A.shape = (2 x 3)
b.shape = (3)
```

实际上，这成了一个比较：

```
A.shape = (2 x 3)
b.shape = (1 x 3)
```

同样的概念适用于被视为具有所需维数的数组的标量之间的比较：

```
A.shape = (2 x 3)
b.shape = (1)
```

这成为以下方面的比较：

```
A.shape = (2 x 3)
b.shape = (1 x 1)
```

当比较失败时，不能执行广播，并且引发错误。

下面的示例尝试将两元素数组广播到 2 x 3 数组。这种比较有效：

```
A.shape = (2 x 3)
b.shape = (1 x 2)
```

我们可以看到最后的维度（列）不匹配，我们希望广播失败。

The example below demonstrates this in NumPy.

```python
# broadcasting error
from numpy import array
A = array([[1, 2, 3], [1, 2, 3]])
print(A.shape)
b = array([1, 2])
print(b.shape)
C = A + b
print(C)
```

运行该示例首先打印数组的形状，然后在尝试广播时引发错误，正如我们预期的那样。

```
(2, 3)
(2,)
ValueError: operands could not be broadcast together with shapes (2,3) (2,)
```
