---
title: 02C-Basic-无序向量
katex: true
categories: dsa
---

# 无序向量

## C++模板类

1. `template`关键字用于定义模板。在C++中，`template`关键字可以用来定义两种主要的模板：函数模板和类模板。

   1. **函数模板**：函数模板是一种特殊的函数，可以处理不同类型的数据。例如，你可以创建一个函数模板来实现一个比较两个元素大小的函数，而不需要为每种数据类型都写一个单独的函数。

      ```c++
      template <typename T>
      T max(T a, T b) {
          return (a > b) ? a : b;
      }
      ```

   2. **类模板**：类模板是一种特殊的类，可以处理不同类型的数据。例如，你可以创建一个类模板来实现一个通用的向量类，而不需要为每种数据类型都写一个单独的向量类。

      ```cpp
      template <typename T>
      class Vector {
      private:
          T* data;
          int size;
      public:
          Vector(int size) : size(size) {
              data = new T[size];
          }
          ~Vector() {
              delete[] data;
          }
          // 其他成员函数...
      };
      ```

   3. 此外，还可以定义模板特化（Template Specialization），它允许你为特定类型定义不同的模板实现。这种好处很多，不仅可以实现泛型编程，而且在未来学习新的DSA时候通过这样的方式定义新的数据结构。如森林的定义：

      ```c++
      template<typename T> class Vector{...;}
      template<typename T> class BinTree{...;}
      Vector<BinTree> forest;//c++
      ```

      ```Java
      class Vector<E> implements List<E>{...;}
      class BinTree<T>{...;}
      ...
      Vector<BinTree> forest;//Java
      ```

## 循秩访问（Call by Rank）

1. 在向量类中，元素可以通过`vec.get(r)`（C++, Java）来访问。当然，在C++中，也可以通过重载下标运算符`[]`来实现循秩访问。（Java中则不可以） 

    ```c++
    template<typename T> T & Vector<T>::operator[](Rank r) { return _elem[ r ]; }//可以作为左值（将某个值赋值给向量中的某个元素）
    template<typename T> T & Vector<T>::operator[](Rank r) const { return _elem[ r ]; }//可以作为左值，但只能作为右值（将向量中的某个元素赋值给其他非向量的同一类型的变量）
    ```

    > 左值返回值之所以可以实现，是因为返回值是一个引用 `&`。

2. 插入和删除（将某些元素左移、右移）：

   ```c++
   template <typename T> Rank Vector<T>::insert( Rank r, T const & e ) {//0<=r<=size
       expand(); //如必要，先扩容
   	for ( Rank i = _size; r < i; i-- ) //O(n-r)：**自后向前**
   	_elem[i] = _elem[i - 1]; //后继元素顺次后移一个单元
       _elem[r] = e; _size++; return r;  //置入新元素，更新容量，返回秩
   }//返回插入的位置（若成功）
   ```

   ```java
    public Object insertAtRank(int r, Object obj) throws ExceptionBoundaryViolation{
        if (0 > r || r > n) throw new ExceptionBoundaryViolation("意外：秩越界"); 
        if (N <= n) {//空间溢出的处理 
            N *= 2; 
            Object B[] = new Object[N];//开辟一个容量加倍的数组 
            for (int i=0; i<n; i++){
                B[i] = A[i];//A[]中内容复制至B[] 
            }
            A = B;//用B替换A（原A[]将被自动回收） 
        } 
        for (int i=n; i>r; i--) 
            A[i] = A[i-1];//后续元素顺次后移 
        A[r] = obj;//插入 
        n++;//更新当前规模 
        return obj; 
    }
   ```

   ```c++
   template <typename T> Rank Vector<T>::remove( Rank lo, Rank hi ) { //0<=lo<=hi<=n
    	if ( lo == hi ) return 0; //出于效率考虑，单独处理退化情况
   	while ( hi < _size ) _elem[ lo++ ] = _elem[ hi++ ]; //后缀[hi,n)前移，自前向后的前移操作
    	_size = lo; shrink(); //更新规模，lo = _size之后的内容无需清零；如必要，则缩容 
   	return hi - lo; //返回被删除元素的数目
   }
   ```

   > 对于单元素删除操作：我们使用区间删除，将单元素视作区间的特例：`[r]=[r,r+1)`。所以可以重载`remove(r,r+1)`方法为`remove(r)`。
   >
   > 若反过来，通过反复调用单元素删除`remove(delElement)`的操作，而实现删除某个区间的元素，会导致整体$O(n^2)$的时间复杂度。每次循环耗时，正比于删除区间的后缀长度$n-hi=O(n)$ 而循环次数等于区间宽度 $hi - lo = O(n)$

3. 查找操作
   1. 该方法的时间复杂度和输入的数据有很大的关系，被称为输入敏感（input-sensitive）：最好O(1)，最差O(n)
   ```c++
   template <typename T> Rank Vector<T>::find( T const & e, Rank lo, Rank hi ) const { //0 <= lo < hi <= _size， //O(hi - lo) = O(n)
    	while ( (lo < hi--) && (e != _elem[hi]) ); //逆向查找
   	return hi; //返回值小于lo即意味着失败；否则即命中者的秩（有多个时，返回最大者）
   }
   ```

4. 唯一化

   1. 思想：当有新元素时，先查找其前驱是否有重复的元素（使用`find`函数），若有，则不放入向量中：若没有，则放入向量中。
   2. 代码实现：

      ```c++
      template <typename T> Rank Vector<T>::dedup() { //剔除相等的元素
      	Rank oldSize = _size;
      	for ( Rank i = 1; i < _size;  )
      	if ( -1 == find( _elem[i], 0, i ) ) //O(i)
      		i++;
      	else remove(i); //O(_size - i)
      	return oldSize - _size;
      } //O(n^2)：对于每一个e，只要find()不是最坏情况（查找成功），则remove()必执行
      ```
   3. 算法的证明：正确性：可以使用数学归纳法证明唯一化；单调性：while循环使得当前元素后缀长度单调下降，且迟早会减至0.
   4. 时间复杂度：主要是find()和remove()操作，每一次循环至多执行n次前述操作，while循环最多为n次，故其时间复杂度是$O(n^2)$的。
   5. 优化：通过`uniquify`方法，至多可以降至$O(n \log n)$.

5. 遍历`traverse()`
   1. 利用指针的机制，只读或做局部的修改。

   2. 利用函数对象的机制，可以进行全局操作。（推荐）

   3. 实现

      ```c++
      //先实现一个可使单个T类型元素加一的类（结构）
      template <typename T> //假设T可直接递增或已重载操作符“++”
       struct Increase{ //函数对象：通过重载操作符“()”实现
           virtual void operator()( T & e ) { e++; } }; //加一
      //再将其作为参数传递给遍历算法
      template <typename T> void increase( Vector<T> & V ){ 
          V.traverse( Increase<T>() ); } //即可以之作为基本操作，遍历向
      ```