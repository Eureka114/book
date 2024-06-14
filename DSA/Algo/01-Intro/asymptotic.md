# 渐进分析

## 渐进符号

### 基础内容

Bachmann-Landau notation, also known as "Big-Oh" notation, abstracts away all constants in asymptotic analysis.



让$f(n),g(n)$为表示整数$n$的函数，我们使用如下的表示记号：

1. $f(n)\in O(g(n))$ if $\exists c,n_0>0$ such that $f(n)\le c\cdot g(n)$ for all $n>n_0$.
2. $f(n)\in\Omega(g(n))$ if $\exists c,n_0>0$ such that $f(n)\ge c\cdot g(n)$ for all $n>n_0$.
3. $f(n)\in\Theta(g(n))$ if both $f(n)\in O(g(n))$ and $f(n)\in\Omega(g(n))$ hold.

对于Big-O和Big-Omega的严格版本，定义如下：

1. $f(n)\in o(g(n))$ if for every constant $c>0$, there exists an integer $n_0>0$ such that $f(n)<c\cdot g(n)$ for all $n>n_0$.
2. $f(n)\in\omega(g(n))$ if for every constant $c>0$, there exists an integer $n_0>0$ such that $f(n)>c\cdot g(n)$ for all $n>n_0$.

> 此外，我们还可以这样表示：
>
> - $f(n)\in O(g(n))$ if $\lim\limits_{n\to\infty}\frac{f(n)}{g(n)}\le c_1$ for some constant $c_1$ which is independent of n.
> - $f(n)\in\Omega(g(n))$ if $\lim\limits_{n\to\infty}\frac{f(n)}{g(n)}\ge c_2$ for some constant $c_2$ which is independent of n.
> - $f(n)\in\Theta(g(n))$ if both $f(n)\in O(g(n))$ and $f(n)\in\Omega(g(n))$ hold. (i.e.  if $\lim\limits_{n\to\infty}\frac{f(n)}{g(n)}= c$ for some constant $c$ which is independent of n.)
>
> - $f(n)\in o(g(n))$ if $\lim\limits_{n\to\infty}\frac{f(n)}{g(n)}=0$.
> - $f(n)\in\omega(g(n))$ if $\lim\limits_{n\to\infty}\frac{f(n)}{g(n)}=\infty$.
>

注意：可以小写字母的符号推导到大写字母的符号，但是不能从某一个渐进符号的大写推出他的小写形式。

> 令$f(n)=2n^2+5n$，$g(n)=3n^2$，则有：
> 
> $$
> \lim_{n\to\infty}\frac{f(n)}{g(n)}=\lim_{n\to\infty}\frac{2n^2+5n}{3n^2}=\frac23
> $$
> 
> 令$c_1\in(\frac23,+\infty),c=\frac23,c_2\in(0,\frac23)$即可。但是，不能说$f(n)\in o(g(n))$或$f(n)\in\omega(g(n))$。

### 增长速度

|                 Function                 |            Type of Growth             |
| :--------------------------------------: | :-----------------------------------: |
|                   $1$                    |            Constant Growth            |
|                $\log^*n$                 |     Iterative Logarithmic Growth      |
|              $\log \log n$               |  Twice Iterative Logarithmic Growth   |
|                 $\log n$                 |          Logarithmic Growth           |
|   $\log^k n$ for some integer $k\ge1$    |        Polylogarithmic Growth         |
|        $n^k$ for some $k\in(0,1)$        |           Sublinear Growth            |
|                   $n$                    |             Linear Growth             |
|                $n\log n$                 |       Logarithmic-Linear Growth       |
|   $n\log^k n$ for some integer $k\ge1$   |     Polylogarithmic-Linear Growth     |
| $n^j\log^k n$ for some integer $j,k\ge1$ |   Polylogarithmic-Polynomial Growth   |
|                  $n^2$                   |           Quadratic Growth            |
|                  $n^3$                   |             Cubic Growth              |
|      $n^k$ for some integer $k\ge1$      | Polynomial growth (General Situation) |
|      $2^{\log^k n}$ for some $k>1$       |        Quasi-Polynomial Growth        |
|           $a^n$ for some $a>1$           |          Exponential Growth           |
|                   $n!$                   |           Factorial Growth            |
|                  $n^n$                   |       Super-Exponential Growth        |


### 计算技巧

- For any $\varepsilon>0$, we have $\log n=o(n^\varepsilon)$.

- **Striling Formula**: $n!\sim\sqrt{2\pi n}\left(\frac{n}{e}\right)^n$.
	- For example, using Stirling Formula, we can prove that $\log n!=\Theta(n\log n)$.
	
- **Addition Rule**:
	- If $f(n)\in O(g(n))$ and $h(n)\in O(k(n))$, then $f(n)+h(n)\in O(\max(g(n),k(n)))$; 
	- If $f(n) \in \Omega(g(n))$ and $h(n)\in\Omega(k(n))$, then $f(n)+h(n)\in\Omega(\min(g(n),k(n)))$.
	
- **Composition Rule**:
	- If $f(n)\in O(g(n))$ and $h(n)\in O(k(n))$, then $(f\circ h)(n)\in O(g\circ k)(n)$; 
	- If $f(n)\in\Omega(g(n))$ and $h(n)\in\Omega(k(n))$, then $(f\circ h)(n)\in\Omega(g\circ k)(n)$.
	
- **L'Hôpital's Rule**: Suppose $f(n)$ and $g(n)$ are both differentiable functions which satisfy L'Hôpital's Rule prerequisties, then
  
	$$
	\lim_{n\to\infty}\frac{f(n)}{g(n)}=\lim_{n\to\infty}\frac{f'(n)}{g'(n)}
	$$

- **Log Ratio Test**: Suppose *f* and *g* are continuous functions over the interval $[1,+\infty)$ and
  
	$$
	\lim_{n\to\infty}\log\left(\frac{f(n)}{g(n)}\right)=\lim_{n\to \infty}\log f(n)-\lim_{n\to \infty}\log g(n)=L.
	$$

    Then,
	
	- If $L=\infty$, then $\lim\limits_{n\to\infty}\dfrac{f(n)}{g(n)}=\infty$;
	- If $L=-\infty$, then $\lim\limits_{n\to\infty}\dfrac{f(n)}{g(n)}=0$;
	- Otherwise, $\lim\limits_{n\to\infty}\dfrac{f(n)}{g(n)}=2^L$.

	> 注意：这里不能使用$\lim\limits_{n\to\infty}\dfrac{\log A(n)}{\log B(n)}$.

- **Intergal Theorem**: Let $f(x)>0$ be an increasing function or decreasing Riemann-integrable function over the interval $[1,+\infty)$, then

    $$
    \sum_{i=1}^nf(i)=\Theta\left(\int_1^nf(x)dx\right).
    $$

    if $f(x)$ is a decreasing function. Moreover, if $f(x)$ is an increasing function, the same is true, provided that $f(n)=O(\int_1^nf(x)dx)$.

## 例题

- 判断$f(n)$是否为$O(g(n))$：

> Question 1: Prove that $3n$ is $O(n^2)$.
>
> Answer 1: $f(n)\le cg(n),\forall n\ge n_0$. Let $n_0=1\Rightarrow c=3$. So statement$3n\le 3n^2,\forall n\ge 1$ is true. Q.E.D.

- 判断$A(n)$和$B(n)$的时间复杂度关系（注意，不是所有的方程都能在渐进的意义下进行比较，如$f(n)=n^{1+\sin n}$）：

> Question 2: Compute $\lim_{n\to\inf}\frac{A(n)}{B(n)}$ to find the relationship:
>
> Answer 2: 
>
> - if it limits to 0: $A(n)\in O(B(n))$; 
> - if it limits to $\infty$: $B(n)\in O(A(n))$; 
> - if it limits to non-zero values: $A(n)=\Theta(B(n))$ (i.e. A(n) and B(n) has the same growth rate.);
> -  if it limits oscillationally, they are no relationship.

> Question 3: Prove that $B(n)\in O(A(n))$, where $A(n)=2^n,B(n)=n^3$.
> 
> $$
> \begin{aligned}
> \lim\limits_{n\to \infty}\frac{A(n)}{B(n)}&=\lim\limits_{n\to \infty}\frac{2^n}{n^3}=\lim\limits_{n\to \infty}\frac{2^n\cdot\ln2}{3n^2}=\lim\limits_{n\to \infty}\frac{2^n\cdot\ln2\cdot\ln2}{6n}\\
> &=\lim\limits_{n\to \infty}\frac{2^n\cdot\ln2\cdot\ln2\cdot\ln2}{6}=\infty
> \end{aligned}
> $$
> 
> which is to say, $B(n)$ is $O(A(n))$. $A(n)$ grows more than B(n).

## 参考资料

1. [lecture2.pdf (jhu.edu)](https://www.cs.jhu.edu/~mdinitz/classes/IntroAlgorithms/Fall2021/Lectures/Lecture2/lecture2.pdf)
1. [bigO.pdf (csulb.edu)](https://home.csulb.edu/~tebert/teaching/lectures/528/bigO/bigO.pdf)
1. [ana-asymp-6up.pdf (chula.ac.th)](https://www.cp.eng.chula.ac.th/~somchai/CD/2110427/2542/Lectures/Slides/pdf/ana-asymp-6up.pdf)
1. [asymptotics - Are the functions always asymptotically comparable? - Computer Science Stack Exchange](https://cs.stackexchange.com/questions/1780/are-the-functions-always-asymptotically-comparable)