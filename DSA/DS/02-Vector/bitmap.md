# 位图

1. 概念：位图就相当于一个数组，不过每个元素仅仅存放0或1，只占一位（bit）。

   > In computing, a bitmap is a mapping from some domain (for example, a range of integers) to bits. It is also called a bit array or bitmap index.

2. 结构：

   ```c++
   class Bitmap{
       private:
       unsigned char *M;//M for array(byte): M[0,N)
       int N,_size;
       public:
       Bitmap(int n=8){//B for Bit: B[0,n)
           M=new unsigned char[N=(n+7)/8];//ceil
           memset(M,0,N);
       }
       ~Bitmap(){
           delete [] M; M=NULL;
       }
       bool test(int k){expand(k);return M[k>>3] & (0x80>>(k & 0x07));}//1
       void set(int k){if(test(k)) return; expand(k); _size++; M[k>>3] |= (0x80>>(k & 0x07));}//2
       void clear(int k){if(!test(k)) return; expand(k); _size--; M[k>>3] &= ~(0x80>>(k & 0x07));}//3
   };
   ```

   

3. 解释：

   1. 对于`test`函数：先找到待查询的`k`号元素所在的字节`(k/8)`，然后再找到其具体的偏移量`(k%8)，或(k&0x07)`，然后生成`mask(掩码)`，满足在一个字节内部，只有相对应的位是`1`，剩下的位都是`0`。最后，将找到的`M[k>>3]`和这个掩码去做按位与操作，就能判断该值是否为1。`bit_mask`具体地求解过程：先生成一个最高位是1，其余位都是0的字节作为初始的掩码，然后对该掩码进行按位与操作，即可得到上述掩码。
   2. 对于`set`和`clear`函数，只需要改变运算符号就可以实现了。

4. 应用：

   1. 小集合+大数据

   2. 素数：使用`Bitmap`类和其中的`test(), set()`函数

   3. [O(1)快速初始化？带你搞懂J.Hopcroft 校验环！ (qq.com)](https://mp.weixin.qq.com/s?__biz=Mzg2NTY3NzIxMQ==&mid=2247484708&idx=1&sn=32feaf448af825d2eb298f5a69a4b274)

