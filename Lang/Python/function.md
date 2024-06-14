# Python函数
## Python函数基础

### 函数定义

Python使用`def`关键字定义函数，语法如下：
```python
def function_name(parameters):
    """docstring"""
    statement(s)
```
- `function_name`：函数名
- `parameters`：参数列表
- `docstring`：函数文档字符串
- `statement(s)`：函数体
- 函数体中的语句块必须缩进，通常使用4个空格缩进。
- 函数体中的`return`语句用于返回函数结果，如果没有`return`语句，函数返回`None`。

### 空函数

在Python中，可以使用`pass`语句作为占位符，表示空函数，语法如下：
```python
def function_name(parameters):
    pass
```
`pass`语句什么也不做，只是占位符，用于保持程序结构的完整性。

### 参数检查

调用函数时，如果参数个数不对，Python解释器会自动检查出来，并抛出`TypeError`；

而如果参数的类型不对，Python解释器就无法帮助我们进行检查。

如果想对参数类型作出检查，就可以使用内置函数`isinstance()`来实现，语法如下：
```python
def function_name(parameters):
    if not isinstance(parameters, (type1, type2)):
        raise TypeError('bad operand type')
```
`isinstance()`函数接受两个参数，第一个参数是待检查的对象，第二个参数是类型的元组。

### 返回多个值

在Python中，函数可以返回多个值，实际上是返回一个元组，语法如下：
```python
def function_name(parameters):
    return value1, value2
```
调用函数时，可以同时接收多个返回值，语法如下：
```python
result1, result2 = function_name(parameters)
```



## 函数定义详解

以下内容来自Python官方。

> 函数定义支持可变数量的参数。这里列出三种可以组合使用的形式。

### 必选实参和可选实参

必选实参和其他语言函数中的参数类似，而可选实参需要指定参数值，需要在参数名之前加上等号 (=) 指定。

在调用函数时，必须加上必选实参；可选实参可以用于添加可选的功能或自定义函数的行为，例如要重复打印的消息次数、要使用的语言环境等。

### 默认值参数

默认值参数是最常见的形式。例如：
```python
def ask_ok(prompt, retries=4, reminder='Please try again!'):
    while True:
        ok = input(prompt)
        if ok in ('y', 'ye', 'yes'):
            return True
        if ok in ('n', 'no', 'nop', 'nope'):
            return False
        retries = retries - 1
        if retries < 0:
            raise ValueError('invalid user response')
        print(reminder)
```
该函数可以使用如下方式进行调用：

> - 只给出必选实参：`ask_ok('Do you really want to quit?')`
> - 给出一个可选实参：`ask_ok('OK to overwrite the file?', 2)`
> - 给出所有实参：`ask_ok('OK to overwrite the file?', 2, 'Come on, only yes or no!')`

注意：

- 和其他语言一样，默认值只会在函数的定义范围内求值（局部变量）

- 默认值只会计算一次，而默认值为列表、字典或类实例等可变对象时，会产生与该规则不同的结果。
  
```python
def f(a, L=[]):
    L.append(a)
    return L
print(f(1))
print(f(2))
print(f(3))
```
输出结果为：
```
[1]
[1, 2]
[1, 2, 3]
```
不想共享默认值时，可以使用`None`作为默认值，然后在函数内部进行判断。
```python
def f(a, L=None):
    if L is None:
        L = []
    L.append(a)
    return L
```

### 关键字参数

`kwarg=value` 形式的关键字参数也可以用于调用函数。函数示例如下：

```python
def parrot(voltage, state='a stiff', action='voom', type='Norwegian Blue'):
    print("-- This parrot wouldn't", action, end=' ')
    print("if you put", voltage, "volts through it.")
    print("-- Lovely plumage, the", type)
    print("-- It's", state, "!")
```

其中，下面的调用方式有效或无效：

```python
parrot(1000)  # 1 positional argument
parrot(voltage=1000)  # 1 keyword argument
parrot(voltage=1000000, action='VOOOOOM')  # 2 keyword arguments
parrot(action='VOOOOOM', voltage=1000000)  # 2 keyword arguments
parrot('a million', 'bereft of life', 'jump')  # 3 positional arguments
parrot('a thousand', state='pushing up the daisies')  # 1 positional, 1 keyword
# parrot(voltage=5.0, 'dead')  # non-keyword argument after a keyword argument
# parrot(110, voltage=220)  # duplicate value for the same argument 不能对同一个参数多次赋值
# parrot(actor='John Cleese')  # unknown keyword argument
```

> 位置参数和关键字参数的区别：
>
> - 位置参数(Positional Argument)：位置参数是按照参数的顺序进行传递的，调用函数时，必须按照函数定义的参数顺序传递参数。
>
> - 关键字参数(Keyword Argument)：关键字参数是按照参数名进行传递的，调用函数时，可以不按照函数定义的参数顺序传递参数，只需要指定参数名即可。
>
> 在函数中，位置参数、关键字参数和必选实参、可选实参没有必然的联系。
>
> 但是，关键字参数必须跟在位置参数后面，且所有传递的关键字参数都必须匹配一个函数接受的参数；不能对同一个参数多次赋值。

### 接收元组和字典

最后一个形参为 `**name` 形式时，接收一个字典，该字典包含与函数中已定义形参对应之外的所有关键字参数。`**name` 形参可以与 `*name` 形参（下一小节介绍）组合使用（`*name` 必须在 `**name` 前面）， `*name` 形参接收一个元组，该元组包含形参列表之外的位置参数。

```python
def cheeseshop(kind, *arguments, **keywords):
    print("-- Do you have any", kind, "?")
    print("-- I'm sorry, we're all out of", kind)
    for arg in arguments:
        print(arg)
    print("-" * 40)
    for kw in keywords:
        print(kw, ":", keywords[kw])
```

调用如下：

```python
cheeseshop("Limburger", "It's very runny, sir.",
           "It's really very, VERY runny, sir.",
           shopkeeper="Michael Palin",
           client="John Cleese",
           sketch="Cheese Shop Sketch")
```

> Python是如何判断元组和词典的分界线的？
>
> 一般来说，在Python中，`=`是不允许出现在元组中的（如果出现也是以字符而不是以运算符的身份出现），而词典的格式就是`key=value`，必须出现等号，所以，它们的分界线就出现在此处。

### 特殊参数

默认情况下，参数可以按位置或显式关键字传递给 Python 函数。为了让代码易读、高效，最好限制参数的传递方式，这样，开发者只需查看函数定义，即可确定参数项是仅按位置、按位置或关键字，还是仅按关键字传递。

```python
def f(pos1, pos2, /, pos_or_kwd, *, kwd1, kwd2):
      -----------    ----------     ----------
        |             |                  |
        |        Positional or keyword   |
        |                                - Keyword only
         -- Positional only
```

如果没`/`和`*`符号时，参数可以按位置或关键字传递给函数。

**仅限位置**形参应放在 `/` （正斜杠）前。`/` 后可以是**位置或关键字**或**仅限关键字**形参。
同理，`*`前可以是**仅限位置**形参或**仅限关键字**形参，而`*` 后是**仅限关键字**形参。

下面的函数定义中，`kwds` 把 `name` 当作键，因此，可能与位置参数 `name` 产生潜在冲突：

```python
def foo(name, **kwds):
    return 'name' in kwds
```

调用该函数不可能返回 `True`，因为关键字 `'name'` 总与第一个形参绑定。例如：

```python
>>> foo(1, **{'name': 2})
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: foo() got multiple values for argument 'name'
>>>
```

加上 `/` （仅限位置参数）后，就可以了。此时，函数定义把 `name` 当作位置参数，`'name'` 也可以作为关键字参数的键：

```python
def foo(name, /, **kwds):
    return 'name' in kwds

>>> foo(1, **{'name': 2})
True
```

换句话说，仅限位置形参的名称可以在 `**kwds` 中使用，而不产生歧义。

> 以下用例决定哪些形参可以用于函数定义：
>
> ```python
> def f(pos1, pos2, /, pos_or_kwd, *, kwd1, kwd2):
> ```
>
> 说明：
>
> - 使用仅限位置形参，可以让用户无法使用形参名。形参名没有实际意义时，强制调用函数的实参顺序时，或同时接收位置形参和关键字时，这种方式很有用。
> - 当形参名有实际意义，且显式名称可以让函数定义更易理解时，阻止用户依赖传递实参的位置时，才使用关键字。
> - 对于 API，使用仅限位置形参，可以防止未来修改形参名时造成破坏性的 API 变动。

### 任意实参列表

调用函数时，使用任意数量的实参是最少见的选项。这些实参包含在元组中（详见 [元组和序列](https://docs.python.org/zh-cn/3.9/tutorial/datastructures.html#tut-tuples) ）。在可变数量的实参之前，可能有若干个普通参数：

```
def write_multiple_items(file, separator, *args):
    file.write(separator.join(args))
```

`variadic` 参数用于采集传递给函数的所有剩余参数，因此，它们通常在形参列表的末尾。`*args` 形参后的任何形式参数只能是仅限关键字参数，即只能用作关键字参数，不能用作位置参数：

```python
>>> def concat(*args, sep="/"):
...     return sep.join(args)
...
>>> concat("earth", "mars", "venus")
'earth/mars/venus'
>>> concat("earth", "mars", "venus", sep=".")
'earth.mars.venus'
```

### 解包实参列表

函数调用要求独立的位置参数，但实参在列表或元组里时，要执行相反的操作。

```python
>>> list(range(3, 6))            # normal call with separate arguments
[3, 4, 5]
>>> args = [3, 6]
>>> list(range(*args))            # call with arguments unpacked from a list
[3, 4, 5]
```

同样，字典可以用 `**` 操作符传递关键字参数：

```python
>>> def parrot(voltage, state='a stiff', action='voom'):
...     print("-- This parrot wouldn't", action, end=' ')
...     print("if you put", voltage, "volts through it.", end=' ')
...     print("E's", state, "!")
...
>>> d = {"voltage": "four million", "state": "bleedin' demised", "action": "VOOM"}
>>> parrot(**d)
-- This parrot wouldn't VOOM if you put four million volts through it. E's bleedin' demised !
```

### Lambda 表达式

用于创建小巧的匿名函数。`lambda a, b: a+b` 函数返回两个参数的和。Lambda 函数可用于任何需要函数对象的地方。在语法上，匿名函数只能是单个表达式。在语义上，它只是常规函数定义的语法糖。与嵌套函数定义一样，lambda 函数可以引用包含作用域中的变量：

```python
>>> def make_incrementor(n):
...     return lambda x: x + n
...
>>> f = make_incrementor(42)
>>> f(0)
42
>>> f(1)
43
```

上例用 lambda 表达式返回函数。还可以把匿名函数用作传递的实参：

\>>>

```python
>>> pairs = [(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
>>> pairs.sort(key=lambda pair: pair[1])
>>> pairs
[(4, 'four'), (1, 'one'), (3, 'three'), (2, 'two')]
```

### 文档字符串

以下是文档字符串内容和格式的约定。

第一行应为对象用途的简短摘要。为保持简洁，不要在这里显式说明对象名或类型，因为可通过其他方式获取这些信息（除非该名称碰巧是描述函数操作的动词）。这一行应以大写字母开头，以句点结尾。

文档字符串为多行时，第二行应为空白行，在视觉上将摘要与其余描述分开。后面的行可包含若干段落，描述对象的调用约定、副作用等。

Python 解析器不会删除 Python 中多行字符串字面值的缩进，因此，文档处理工具应在必要时删除缩进。这项操作遵循以下约定：文档字符串第一行 *之后* 的第一个非空行决定了整个文档字符串的缩进量（第一行通常与字符串开头的引号相邻，其缩进在字符串中并不明显，因此，不能用第一行的缩进），然后，删除字符串中所有行开头处与此缩进“等价”的空白符。不能有比此缩进更少的行，但如果出现了缩进更少的行，应删除这些行的所有前导空白符。转化制表符后（通常为 8 个空格），应测试空白符的等效性。

下面是多行文档字符串的一个例子：

```python
>>> def my_function():
...     """Do nothing, but document it.
...
...     No, really, it doesn't do anything.
...     """
...     pass
...
>>> print(my_function.__doc__)
Do nothing, but document it.

    No, really, it doesn't do anything.
```

### 函数注解

[函数注解](https://docs.python.org/zh-cn/3.9/reference/compound_stmts.html#function) 是可选的用户自定义函数类型的元数据完整信息（详见 [**PEP 3107**](https://www.python.org/dev/peps/pep-3107) 和 [**PEP 484**](https://www.python.org/dev/peps/pep-0484) ）。

[标注](https://docs.python.org/zh-cn/3.9/glossary.html#term-function-annotation) 以字典的形式存放在函数的 `__annotations__` 属性中，并且不会影响函数的任何其他部分。 形参标注的定义方式是在形参名后加冒号，后面跟一个表达式，该表达式会被求值为标注的值。 返回值标注的定义方式是加组合符号 `->`，后面跟一个表达式，该标注位于形参列表和表示 [`def`](https://docs.python.org/zh-cn/3.9/reference/compound_stmts.html#def) 语句结束的冒号之间。 下面的示例有一个必须的参数，一个可选的关键字参数以及返回值都带有相应的标注:

```python
>>> def f(ham: str, eggs: str = 'eggs') -> str:
...     print("Annotations:", f.__annotations__)
...     print("Arguments:", ham, eggs)
...     return ham + ' and ' + eggs
...
>>> f('spam')
Annotations: {'ham': <class 'str'>, 'return': <class 'str'>, 'eggs': <class 'str'>}
Arguments: spam eggs
'spam and eggs'
```