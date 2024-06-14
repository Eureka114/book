# 解读 C 的指针声明

## 使用英语解读指针声明

- 如果你想要去阅读C语言有关指针部分的书籍，你会发现这个部分的内容很难理解。这是因为C语言的声明语法是非常复杂的。在C语言中，声明的语法是从内向外读的，而不是从左向右读的。

- 比如，对于这个声明：``int (*pf)();``，我们应该这样读：``pf is a pointer to a function returning int``。这样读起来就会容易理解很多。

- 事实上，使用英语解读C语言的声明是一种很好的学习方法。我们采用的主要方法是：机械地向前读。

	> 1. 首先着眼于标识符（变量名或者函数名）。
	>
	> 2. 从距离标识符最近的地方开始，依照优先顺序解释派生类型（指 针、数组和函数）。优先顺序说明如下：
	>
	> 	1. 用于整理声明内容的`()`；
	> 	2. 用于表示数组的`[]`，用于表示函数的`()`；
	> 	3. 用于表示指针的`*`。
	>
	> 3. 解释完成派生类型，使用“of”、“to”、“returning”将它们连接起来。
	>
	> 4. 最后，追加数据类型修饰符（在左边，int、double 等）。
	>
	> 	
	>
	> - 数组元素个数和函数的参数属于类型的一部分。应该将它们作为附属于类型的属性进行解释。

- 如，对于`int (*func_p)(double)`就是`func_p is pointer to function(double) returning int.`的意思。

	|         C语言          |                        英语表达                         |                  中文表达                   |
	| :--------------------: | :-----------------------------------------------------: | :-----------------------------------------: |
	|      `int hoge;`       |                      hoge  is int                       |                  hoge是int                  |
	|    `int hoge[10];`     |            hoge  is array(元素个数10)of int             |        hoge是int的数组(元素个 数10)         |
	|   `int hoge[10][3];`   |  hoge  is array(元素个数10) of array(元素个数3) of int  | hoge是int数组(元素个数 3)的数组(元素个数10) |
	|    `int *hoge[10];`    |      hoge  is array元素:个数10) of pointer to int       |   hoge是指向int的指针的数 组(元素个数10)    |
	|   `int (*hoge) [3];`   |     hoge  is pointer to array(元素个数3) of double      |    hoge是指向int的数组(元素个数3)的指针     |
	|  `int  func(int a);`   |       func is  function(参数为int a) retuning int       |      func是返回int的函数(参数是int a)       |
	| `int (*func)(int a) ;` | func is pointer to function(参数 为int a) returning int | func是指向返回int的函数(参数为int a)的指针  |

## 类型名

- 在 C 中，除标识符以外，有时候还必须定义“类型”。 
- 具体来说，遇到以下情况需定义“类型”： 
	- 在强制转型运算符中
	- 类型作为 `sizeof` 运算符的操作数
- 比如，将强制转型运算符写成`(int *)`，其中，这里指定`int *`为类型名。



|       声明       |              声明的解释               |     类型名     |              类型名的解释              |
| :--------------: | :-----------------------------------: | :------------: | :------------------------------------: |
|   `int hoge;`    |               hoge是int               |     `int`      |                int类型                 |
|   `int *hoge;`   |          hoge是指向int的指针          |     `int*`     |           指向int的指针类型            |
| `double(*p)[3];` | p是指向double的数组(元 素个数3)的指针 | `double(*)[3]` | 指向double的数组(元素 个数3)的指针类型 |
| `void(*func)();` |     func是指向返回void函数 的指针     |  `void (*)()`  |       指向返回void函数的指针类型       |

最后两个例子中，括起星号的括弧(*)好像有点多余，但是一 旦去掉括弧，意思就完全不一样了。

> ` (double *[3])`是将` double *hoge[3]`的标识符去掉后形成的，所以 这个类型名被解释成“指向 `double`的指针的数组”。
