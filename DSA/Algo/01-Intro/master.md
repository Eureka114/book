# 递归式求解——代入法、递归树与主定理

[递归式求解——代入法、递归树与主定理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/267890781)

[Master theorem (analysis of algorithms) - Wikipedia](https://en.wikipedia.org/wiki/Master_theorem_(analysis_of_algorithms))

## 从**分治法**到**递归式**

所谓**分治法**，就是把大问题分解成小问题，再把小问题的解合并成原问题的解。**递归式**则是用数学语言对这一过程的时间代价建模。如MergeSort的递推式：


$$
   T(n)=2T(n/2)+\Theta(n),T(1)=\Theta(1).
$$


> 递归式的右侧通常由两部分组成，第一部分是分解后子问题的总代价，第二部分是分解和合并子问题的总代价。第一部分仍然是T的函数，第二部分则是实际的渐进复杂度。

以下有三种方法来求解递归式：代入法、递归树和主定理。

## 代入法

1. 我们需要先猜出解的形式，然后用数学归纳法证明该猜测是正确的。
2. 实例：假设猜测MergeSort的T解是$T(n)=O(n\log n)\to T(n)\le c\cdot n\log n, \exists c \in \mathbb{R}.$ 然后猜测MergeSort $T(n)=\Omega(n\log n)$, 若两者均正确，则MergeSort的T解为$T(n)=\Theta(n\log n)$.
3. 代入法依赖于准确的猜测。

## 递归树

1. 把递归式逐层展开，直接就能得到一个树形结构。
2. 实例：$T(n)=3T(n/4)+\Theta(n^2)$. 具体流程见网页链接。每层数据规模缩小为之前的四分之一，直到叶子结点数据规模为1，因此总层数为$\log_43$。右侧计算了每层所有结点的总代价：单个结点的代价乘以该层结点数量。最后一层单个结点的代价为常数，结点数量$3^{\log_4n}=n^{\log_43}$，记作$\Theta(n^{\log_43})$。然后对几何级数求和（放缩、舍去低阶项）。
3. 有些情况下，利用递归树计算精确的解会稍有困难，但不妨碍我们做出一个合理的猜测，然后进一步用代入法验证该猜测。

## 主方法

代入法和递归树法各有缺点，前者需要一个准确的猜测，后者需要画出递归树并推导结果。而大部分递归式解的形式都是一样的。我们也许可以通过对递归式分类，按照某种规则直接写出最终的解。

若分治法每一次都均分数据时，递归式具有如下一般的形式：

$$
	T(n)= \begin{cases} aT(n/b)+f(n) & \text { if } n \geq d \\ \Theta(1) & \text { if } n<d \end{cases}
$$

> 这里，*a*是一个正整数，表示每次分治，问题被分解为*a*个子问题;
>
> *b* 是一个大于1的整数，表示每次分治，问题规模缩小为之前的$\dfrac1b$.
>
> *f(n)*是渐进正函数，表示分解和合并的代价。
>

用递归树找统一解，Total为所有节点的总代价：

![img](https://pic4.zhimg.com/80/v2-a0895b3402aa4bbcd5f405006e2b0ebf_1440w.webp)

$T(1)=\Theta(1)$为基本操作所花费的时间。化简后，有：

$$
T(n)=n^{\log_ba}\cdot T(1)+\sum_{j=0}^{\log _{b} n-1} a^{j} f\left(n / b^{j}\right)
$$

下面有三种情况，按照两个和项的大小进行分类，即比较$f(n)$和$n^{\log_ba}$的大小：

1. 若$f(n)$渐进小于$n^{\log_ba}$.，即认为$\exists \epsilon>0,\ s.t.\ f(n)=O(n^{\log_ba-\epsilon})$成立时，有$T(n)$为$\Theta(n^{\log_ba})$.
   
> If $f(n)$ is $O(n^{\log_ba-\varepsilon})$, then $T(n)$ is $\Theta(n^{\log_ba})$.

2. 若$f(n)$和$n^{\log_b a}\log^k n$相等$(k\ge0)$，则$T(n)=\Theta(n^{\log_b a}\log^{k+1} n)$.
   
> If $f(n)$ is $\Theta\left(n^{\log _{b} a} \log ^{k} n\mathbb{R}\right)$, then $T(n)$ is $\Theta\left(n^{\log _{b} a} \log ^{k+1} n\mathbb{R}\right)$.

3. 若$f(n)$渐进大于$n^{\log_ba}$，**且对于$\delta$<1和所有足够大的n有$af(n/b)\le \delta f(n)$，** 则有$T(n)=\Theta(f(n))$.

> If $f(n)$  is $\Omega\left(n^{\log _{b} a+\varepsilon}\mathbb{R}\right)$ , then  T(n)  is  $\Theta(f(n))$ , provided  a $f(n / b) \leq \delta f(n)$  for some  $\delta<1$ .



实例：$T(n)=9T(n/3)+n,\ a=9,\ b=3,\ f(n)=n,\ n^{\log_ba}=n^2$, $f(n)<\Theta(n^2)$, 故$T(n)=\Theta(n^2)$. 

必须注意，主定理第三条要检查其是否满足附加条件。并非所有满足上面形式的式子都可以使用主定理来进行判定。如$T(n)=2T(n/2)+n\log n$, 不满足上述附加条件，故只能使用递归树求解。求解过程如下：

>$$
>T(n)=2T(n/2)+n\log n
>$$
>
>
>
>对于该式，有 $a=2,b = 2,f\left(n\mathbb{R}\right) = n\log n$ ，此时 $n^{log_ba} = n^{log_22} = n$ 。显然， $f(n\mathbb{R}) = n\log n$ 渐进大于 $n$ ，应考虑主定理的第三种情况。将其带入附加条件，得
>
>
>$$
>\begin{aligned}
>  2 \frac{n}{2} \lg \frac{n}{2} & \leq c n \lg n \\\lg n-1 & \leq c \lg n \\(1-c) \lg n & \leq 1\end{aligned}
>$$
>
>
>当 $c \ge 1$ 时，该式恒成立。但附加条件要求 $c \lt 1$ ，因此该式不能使用主方法的第三种情况。结果为$T(n)=\Theta(n\log^2 n)$.

> 令解：带入方法2可求解。

## 特别内容

1. 对于任意一个符合主定理格式的递归式，如果它满足情况三中的正则条件，那么它一定满足$f(n)-\Omega(n^{\log_ba+\epsilon})$, 而反过来不可推出。
2. 另一个例子：求解递推方程$T(n)=T(n/3)+T(2n/3)+n$. 这个递归树的树叶不在同一层上面。此时，应从最坏的情况出发，考虑最长的路径。那么这个递归树一共有$k=\log_{3/2}n$层。（2n/3一侧的路径最长，每走一步，问题规模会变成原来的2/3.）而每一层节点的数值之和为O(n)，从而有：$T(n)=O(n\log_{3/2}n)=O(n\log n)$.

## Wikipedia上的主定理——Generic Form

1. The master theorem always yields [asymptotically tight bounds](https://en.wikipedia.org/wiki/Asymptotically_tight_bound) to recurrences from [divide and conquer algorithms](https://en.wikipedia.org/wiki/Divide_and_conquer_algorithm) that partition an input into smaller subproblems of equal sizes, solve the subproblems recursively, and then combine the subproblem solutions to give a solution to the original problem. The time for such an algorithm can be expressed by adding the work that they perform at the top level of their recursion (to divide the problems into subproblems and then combine the subproblem solutions) together with the time made in the recursive calls of the algorithm. If $T(n)$ denotes the total time for the algorithm on an input of size $n$ and $f(n)$ denotes the amount of time taken at the top level of the recurrence then the time can be expressed by a [recurrence relation](https://en.wikipedia.org/wiki/Recurrence_relation) that takes the form:

2. Here $n$ is the size of an input problem, $a$ is the number of subproblems in the recursion, and $b$ is the factor by which the subproblem size is reduced in each recursive call (b>1). Crucially, $a$ and $b$ must not depend on $n$. The theorem below also assumes that, as a base case for the recurrence, $T(n)=\Theta (1)$ when $n$ is less than some bound $\kappa >0$, the smallest input size that will lead to a recursive call.

3. Recurrences of this form often satisfy one of the three following regimes, based on how the work to split/recombine the problem $f(n)$ relates to the ***critical exponent*** $c_{crit}=\log _{b}a$. 

   1. **Case 1**: 

      - Description: Work to split/recombine a problem is dwarfed by subproblems. (I.e. the recursion tree is leaf-heavy.)

      - Condition on $f(n)$ in relation to $c_{crit}$, i.e. $\log_ba$: When $f(n)=O(n^{c})$ where $ c<c_{crit}$. (upper-bounded by a lesser exponent polynomial)

      - Master Theorem bound: then $T(n)=\Theta(n^{c_{crit}})$. (The splitting term does not appear; the recursive tree structure dominates.)

      - Notational examples: If $b=a^{2}$ and $f(n)=O(n^{1/2-\epsilon })$, then $T(n)=\Theta (n^{1/2})$.

        > 描述：拆分/重组问题的工作与子问题相形见绌。（即递归树的叶子过多）
        >
        > $f(n)$和$\log_ba$的关系：当$f(n)=O(n^{c})$时，其中$c<\log_ba$。（上界为小指数多项式）
        >
        > 主定理的约束条件：$T(n)=\Theta(n^{c_{crit}})=T(n)=\Theta(n^{\log_b a})$。（递归树占主导地位，而未出现拆分项。）

   2. **Case 2**:

      1. Description: Work to split/recombine a problem is comparable to subproblems.

      2. Condition on $f(n)$ in relation to $c_{crit}$, i.e. $\log_ba$: When $f(n)=\Theta (n^{c_{crit}}\log ^{k}n)$ for a $k\geq 0$ (rangebound by the critical-exponent polynomial, times zero or more optional $\log s$)

      3. Master Theorem bound: then $T(n)=\Theta (n^{c_{crit}}\log ^{k+1}n\mathbb{R})$ (The bound is the splitting term, where the log is augmented by a single power.)

      4. Notational examples: If $b=a^{2}$ and $f(n)=\Theta (n^{1/2})$, then $T(n)=\Theta (n^{1/2}\log n)$; If $b=a^{2}$ and $f(n)=\Theta (n^{1/2}\log n)$, then $T(n)=\Theta (n^{1/2}\log ^{2}n)$.

         > 描述：拆分/重组问题的工作与子问题相当。
         >
         > $f(n)$和$\log_ba$的关系：当 $f(n)=\Theta (n^{c_{crit}}\log ^{k}n)$时，其中$k\ge 0$。（界被$\log_ba$所限，乘以零或可选的$\log s$）
         >
         > 主定理的约束条件：$T(n)=\Theta (n^{c_{crit}}\log ^{k+1}n\mathbb{R})$。（界值为拆分项，对数的次数增加1。）

   3. **Case 3**:

      1. Description: Work to split/recombine a problem dominates subproblems. (I.e. the recursion tree is root-heavy.)

      2. Condition on $f(n)$ in relation to $c_{crit}$, i.e. $\log_ba$: When $f(n)=\Omega (n^{c})$ where $c>c_{crit}$. (lower-bounded by a greater exponent polynomial)

      3. Master Theorem bound: this doesn't necessarily yield anything. Furthermore, if $af\left({\frac {n}{b}}\mathbb{R}\right)\leq kf(n)$ for some constant $k<1$ and sufficiently large $n$ (often called the *regularity condition*) then the total is dominated by the splitting term $f(n)$: $T\left(n\mathbb{R}\right)=\Theta \left(f(n)\mathbb{R}\right)$.

      4. Notational examples: If $b=a^{2}$ and $f(n)=\Omega (n^{1/2+\epsilon })$ and the regularity condition holds, then $T(n)=\Theta (f(n))$.

         > 描述：拆分/重组问题的工作远胜于子问题。（即递归树的根过多）
         >
         > $f(n)$和$\log_ba$的关系：当$f(n)=O(n^{c})$时，其中$c>\log_ba$。（下界为大指数多项式）
         >
         > 主定理的约束条件：这不一定有结果。若$\exists k<1$和一个很大的$n$（规则性条件），有$af\left({\dfrac {n}{b}}\mathbb{R}\right)\leq kf(n)$，则结果由拆分项$f(n)$主导，$T\left(n\mathbb{R}\right)=\Theta \left(f(n)\mathbb{R}\right)$。


A useful extension of Case 2 handles all values of $k$:

| Case | Condition on $f(n)$ in relation to $c_{crit}$, i.e. $\log _{b}a$ | Master Theorem bound                                         | Notational examples                                          |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2a   | When $f(n)=\Theta (n^{c_{crit}}\log ^{k}n)$ for any $k>-1$ | ... then $T(n)=\Theta \left(n^{c_{crit}}\log ^{k+1} n\mathbb{R}\right)$ (The bound is the splitting term, where the log is augmented by a single power.) | If $b=a^{2}$ and $f(n)=\Theta (n^{1/2}/\log ^{1/2}n)$, then $T(n)=\Theta (n^{1/2}\log ^{1/2}n)$. |
| 2b   | When $f(n)=\Theta (n^{c_{crit}}\log ^{k}n)$ for $k=-1$ | ... then $T(n)=\Theta \left(n^{c_{crit}}\log \log n\mathbb{R}\right)$ (The bound is the splitting term, where the log reciprocal is replaced by an iterated log.) | If $b=a^{2}$ and $f(n)=\Theta (n^{1/2}/\log n)$, then $T(n)=\Theta (n^{1/2}\log \log n)$. |
| 2c   | When $f(n)=\Theta (n^{c_{crit}}\log ^{k}n)$ for any $k>-1$ | ... then $T(n)=\Theta \left(n^{c_{crit}}\mathbb{R}\right)$ (The bound is the splitting term, where the log disappears.) | If $b=a^{2}$ and $f(n)=\Theta (n^{1/2}/\log^2 n)$, then $T(n)=\Theta (n^{1/2})$. |

