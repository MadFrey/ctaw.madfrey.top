---
description: 记录一下学python时的一些零碎的笔记
---

# python

## 1.名称与环境

编程语言的一个要素就是使用名称来引用计算对象，如果一个值被赋予了名称，我们说名称绑定到了值上面。

在 Python 中，我们可以使用赋值语句建立新的绑定，`=` 左边是名称，右边是值。

赋值语句可以为现有函数赋予新名称。

```python
>>> f = max
>>> f
<built-in function max>
>>> f(2, 3, 4)
4
```

在 Python 中，名称通常被称为“变量名 variable names”或“变量 variables”，因为它们可以在执行程序的过程中与不同的值绑定。当一个变量通过赋值语句与一个新值绑定，它就不再绑定以前的值。你甚至可以将内置名称与新值绑定。

```python
>>> max = 5
>>> max
5
```

将 `max` 赋值为 5 后，名称 `max` 不再绑定函数，因此调用 `max(2, 3, 4)` 将导致错误。

执行赋值语句时，Python 会先求解 `=` 右侧的表达式，再将结果与左侧的名称绑定，所以可以在右侧表达式中引用一个已绑定的变量。

```
>>> x = 2
>>> x = x + 1
>>> x
3
```

还可以在单个语句中为多个变量分配值，左右都用逗号隔开。

```
>>> area, circumference = pi * radius * radius, 2 * pi * radius
>>> area
314.1592653589793
>>> circumference
62.83185307179586
```

对于多重赋值，所有 `=` 右边的表达式都会先求值，然后再与左边的名称绑定。在这个规则下，我们可以在单个语句内交换两个变量的值。

```python
>>> x, y = 3, 4.5
>>> y, x = x, y
>>> x
4.5
>>> y
3
```



## 2.非纯函数 print

**纯函数（Pure functions）**：函数有一些输入（参数）并返回一些输出（调用返回结果）

**非纯函数（Non-pure functions）**：除了返回值外，调用一个非纯函数还会产生其他改变解释器和计算机的状态的副作用（side effect）。一个常见的副作用就是使用 `print` 函数产生（非返回值的）额外输出。

```python
>>> print(1, 2, 3)
1 2 3
```

虽然  `print`  和  `abs`  在这些例子中看起来很相似，但它们的工作方式基本不同。`print` 返回的值始终为 `None`，这是一个不代表任何内容的特殊 Python 值。而交互式 Python 解释器并不会自动打印 `None` 值，所以 `print` 函数的额外输出就是它的副作用。

下面这个调用 `print` 的嵌套表达式就展示了非纯函数的特征。

```python
>>> print(print(1), print(2))
1
2
None None
```



## 3.选择名称

名称的可修改性并不意味着形式参数名称不重要。相反，精心选择的函数和参数名称对于程序的可解释性至关重要！

以下指南改编自 [Python 代码风格指南](http://www.python.org/dev/peps/pep-0008), 它可以作为所有（非叛逆的）Python 程序员的指南

1. 函数名是小写的，单词之间用下划线分隔。鼓励使用描述性名称。
2. 函数名称通常反映解释器应用于参数的操作（例如， `print, add, square` ）或结果（例如， `max, abs, sum` ）。
3. 参数名称是小写的，单词之间用下划线分隔。首选单个词的名称。
4. 参数名称应该反映参数在函数中的作用，而不仅仅是允许的参数类型。
5. 当作用明确时，单字参数名称可以接受，但应避免使用 l（小写的 L）和 O（大写的 o）或 I（大写的 i）以避免与数字混淆。



## 4.参数默认值

在 Python 中，我们可以为函数的参数提供默认值。当调用该函数时，具有默认值的参数是可选的。如果未提供，则将默认值绑定到形参上。例如，如果程序通常用于计算“一摩尔”粒子的压力，则可以提供此值作为默认值：

```python
>>> def pressure(v, t, n=6.022e23):
        """计算理想气体的压力，单位为帕斯卡

        使用理想气体定律：http://en.wikipedia.org/wiki/Ideal_gas_law

        v -- 气体体积，单位为立方米
        t -- 绝对温度，单位为开尔文
        n -- 气体粒子，默认为一摩尔
        """
        k = 1.38e-23  # 玻尔兹曼常数
        return n * k * t / v
```

`=` 符号在此示例中表示两种不同的含义，具体取决于使用它的上下文。在 def 语句中，`=` 不执行赋值，而是指示调用 `pressure` 函数时使用的默认值。相比之下，函数体中对 `k` 的赋值语句中将名称 `k` 与玻尔兹曼常数的近似值进行了绑定。

```python
>>> pressure(1, 273.15)
2269.974834
>>> pressure(1, 273.15, 3 * 6.022e23)
6809.924502
```

`pressure` 函数的定义接收三个参数，但上面的第一个调用表达式中只提供了两个。在这种情况下，`n` 的值取自 `def` 语句中的默认值。如果提供了第三个参数，默认值将被忽略。



## 5.柯里化

我们可以使用高阶函数将一个接受多个参数的函数转换为一个函数链，每个函数接受一个参数。更具体地说，给定一个函数 `f(x, y)`，我们可以定义另一个函数 `g` 使得 `g(x)(y)` 等价于 `f(x, y)`。在这里，`g` 是一个高阶函数，它接受单个参数 `x` 并返回另一个接受单个参数 `y` 的函数。这种转换称为柯里化（Curring）。

例如，我们可以定义 `pow` 函数的柯里化版本：

```python
>>> def curried_pow(x):
        def h(y):
            return pow(x, y)
        return h
>>> curried_pow(2)(3)
8
```



## 6. Lambda 表达式

到目前为止，每当我们想要定义一个新函数时，都需要给它取一个名字。但是对于其他类型的表达式，我们不需要将中间值与名称相关联。也就是说，我们可以计算 `a * b + c * d` 而不必命名子表达式 `a*b` 或 `c*d` 或完整的表达式。在 Python 中，我们可以使用 lambda 表达式临时创建函数，这些表达式会计算为未命名的函数。一个 lambda 表达式的计算结果是一个函数，它仅有一个返回表达式作为主体。不允许使用赋值和控制语句。

```python
>>> s = lambda x: x * x
>>> s
<function <lambda> at 0xf3f490>
>>> s(12)
144
```



## 7.函数装饰器

Python 提供了一种特殊的语法来使用高阶函数作为执行 `def` 语句的一部分，称为装饰器（decorator）。最常见的例子也许就是 `trace`

```python
>>> def trace(fn):
        def wrapped(x):
            print('-> ', fn, '(', x, ')')
            return fn(x)
        return wrapped

>>> @trace
    def triple(x):
        return 3 * x

>>> triple(12)
->  <function triple at 0x102a39848> ( 12 )
36
```

`triple` 的 `def` 语句有一个注解（annotation） `@trace`，它会影响 `def` 执行的规则。和往常一样，函数 `triple` 被创建了。但是，名称 triple 不会绑定到这个函数上。相反，这个名称会被绑定到在新定义的 `triple` 函数调用 `trace` 后返回的函数值上。代码中，这个装饰器等价于:

```python
>>> def triple(x):
        return 3 * x
>>> triple = trace(triple)
```



## 8.条件语句

逻辑表达式具有相应的求值过程。而这些过程利用了这样一个理论 → 有时，逻辑表达式的真值可以在不对其所有子表达式求值的情况下确定，这一特性称为短路（short-circuiting）。

***

求解表达式 `<left> and <right>` 的步骤如下：

1. 求解子表达式  `<left>`。
2. 如果左边的结果为假值 v，则表达式的计算结果就是 v。
3. 否则，表达式的计算结果为子表达式 `<right>` 的值。

***

求解表达式  `<left> or <right>` 的步骤如下：

1. 求解子表达式  `<left>`。
2. 如果左边的结果为真值 v，则表达式的计算结果就是 v。
3. 否则，表达式的计算结果为子表达式 `<right>` 的值。

***

求解表达式  `not <exp>` 的步骤如下：

1. 求解  `<exp>`，如果结果为假值，则值为 `True` ，否则为 `False`。

***

这些值、规则和运算符为我们提供了一种组合比较结果的方法。执行比较并返回布尔值的函数通常以 `is` 开头，后面不跟下划线（例如 `isfinite, isdigit, isinstance` 等）



## 9.序列遍历

Python 的 for 语句可以通过直接遍历元素值来简化函数，而无需引入 index

```renpy
>>> def count(s, value):
        """统计序列「s」中「value」的出现次数。"""
        total = 0
        for elem in s:
            if elem == value:
                total = total + 1
        return total
>>> count(digits, 8)
2
```

for 语句由如下形式的单个子句组成

```python
for <name> in <expression>:
    <suite>
```

for 语句按以下过程执行：

1. 评估 header 中的 `<expression>` ，它必须产生一个 iterable 的值 （译者注：在这里可以近似理解为可遍历的值）
2. 对 iterable 值中的每个元素，按顺序
   1. 在当前帧中，将元素值赋值到 `<name>`
   2. 执行 `<suite>`

这个执行过程中引用 “iterable” 值。list 是一种序列，序列是 iterable 值，它们中的元素按顺序被考虑。Python 包括其他 iterable 值，但我们暂时聚焦于序列；术语 “iterable” 的通用定义在第 4 章的迭代器部分。

这个评估过程中的一个重要结果是 `<name>` 将在执行  for 语句后绑定到序列的最后一个元素。for 循环引入了另一种可以通过语句更新环境的方法。



**范围 (Ranges)**。范围是 Python 中的另一种内置序列类型，它表示一个整数范围。范围是用  `range` 创建的，它有两个整数参数：范围的起始数字、范围的末尾数字再加一。

```python
>>> range(1, 10)  # 包括 1，但不包括 10
range(1, 10)
```

借助 `range` 来调用 `list` 构造函数，可以按范围构造出一个列表，从而简单的检查元素。

```python
>>> list(range(5, 8))
[5, 6, 7]
```

如果只给出一个参数，参数被理解为是“范围的末尾数字加一”。从而生成一个从 0 开始，直到该参数前一个值的列表。

```python
>>> list(range(4))
[0, 1, 2, 3]
```

范围通常出现在 for 语句 header 中的表达式，以指定 `<suite>` 应执行的次数。如果 `<name>` 没有被用在 `<suite>`，一个惯用的使用方式是，使用下划线表示 `<name>`。

```python
>>> for _ in range(3):
        print('Go Bears!')

Go Bears!
Go Bears!
Go Bears!
```



## 10.序列处理

**列表推导式** (**List Comprehensions)**。许多序列操作可以被理解为：为序列中的每个元素运算一个固定表达式，并将结果值收集在结果序列中。在 Python 中，列表推导式是执行此类计算的表达式。

```python
>>> odds = [1, 3, 5, 7, 9]
>>> [x+1 for x in odds]
[2, 4, 6, 8, 10]
```

上面的  `for`  关键字不是  for  语句的一部分，而是列表推导式的一部分，因为它包含在方括号中。子表达式  `x+1`  依次通过绑定到  `odds`  中每个元素的  `x`  进行求值，并将结果值收集到一个列表中。

另一个常见的序列处理操作是选出满足条件的值来组成一个子集。列表推导式也可以表达这种模式，例如选择 `odds` 中所有可以整除 25 的元素。

```python
>>> [x for x in odds if 25 % x == 0]
[1, 5]
```

列表推导式的一般形式是：

```python
[<map expression> for <name> in <sequence expression> if <filter expression>
```

为了运算列表推导式，Python 首先评估  `<sequence expression>`，它必须返回一个 iterable 值。然后，每个元素依次绑定到  `<name>`，再运算 `<filter expression>`；如果产生一个真值，运算 `<map expression>`。最后 `<map expression>` 的值被收集到一个列表中。

**聚合 (Aggregation)**。序列处理中的第三种常见模式是将序列中的所有值聚合为一个值。内置函数  `sum`、 `min`  和  `max`  都是聚合函数的示例。



## 11.序列抽象

**成员资格 (Membership)**。值可以被测试是否属于列表。Python 有两个运算符  `in`  和  `not in` ，它们的计算结果为  True  或  False ，具体取决于元素是否出现在序列中。

```python
>>> digits
[1, 8, 2, 8]
>>> 2 in digits
True
>>> 1828 not in digits
True
```



## 12.字符串

字符串满足我们在本节开头介绍的序列的两个基本条件：具有长度、支持元素选择。

字符串中的元素也是字符串，但只有一个字符。一个字符可以是字母表中的任何单个字母、或标点符号、或其他符号。与其他编程语言不同，Python 没有单独的字符类型；任何文本都是字符串。单个字符的字符串的长度为 1。

```python
>>> city = 'Berkeley'
>>> len(city)
8
>>> city[3]
'k'
```

与列表一样，字符串也可以通过加法和乘法组合。

```python
>>> 'Berkeley' + ', CA'
'Berkeley, CA'
>>> 'Shabu ' * 2
'Shabu Shabu '
```

**成员资格 (Membership)**。字符串的行为不同于 Python 中的其他序列类型。字符串抽象不同于列表和范围的完整序列抽象。尤其是，成员运算符  `in`  应用于字符串时，与应用于序列时相比完全不同。它匹配子字符串而不是元素。

python

```python
>>> 'here' in "Where's Waldo?"
True
```

**多行文字 (Multiline Literals)**。字符串不限于一行。跨越多行的字符串文字可以用三重引号括起。我们已经在文档字符串中广泛使用了这种三重引号。

python

```python
>>> """The Zen of Python
claims, Readability counts.
Read more: import this."""
'The Zen of Python\nclaims, "Readability counts."\nRead more: import this.'
```

在上面的打印结果中， （读作“反斜杠 n”）表示换行。尽管它显示为两个字符（反斜杠和 “n” ），但出于长度和元素选择的目的，它被视为单个字符。

**字符串强制 (String Coercion)** 通过以对象值作为参数调用  `str`  构造函数，可以从 Python 中的任何对象创建字符串。在用各种类型的对象来构造描述性字符串时，字符串的这一特性是很有用的。

```python
>>> str(2) + ' is an element of ' + str(digits)
'2 is an element of [1, 8, 2, 8]'
```



