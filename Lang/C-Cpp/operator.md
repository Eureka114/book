# C++内置运算符、优先级、关联性、运算符重载

## C++ 运算符的优先级和关联性表

下表显示 C++ 运算符的优先级和关联性（从最高优先级到最低优先级）。 优先级别编号相同的运算符具有等同的优先级别，除非由括号显式施加另一种关系。

| 运算符说明                                                   | 运算符           | 替代项 |
| :----------------------------------------------------------: | :--------------: | :----: |
| 第  1 组优先级，无关联性                       | 第  1 组优先级，无关联性 | 第  1 组优先级，无关联性 |
| [范围解析](https://learn.microsoft.com/zh-cn/cpp/cpp/scope-resolution-operator?view=msvc-170) | `::`               |        |
|                                  | 第  2 组优先级，从左到右关联 |  |
| [成员选择（对象或指针）](https://learn.microsoft.com/zh-cn/cpp/cpp/member-access-operators-dot-and?view=msvc-170) | `. 或 -> `         |        |
| [数组下标](https://learn.microsoft.com/zh-cn/cpp/cpp/subscript-operator?view=msvc-170) | `[]`              |        |
| [函数调用](https://learn.microsoft.com/zh-cn/cpp/cpp/function-call-operator-parens?view=msvc-170) | `()`               |        |
| [后缀递增](https://learn.microsoft.com/zh-cn/cpp/cpp/postfix-increment-and-decrement-operators-increment-and-decrement?view=msvc-170) | `++ `              |        |
| [后缀递减](https://learn.microsoft.com/zh-cn/cpp/cpp/postfix-increment-and-decrement-operators-increment-and-decrement?view=msvc-170) | `--`               |        |
| [类型名称](https://learn.microsoft.com/zh-cn/cpp/cpp/typeid-operator?view=msvc-170) | `typeid`          |        |
| [常量类型转换](https://learn.microsoft.com/zh-cn/cpp/cpp/const-cast-operator?view=msvc-170) | `const_cast`      |        |
| [动态类型转换](https://learn.microsoft.com/zh-cn/cpp/cpp/dynamic-cast-operator?view=msvc-170) | `dynamic_cast`    |        |
| [重新解释的类型转换](https://learn.microsoft.com/zh-cn/cpp/cpp/reinterpret-cast-operator?view=msvc-170) | `reinterpret_cast` |        |
| [静态类型转换](https://learn.microsoft.com/zh-cn/cpp/cpp/static-cast-operator?view=msvc-170) | `static_cast`     |        |
|                                  | 第  3 组优先级，从右到左关联 |  |
| [对象或类型的大小](https://learn.microsoft.com/zh-cn/cpp/cpp/sizeof-operator?view=msvc-170) | `sizeof`          |        |
| [前缀递增](https://learn.microsoft.com/zh-cn/cpp/cpp/prefix-increment-and-decrement-operators-increment-and-decrement?view=msvc-170) | `++`              |        |
| [前缀递减](https://learn.microsoft.com/zh-cn/cpp/cpp/prefix-increment-and-decrement-operators-increment-and-decrement?view=msvc-170) | `--`              |        |
| [二进制反码](https://learn.microsoft.com/zh-cn/cpp/cpp/one-s-complement-operator-tilde?view=msvc-170) | `~`               | `compl` |
| [逻辑“非”](https://learn.microsoft.com/zh-cn/cpp/cpp/logical-negation-operator-exclpt?view=msvc-170) | `!`               | `not`  |
| [一元求反](https://learn.microsoft.com/zh-cn/cpp/cpp/unary-plus-and-negation-operators-plus-and?view=msvc-170) | `-`               |        |
| [一元加](https://learn.microsoft.com/zh-cn/cpp/cpp/unary-plus-and-negation-operators-plus-and?view=msvc-170) | `+`               |        |
| [Address-of](https://learn.microsoft.com/zh-cn/cpp/cpp/address-of-operator-amp?view=msvc-170) | `&`               |        |
| [间接寻址](https://learn.microsoft.com/zh-cn/cpp/cpp/indirection-operator-star?view=msvc-170) | `*`               |        |
| [创建对象](https://learn.microsoft.com/zh-cn/cpp/cpp/new-operator-cpp?view=msvc-170) | `new`             |        |
| [销毁对象](https://learn.microsoft.com/zh-cn/cpp/cpp/delete-operator-cpp?view=msvc-170) | `delete`          |        |
| [强制转换](https://learn.microsoft.com/zh-cn/cpp/cpp/cast-operator-parens?view=msvc-170) | `()`              |        |
|                                  | 第  4 组优先级，从左到右关联 |  |
| [指向成员的指针（对象或指针）](https://learn.microsoft.com/zh-cn/cpp/cpp/pointer-to-member-operators-dot-star-and-star?view=msvc-170) | `.* 或 ->*`       |        |
|                                  | 第  5 组优先级，从左到右关联 |  |
| [乘法](https://learn.microsoft.com/zh-cn/cpp/cpp/multiplicative-operators-and-the-modulus-operator?view=msvc-170) | `*`               |        |
| [除法](https://learn.microsoft.com/zh-cn/cpp/cpp/multiplicative-operators-and-the-modulus-operator?view=msvc-170) | `/`               |        |
| [取模](https://learn.microsoft.com/zh-cn/cpp/cpp/multiplicative-operators-and-the-modulus-operator?view=msvc-170) | `%`               |        |
|                                  | 第  6 组优先级，从左到右关联 |  |
| [加法](https://learn.microsoft.com/zh-cn/cpp/cpp/additive-operators-plus-and?view=msvc-170) | `+`               |        |
| [减法](https://learn.microsoft.com/zh-cn/cpp/cpp/additive-operators-plus-and?view=msvc-170) | `-`               |        |
|                                  | 第  7 组优先级，从左到右关联 |  |
| [左移](https://learn.microsoft.com/zh-cn/cpp/cpp/left-shift-and-right-shift-operators-input-and-output?view=msvc-170) | `<<`              |        |
| [右移](https://learn.microsoft.com/zh-cn/cpp/cpp/left-shift-and-right-shift-operators-input-and-output?view=msvc-170) | `>>`              |        |
|                                  | 第  8 组优先级，从左到右关联 |  |
| [小于](https://learn.microsoft.com/zh-cn/cpp/cpp/relational-operators-equal-and-equal?view=msvc-170) | `<`               |        |
| [大于](https://learn.microsoft.com/zh-cn/cpp/cpp/relational-operators-equal-and-equal?view=msvc-170) | `>`               |        |
| 小于或等于                                                   | `<=`             |        |
| 大于或等于                                                   | `>=`             |        |
|                                  | 第  9 组优先级，从左到右关联 |                          |
| [等式](https://learn.microsoft.com/zh-cn/cpp/cpp/equality-operators-equal-equal-and-exclpt-equal?view=msvc-170) | `==`              |        |
| [不相等](https://learn.microsoft.com/zh-cn/cpp/cpp/equality-operators-equal-equal-and-exclpt-equal?view=msvc-170) | `!=`             | `not_eq` |
|                                 | 第  10 组优先级，从左到右关联 |  |
| [按位“与”](https://learn.microsoft.com/zh-cn/cpp/cpp/bitwise-and-operator-amp?view=msvc-170) | `&`               | `bitand` |
|                                 | 第  11 组优先级，从左到右关联 |  |
| [按位“异或”](https://learn.microsoft.com/zh-cn/cpp/cpp/bitwise-exclusive-or-operator-hat?view=msvc-170) | `^`               | `xor`  |
|                                 | 第  12 组优先级，从左到右关联 |  |
| [按位“与或”](https://learn.microsoft.com/zh-cn/cpp/cpp/bitwise-inclusive-or-operator-pipe?view=msvc-170) | `\|`              | `bitor` |
|                                 | 第  13 组优先级，从左到右关联 |  |
| [逻辑与](https://learn.microsoft.com/zh-cn/cpp/cpp/logical-and-operator-amp-amp?view=msvc-170) | `&&`              | `and`  |
|                                 | 第  14 组优先级，从左到右关联 |  |
| [逻辑或](https://learn.microsoft.com/zh-cn/cpp/cpp/logical-or-operator-pipe-pipe?view=msvc-170) | `||`           | `or`   |
|                                 | 第  15 组优先级，从右到左关联 |  |
| [条件](https://learn.microsoft.com/zh-cn/cpp/cpp/conditional-operator-q?view=msvc-170) | `? :`             |        |
| [转让](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `=`               |        |
| [乘法赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `*=`              |        |
| [除法赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `/=`              |        |
| [取模赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `%=`              |        |
| [加法赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `+=`              |        |
| [减法赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `-=`              |        |
| [左移赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `<<=`             |        |
| [右移赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `>>=`             |        |
| [按位“与”赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `&=`              | `and_eq` |
| [按位“与或”赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `|=`             | `or_eq` |
| [按位“异或”赋值](https://learn.microsoft.com/zh-cn/cpp/cpp/assignment-operators?view=msvc-170) | `^=`              | `xor_eq` |
| [引发表达式](https://learn.microsoft.com/zh-cn/cpp/cpp/try-throw-and-catch-statements-cpp?view=msvc-170) | `throw`           |        |
|                                 | 第  16 组优先级，从左到右关联 |  |
| [Comma](https://learn.microsoft.com/zh-cn/cpp/cpp/comma-operator?view=msvc-170) | `，`              |        |

## C++中的重载运算符

1. 为了使用自定义类型的运算符，我们需要重载运算符。

2. 重载的运算符是带有特殊名称的函数，函数名是由关键字 operator 和其后要重载的运算符符号构成的。与其他函数一样，重载运算符有一个返回类型和一个参数列表。

3. 重载运算符可以被定义为非成员函数或者为类成员函数。例如：

	```c++
	Box operator+(const Box& anotherBox){...}//成员函数
	Box operator+(const Box& bx1, const Box& bx2){...}//非成员函数
	```

	

4. 可重载运算符列表：

|    `，`    |         Comma          | 二进制 |
| :--------: | :--------------------: | :----: |
|    `!`     |         逻辑非         |  一元  |
|    `!=`    |         不相等         | 二进制 |
|    `%`     |          取模          | 二进制 |
|    `%=`    |        取模赋值        | 二进制 |
|    `&`     |          位与          | 二进制 |
|    `&`     |       Address-of       |  一元  |
|    `&&`    |         逻辑与         | 二进制 |
|    `&=`    |      按位“与”赋值      | 二进制 |
|   `( )`    |        函数调用        |   —    |
|   `( )`    |       转换运算符       |  一元  |
|    `*`     |          乘法          | 二进制 |
|    `*`     |      指针取消引用      |  一元  |
|    `*=`    |        乘法赋值        | 二进制 |
|    `+`     |        附加内容        | 二进制 |
|    `+`     |         一元加         |  一元  |
|    `++`    |         递增 1         |  一元  |
|    `+=`    |        加法赋值        | 二进制 |
|    `-`     |           减           | 二进制 |
|    `-`     |        一元求反        |  一元  |
|    `--`    |         递减 1         |  一元  |
|    `-=`    |        减法赋值        | 二进制 |
|    `->`    |        成员选择        | 二进制 |
|   `->*`    | 指向成员的指针选定内容 | 二进制 |
|    `/`     |          分部          | 二进制 |
|    `/=`    |        除法赋值        | 二进制 |
|    `<`     |          小于          | 二进制 |
|    `<<`    |          左移          | 二进制 |
|   `<<=`    |        左移赋值        | 二进制 |
|    `<=`    |       小于或等于       | 二进制 |
|    `=`     |          赋值          | 二进制 |
|    `==`    |          等式          | 二进制 |
|    `>`     |          大于          | 二进制 |
|    `>=`    |       大于或等于       | 二进制 |
|    `>>`    |          右移          | 二进制 |
|   `>>=`    |        右移赋值        | 二进制 |
|   `[ ]`    |        数组下标        |   —    |
|    `^`     |          异或          | 二进制 |
|    `^=`    |        异或赋值        | 二进制 |
|    `|`     |          位或          | 二进制 |
|    `|=`    |     按位“与或”赋值     | 二进制 |
|    `||`    |         逻辑或         | 二进制 |
|    `~`     |       二进制反码       |  一元  |
|  `delete`  |          删除          |   —    |
|   `new`    |          新建          |   —    |
| 转换运算符 |       转换运算符       |  一元  |

5. 不可被重载的运算符列表：

|  `.`  |        成员选择        |
| :---: | :--------------------: |
| `.*`  | 指向成员的指针选定内容 |
| `::`  |        范围解析        |
| `? :` |        条件逻辑        |
|  `#`  |  预处理器转换为字符串  |
| `##`  |      预处理器串联      |