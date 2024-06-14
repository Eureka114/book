# 算法部分题目

## 算法-1：简介

### Task 1

- 根据`power2()`，设计一个算法，在 O(logn)时间内计算出`fib(n)`。其中，`power2()`方法是用来求幂函数$2^n$，实现如下：

	```cpp
	//power2
	long long sqr ( long long a ) { return a * a; } //平方：若是连续执行，很快就会数值溢出！
	long long power2 ( int n ) { //幂函数2^n算法（优化递归版），n >= 0
	    if ( 0 == n ) return 1; //递归基；否则，视n的奇偶分别递归
	    return ( n & 1 ) ? sqr ( power2 ( n >> 1 ) ) << 1 : sqr ( power2 ( n >> 1 ) );
	} //O(logn) = O(r)，r为输入指数n的比特位数
	```

	> 解：由于斐波那契数列可以使用矩阵乘法来求值：
	>
	> $$
	> 	\begin{pmatrix}    0 & 1 \\    1 & 1  \end{pmatrix} \cdot\begin{pmatrix}    fib(n-1) \\fib(n)  \end{pmatrix} =\begin{pmatrix}    fib(n) \\    fib(n+1)  \end{pmatrix}
	> $$
	>
	> 则可以得到如下公式：
	>
	> $$
	> 	\left(\begin{array}{c}fib(n) \\fib(n+1)\end{array}\right)=\left(\begin{array}{ll}0 & 1 \\1 & 1\end{array}\right)^n\left(\begin{array}{l}fib(0) \\fib(1)\end{array}\right)=\left(\begin{array}{ll}0 & 1 \\1 & 1\end{array}\right)^n\left(\begin{array}{l}0 \\1\end{array}\right)
	> $$
	>
	> 而矩阵乘法的时间复杂度为常数时间。则总的时间复杂度为$O(\log n)$。

### Task 2

- 对中华更相减损术算法的分析：

	```cpp
	long long gcdCN ( long long a, long long b ) { //assert: 0 < min(a, b)
	    int r = 0; //a和b的2^r形式的公因子
	    while ( ! ( ( a & 1 ) || ( b & 1 ) ) ) { //若a和b都是偶数
	        a >>= 1; b >>= 1; r ++; //则同时除2（右移），并累加至r
	    } //以下，a和b至多其一为偶
	    while ( 1 ) {
	        while ( ! ( a & 1 ) ) a >>= 1; //若a偶（b奇），则剔除a的所有因子2
	        while ( ! ( b & 1 ) ) b >>= 1; //若b偶（a奇），则剔除b的所有因子2
	        ( a > b ) ? a = a - b : b = b - a; //简化为：gcd(max(a, b) - min(a, b), min(a, b))
	        if ( 0 == a ) return b << r; //简化至平凡情况：gcd(0, b) = b
	        if ( 0 == b ) return a << r; //简化至平凡情况：gcd(a, 0) = a
	    }
	}
	```

	> 1. 该算法的渐进时间复杂度$O(\log(a+b))$，与欧几里得算法相同。
	> 2. 考查该算法的每一步迭代，紧接于两个内部`while`循环之后设置一个断点，观察此时的`a`和`b`。实际上，在a和b各自剔除了所有因子2之后，此时它们都将是奇数。接下来，无论二者大小如何，再经一次互减运算，它们必然将成为一奇一偶。比如，不失一般性，设$a>b$，则得到：$(a-b)$偶数，$b$奇数；再经一步迭代并重新回到断点时，前者至多是：$(a-b)/2$，两个变量之和至多是$(a-b)/2+b\le(a+b)/2$。可见，每经过一步迭代，$a+b$减少一半，故总体迭代步数不超过：$\log_2(a+b)$。
	> 3. 并且，相较于欧几里得算法而言，该算法涉及的运算更为简单，只有加减和移位操作，而没有欧几里得算法中的乘除操作。

### Task 3

- 设计一个就地移位的算法，使得有$n$个元素的数组`arr[]`进行平移操作，让第$k$项$(k\le n-1)$至数组的首位，让第$k-1$项至数组的末位。

	```cpp
	int shift2 ( int* A, int n, int k ) { //倚劣倒置算法，将数组循环左秱k位，O(3n) 
	    k %= n; //确保k <= n 
	    reverse ( A, k ); //将匙间A[0, k)倒置：O(3k/2)次操作 
	    reverse ( A + k, n - k ); //将匙间A[k, n)倒置：O(3(n - k)/2)次操作 
	    reverse ( A, n ); //倒置整个数组A[0, n)：O(3n/2)次操作 
	    return 3 * n; //迒回累计操作次数，以便不其它算法比较：3/2 * (k + (n - k) + n) = 3n 
	}//逻辑十分巧妙
	```

	> 原理：若在原向量$V$中前$k$个元素组成的前缀为$L$，剩余的（后缀）部分为$R$，经整体左移之后的向量应为$R+L$；这里约定，任意向量$V$整体倒置后的结果记作$V'$。于是该算法的原理来自如下恒等式：
	>
	> $$
	> 	R+L=(L'+R')'
	> $$

### Task 4

对于某些算法的时间复杂度分析：

- Subtask 1:

	```cpp
	void F(int n) { 
	for (int i = 0; i < n; i ++) 
	   for (int j = 1; j < n; j <<= 1); 
	}
	```

	> 复杂度：
	>
	> $$
	> \sum_{i=0}^{n-1}\sum_{(k=\log j)=0}^{\log n-1}1=\sum_{i=0}^{n-1}(\log n-1)=n\log n-n=O(n\log n)
	> $$

- Subtask 2:

	```cpp
	void F(int n){
	    for(int i=1,r=1; i<n; i<<=r,r<<=1);
	}
	```

	> 复杂度：$i\to i\cdot2^r,\ r\to2^r$。所以，有$i=\prod\limits_{t=0}^{k-1}2^{2^t}=2^{2^k-1}$, $i<n\Rightarrow2^k-1\le\log n\Rightarrow k\le\log\log n$.

- Subtask 3:

	```cpp
	void F(int n){
		for(int i=1;i<n;i=1<<i)
	}
	```

	> 即每经一次迭代，$i$即增长至$2^i$。设经过$k$次迭代之后，因$i\ge n$而退出迭代。现颠倒原迭代的方向，其过程应等效于反复令$n = \log_2n$，并经k次迭代之后有$n\le1$。由此可知，若对$n$反复取对数直至其不大于1，则$k$等于其间所做对数运算的次数，记作$k = \log^*n$， 读作“log-星-n”。
	>
	> 所以时间复杂度为$O(\log^*n)$.

	

	> $O(\log\log n)$和$O(\log^*n)$均可视作常数。特别地，对于$n=2^{270}$，有$\log^*n<5$。

- Subtask 4:

	```cpp
	int sum( int A[], int n ) 
	{  
		return  n < 1  ?  0 : sum(A, n - 1) + A[n - 1];  
	}
	```

	> 时间复杂度：
	>
	> 由于单个递归实例需要$O(1)$时间完成，共有$n$个实例，所以整个算法的复杂度是$O(n)$。当`sum(A,n)`调用`sum(A,n-1)`时，`sum(A,n)`函数中的数据以“函数帧”的形式被压入一个栈中，并没有处于执行状态。

### Task 5

下列函数渐进增长的大小排序：

$$
O\left(\frac{n}{\log n}\right)>O(n^\frac23)>O(\log^2n)>O(\log\log n)
$$

因为，对数函数$\log n$的渐进增长要慢于任何次数的幂函数$O(x^{\varepsilon}),\ \varepsilon>0$。