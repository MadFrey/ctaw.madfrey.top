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



## 13.序列对象

因为两个列表可能内容相同，但实际上是不同的列表，所以我们需要一种方法来测试两个对象是否是同一个。Python 包括两个比较运算符，称为 is 和 is not，它们测试两个表达式实际上是否为相同的对象。如果两个对象的当前值相等，则它们是相同的，并且对一个对象的任何更改都将始终反映在另一个对象中。身份是比相等更强大的条件

```python
>>> suits is nest[0]
True
>>> suits is ['heart', 'diamond', 'spade', 'club']
False
>>> suits == ['heart', 'diamond', 'spade', 'club']
True
```

最后两个比较说明了 is 和 == 之间的区别。前者检查身份，而后者检查内容的相等性。

可以使用列表构造函数来复制列表。从而对一个列表的更改不会影响另一个列表，除非它们共享相同的内存引用（即是否是同一个对象）。

```python
>>> nest = list(suits)  # 用与 suits 一样的元素新建一个列表
>>> nest[0] = suits     # 创建一个内嵌列表(引用) 
```

**列表推导式 (List comprehensions)**生成的结果列表不与 suits 共享任何内容，并且操作列表推导式不会改变 suits 。

与列表一样，元组具有有限长度并支持元素选择。列表中的一些方法也适用于元组，例如 count 和 index。

```
>>> code = ("up", "up", "down", "down") + ("left", "right") * 2
>>> len(code)
8
>>> code[3]
'down'
>>> code.count("down")
2
>>> code.index("left")
4
```

但是，操作列表内容的方法不适用于元组，因为元组是不可变的。

虽然无法更改元组中的元素，但可以更改元组中包含的可变元素的值（列表）。

元组会在多重赋值中被隐式地使用。将两个值绑到两个名称时，会创建一个双元素元组，然后将其解包。



## 14.字典

字典中的每个键最多只能有一个值。添加新的键值对和更改键的现有值都可以通过赋值语句来实现。

```
>>> numerals['I'] = 1
>>> numerals['L'] = 50
>>> numerals
{'I': 1, 'X': 10, 'L': 50, 'V': 5}
```

请注意， L 并未添加到上面输出的末尾。因为字典是键值对的无序集合。当我们打印字典时，键和值按某种顺序呈现，但作为该语言的使用者，我们无法预测该顺序是什么。多次运行程序时，顺序可能会发生变化。

字典类型还支持各种迭代整个字典内容的方法。方法 keys、values 和 items 都返回可迭代的值。

```
>>> sum(numerals.values())
66
```

可以通过调用 dict 构造函数将键值对列表转换为字典。

```
>>> dict([(3, 9), (4, 16), (5, 25)])
{3: 9, 4: 16, 5: 25}
```

但字典也确实有一些限制：

* 字典的键不能是或包含可变值。
* 对于给定的键，最多只能有一个对应的值。

字典实现的一个有效方法是 get，它返回键的值（如果键存在）或默认值。get 的参数是键和默认值。

```
>>> numerals.get('A', 0)
0
>>> numerals.get('V', 0)
5
```

字典也有类似于列表的推导式语法。键表达式和值表达式由冒号分隔。运算字典推导式会创建一个新的字典对象。

```
>>> {x: x*x for x in range(3,6)}
{3: 9, 4: 16, 5: 25}
```



## 15.局部状态

非局部语句 (nonlocal statement)。当我们调用 make\_withdraw 时，我们将 balance 绑定到初始金额。然后我们定义并返回一个局部函数 withdraw，它会在调用时更新并返回 balance 的值。

```
>>> def make_withdraw(balance):
        """返回一个每次调用都会减少余额的 withdraw 函数"""
        def withdraw(amount):
            nonlocal balance                 # 声明 balance 是非局部的
            if amount > balance:
                return 'Insufficient funds'
            balance = balance - amount       # 重新绑定
            return balance
        return withdraw
```

非局部语句声明：每当我们更改 balance 的绑定时**，绑定关系都会在已经绑定 balance 的第一个帧中更改**。回想一下，如果没有非局部语句，赋值语句将始终在当前环境的第一帧中绑定。非局部语句指示名称不会出现在第一个（局部）帧或最后一个（全局）帧，而是出现在其他地方。

**Python 特质 (Python Particulars)**。这种非局部赋值模式是具有高阶函数和词法作用域的编程语言的普遍特征。大多数其他语言根本不需要非局部语句。相反，非局部赋值通常是赋值语句的默认行为。

Python 在名称查找方面也有一个不常见的限制：在一个函数体内，一个名称的所有实例必须引用同一个帧。因此，Python 无法在非局部帧中查找名称的值，然后在局部帧中绑定相同的名称，因为同名名称将在同一函数的两个不同帧中被访问。此限制允许 Python 在执行函数体之前预先计算哪个帧包含每个名称。违反此限制时，会产生令人困惑的错误消息。如

```python
def make_withdraw(balance):
2	    def withdraw(amount):
3	        if amount > balance:
4	            return 'Insufficient funds'
5	        balance = balance - amount
6	        return balance
7	    return withdraw
8	
9	wd = make_withdraw(20)
10	wd(5)
```

UnboundLocalError: local variable 'balance' referenced before assignment

出现此 UnboundLocalError 是因为 balance 是在第 5 行中局部分配的，因此 Python 假定对 balance 的所有引用也必须出现在局部帧中。这个错误发生在第 5 行执行之前，这意味着 Python 在执行第 3 行之前以某种方式考虑了第 5 行。在我们研究解释器设计时，我们将看到在执行函数体之前预先计算有关函数体的实际情况是很常见的。此时，Python 的预处理限制了 balance 可能出现的帧，从而阻止了名称的查找。添加非局部语句可更正此错误。Python 2 中不存在非局部语句。



## 16.约束传递 (Propagating Constraints)

**使用约束系统 (Using the Constraint System)**。要使用约束系统执行上述温度计算，我们首先通过调用连接器 connector 创建两个命名连接器，摄氏度 celsius 和华氏度 fahrenheit。

```
>>> celsius = connector('Celsius')
>>> fahrenheit = connector('Fahrenheit')
```

然后，我们将这些连接器链接到一个反映上图的网络中。函数转换器 (converter) 组装网络中的各种连接器和约束。

```
>>> def converter(c, f):
        """用约束条件连接 c 到 f ，将摄氏度转换为华氏度."""
        u, v, w, x, y = [connector() for _ in range(5)]
        multiplier(c, w, u)
        multiplier(v, x, u)
        adder(v, y, f)
        constant(w, 9)
        constant(x, 5)
        constant(y, 32)
```

```
>>> converter(celsius, fahrenheit)
```

我们将使用消息传递系统来协调约束和连接器。约束是不包含局部状态本身的字典。它们对消息的响应是非纯函数，会更改它们约束的连接器。

**连接器是保存当前值并响应操纵该值的消息的字典**。约束不会直接更改连接器的值，而是通过发送消息来更改，以便连接器可以通知其他约束以响应更改。这样，一个连接器既代表了一个数字，同时也封装了连接器的行为。

我们可以发送给连接器的一条消息是设置它的值。在这里，我们（ “ user ” ）将 celsius 的值设置为 25。

```
>>> celsius['set_val']('user', 25)
Celsius = 25
Fahrenheit = 77.0
```

不仅 celsius 的值变为 25，而且它的值通过网络传播，因此 fahrenheit 的值也发生变化。打印这些更改是因为我们在构造它们时命名了这两个连接器。

现在我们可以尝试将 fahrenheit 度设置为一个新值，比如 212。

```
>>> fahrenheit['set_val']('user', 212)
Contradiction detected: 77.0 vs 212
```

连接器抱怨说它感觉到了一个矛盾：它的值为 77.0，而有人试图将它设置为 212。如果我们真的想用新值应用到网络，我们可以告诉 celsius 忘记它的旧值：

```
>>> celsius['forget']('user')
Celsius is forgotten
Fahrenheit is forgotten
```

连接器 celsius 发现最初设置其值的用户现在收回该值，因此 celsius 同意失去其值，并将这一事实通知网络的其余部分。这个信息最终传播到 fahrenheit，它现在发现它没有理由继续相信它自己的值是 77。因此，它也放弃了它的值。

现在 fahrenheit 没有值，我们可以将其设置为 212：

```
>>> fahrenheit['set_val']('user', 212)
Fahrenheit = 212
Celsius = 100.0
```

这个新值在通过网络传播时会迫使 celsius 的值变为 100。我们使用了完全相同的网络来计算给定 celsius 的 fahrenheit 和给定 fahrenheit 的 celsius。这种计算的非方向性是基于约束的系统的显着特征。

**实施约束系统 (Implementing the Constraint System)**。正如我们所见，连接器是将消息名称映射到函数和数据值的字典。我们将实施响应以下消息的连接器：

```
>>> connector ['set_val'](source, value)  """表示 source 在请求连接器将当前值设为 value"""
>>> connector ['has_val']()  """返回连接器是否已经具有值"""
>>> connector ['val']  """是连接器的当前值"""
>>> connector ['forget'](source)  """告诉连接器 source 请求遗忘它的值"""
>>> connector ['connect'](source)  """告诉连接器参与新的约束，即 source"""
```

约束也是字典，它通过两条消息从连接器接收信息：

```
>>> constraint['new_val']()  """表示与约束相连的某个连接器具有新的值。"""
>>> constraint['forget']()  """表示与约束相连的某个连接器遗忘了值。"""
```

当约束收到这些消息时，它们会将消息传播到其他连接器。



## 17.类的定义

`class` 语句可以创建自定义类，类体里面又包含多条子语句。类语句定义类名，类体包含一组语句来定义类的属性。

```
class <name>:
    <suite>
```

执行类语句，将创建一个新类，并在当前环境的第一帧中绑定 `<name>` 。然后执行类体里面的语句。在 `class` 的 `<suite>` 中 `def` 或赋值语句中绑定的任何名称都会创建或修改类的属性。

类通常通过操作类属性来进行设计，这些属性是与该类的每个实例关联的名称-值对。类通过定义一个初始化对象的方法来指定特定对象的实例属性。例如，初始化 `Account` 类的对象的一部分是为它分配一个 0 的起始余额。

`class` 语句中的 `<suite>` 包含 `def` 语句， `def` 语句为类的对象定义新方法。初始化对象的方法在 `Python` 中有一个特殊的名称 `__init__` (“ init ”的每一测都有两个下划线)，称为类的构造函数（constructor）

```
>>> class Account:
   		def __init__(self, account_holder):
            self.balance = 0
            self.holder = account_holder
```

`Account` 的 `__init__` 方法有两个形式参数。第一个 `self` 绑定到新创建的 `Account` 对象。第二个参数 `account_holder` 绑定到调用类进行实例化时传递给类的参数。

定义 `Account` 类后，我们可以实例化它。

```
>>> a = Account('Kirk')
```

上面的语句调用 `Account` 类创建一个新对象，这个对象是 `Account` 的一个实例，然后使用两个参数调用构造函数 `__init__` : 新创建的对象和字符串 “Kirk” 。一般来说，我们使用参数名称 `self` 作为构造函数的第一个参数，它会自动绑定到正在实例化的对象。几乎所有的 Python 代码都遵守这个规定。

现在，我们可以使用符号点来访问对象的 `balance` 和 `holder` 。

```
>>> a.balance
0
>>> a.holder
'Kirk'
```



## 18.点表达式

**点表达式**：代码片段 `spock_account.deposit` 称为点表达式。点表达式由表达式、点和名称组成：

```
<expression>.<name>
```

`<expression>` 可以是任何有效的 Python 表达式，但 `<name>` 必须是简单名称（而不是计算结果为名称的表达式）。点表达式的计算结果为作为 `<expression>` 值的对象的 `<name>` 的属性值。

内置函数 `getattr` 还按名称返回对象的属性。它是点表示法的函数等效物。使用 `getattr` ，我们可以使用字符串查找属性，就像我们对调度字典所做的那样。

```
>>> getattr(spock_account, 'balance')
10
```

我们还可以测试应该对象是否具有 `hasattr` 的名命属性。

```
>>> hasattr(spock_account, 'deposit')
True
```

**方法和函数**：在对象上调用方法时，该对象将作为第一个参数隐式传递给该方法。也就是说，点左侧的 `<expression>` 值的对象将自动作为第一个参数传递给点表达式右侧命名的方法。因此，对象绑定到参数 `self`。

我们可以通过对点表达式的返回值调用 `type` 来查看交互式解释器的差异。作为类的属性，方法只是一个函数，但作为实例的属性，它是一个绑定方法：

```
>>> type(Account.deposit)
<class 'Function'>
>>> type(spock_account.deposit)
<class 'method'>
```

这两个结果的区别仅在于第一个是参数为 `self` 和 `amount` 的标准双参数函数。第二种是单参数方法，调用方法时，名称 `self` 将自动绑定到名为 `spock_account` 的对象，而参数 `amount` 将绑定到传递给方法的参数。这两个值（无论是函数值还是绑定方法值）都与相同的 `deposit` 函数体相关联。

我们可以通过两种方式调用 `deposit` ：作为函数和作为绑定方法。在前一种情况下，我们必须显式地为 `self` 参数提供一个参数。在后一种情况下， `self` 参数会自动绑定。

```
>>> Account.deposit(spock_account, 1001)	# 函数 deposit 接受两个参数
1011
>>> spock_account.deposit(1000) 			# 方法 deposit 接受一个参数
2011
```

**命名约定**：类名通常使用 CapWords 约定（也称为 CamelCase，因为名称中间的大写字母看起来像驼峰）编写。方法名称遵循使用下划线分隔的小写单词命名函数的标准约定。

在某些情况下，有一些实例变量和方法与对象的维护和一致性相关，我们不希望对象的用户看到或使用。它们不是类定义的抽象的一部分，而是实现的一部分。Python 的约定规定，如果属性名称以下划线开头，则只能在类本身的方法中访问它，而不是用户访问。



## 19.类属性

仍然可以从类的任何实例访问此属性。

```
>>> spock_account = Account('Spock')
>>> kirk_account = Account('Kirk')
>>> spock_account.interest
0.02
>>> kirk_account.interest
0.02
```

类属性的赋值会改变类的所有实例的属性值。

```
>>> Account.interest = 0.04
>>> spock_account.interest
0.04
>>> kirk_account.interest
0.04
```

**属性名称**。我们已经在对象系统中引入了足够的复杂性，以至于我们必须指定如何将名称解析为特定属性。毕竟，我们可以很容易地拥有一个同名的类属性和一个实例属性。

正如我们所看到的，点表达式由表达式、点和名称组成：

```
<expression> . <name>
```

计算点表达式：

1. 点表达式左侧的 `<expression>` ，生成点表达式的对象。
2. `<name>` 与该对象的实例属性匹配；如果存在具有该名称的属性，则返回属性值。
3. 如果实例属性中没有 `<name>` ，则在类中查找 `<name>`，生成类属性。
4. 除非它是函数，否则返回属性值。如果是函数，则返回该名称绑定的方法。

**属性赋值**。所有左侧包含点表达式的赋值语句都会影响该点表达式对象的属性。如果对象是实例，则赋值将设置实例属性。如果对象是类，则赋值将设置类属性。由于此规则，**对对象的属性的赋值不会影响其类的属性。**



## 20.继承

下面显示了 `CheckingAccount` 的完整实现。我们通过将计算结果为基类的表达式放在类名后面的括号中来指定继承。

```
>>> class CheckingAccount(Account):
    	  """从账号取钱会扣出手续费的账号"""
          withdraw_charge = 1
          interest = 0.01
          def withdraw(self, amount):
                return Account.withdraw(self, amount + self.withdraw_charge)
```

在这里，我们介绍一个 `CheckingAccount` 类的类属性 `withdraw_charge` 。我们为 `CheckingAccount` 的 `interest` 属性分配一个较低的值。我们还定义了一个新的 `withdraw` 方法来覆盖 `Account` 类中定义的行为。由于类套件中没有其他语句，所有其他行为都继承自基类 `Account` 。

在类中查找名称。

1. 如果它命名在指定类中的属性，则返回属性值。
2. 否则，在该类的父类中查找该名称的属性。



## 21.多继承

继承排序问题没有正确的解决方案，因为在某些情况下，我们可能更愿意将某些继承类置于其他类之上。但是，任何支持多重继承的编程语言都必须以一致的方式选择某些排序，以便该语言的用户可以预测其程序的行为。

进一步阅读。Python 使用称为 C3 方法解析排序的递归算法解析此名称。可以在所有类上使用 `mro` 方法查询任何类的方法解析顺序。



## 22.对象抽象

对象抽象的一个核心概念就是泛型函数，这种函数能够接受多种不同类型的值。我们将思考三种不同的用于实现泛型函数的技术：共享接口，类型派发和类型强制转换。在建立这些概念的过程中，我们也会发现一些特性，这些特性支持 Python 对象系统创泛型函数。



## 23.字符串转换

Python 规定所有的对象都应该生成两个不同的字符串表示：一种是人类可读的文本，另一种是 Python 可解释的表示式。字符串的构造函数，即 `str`，返回一个人类可读的字符串。如果可能，`repr` 函数返回一个 Python 可解释的表达式，该表达式的求值结果与原对象相同。

对于表达式的值调用 `repr` 的结果就是 Python 在交互式会话中所打印的内容。

```
>>> 12e12
12000000000000.0
>>> print(repr(12e12))
12000000000000.0
```

在一些情况下，不存在对原始值的字符串表示时，Python 通常生成一个被尖括号包围的描述。

```
>>> repr(min)
'<built-in function min>'
```

`str` 构造器通常与 `repr` 行为一致，但是在某些情况下它会提供一个更容易解释的文本表示。例如，我们可以看到 `str` 和 `repr` 对于日期对象的不同展示。

```
>>> from datetime import date
>>> tues = date(2011, 9, 12)
>>> repr(tues)
'datetime.date(2011, 9, 12)'
>>> str(tues)
'2011-09-12'
```

定义 `repr` 函数带来了一个新的挑战：我们想要它正确地应用于所有的数据类型，即使是那些实现 `repr` 时还不存在的类型。我们希望它是一个通用的或者多态（ polymorphic ）的函数，可以被应用于数据的多种（多）不同形式（态）。

在这情况下，对象系统提供了一种优雅的解决方案：`repr` 函数总是在其参数值上调用一个名为 `__repr__` 的方法。

```
>>> tues.__repr__()
'datetime.date(2011, 9, 12)'
```



## 24.专用方法

在 Python 中，某些特殊名称会在特殊情况下被 Python 解释器调用。例如，类的 `__init__` 方法会在对象被创建时自动调用。`__str__` 方法会在打印时自动调用，`__repr__` 方法会在交互式环境显示其值的时候自动调用。

在 Python 中有一些为其他行为而准备的特殊名称。下面介绍其中某些常用的。

真值（ True ）和假值（ False ）。我们之前已经看到，数字类型在 Python 中拥有真值：更准确地说，0 是一个假值而其他所有数字都是真值。实际上 Python 中的所有对象都拥有真假值。默认情况下，用户定义类的对象被认为是真值，但是专门的 `__bool__` 方法可以用于覆盖这种行为。如果一个对象定义了 `__bool__` 方法，那么 Python 就会调用这个方法来确定它的真假值。

举一个例子，假设我们想让一个只有 0 存款的账号为假值。我们可以为 `Account` 添加一个 `__bool__` 方法来实现这种行为。

```
>>> Account.__bool__ = lambda self: self.balance != 0
```

我们可以调用 `bool` 构造器来看一个对象的真假值，同时我们也可以在布尔上下文中使用任何对象。

```
>>> bool(Account('Jack'))
False
>>> if not Account('Jack'):
		print('Jack has nothing')
Jack has nothing
```

序列操作。我们已经知道使用 `len` 函数可以确定序列的长度。

```
>>> len('Go Bears!')
9
```

`len` 函数调用了它的参数的 `__len__` 方法来确定其长度。所有的内置序列类型都实现了这个方法。

```
>>> 'Go Bears!'.__len__()
9
```

如果序列没有提供 `__bool__` 方法，那么 Python 会使用序列的长度来确定其真假值。空的序列是假值，而非空序列是真值。

```
>>> bool('')
False
>>> bool([])
False
>>> bool('Go Bears!')
True
```

`__getitem__` 方法由元素选择操作符调用，但也可以直接调用它。

```
>>> 'Go Bears!'[3]
'B'
>>> 'Go Bears!'.__getitem__(3)
'B'
```

Python 还允许我们定义像函数一样可以被“调用的对象”，只要在对象中包含一个 `__call__` 方法。通过这个方法，我们可以定义一个行为像高阶函数的类。

举个例子，思考下面这个高阶函数，它返回一个函数，这个函数将一个常量值加到其参数上。

```
>>> def make_adder(n):
    	def adder(k):
            return n + k
        return adder

>>> add_three = make_adder(3)
>>> add_three(4)
7
```

我们可以创建一个 `Adder` 类，定义一个 `__call__` 方法来提供相同的功能。

```
>>> class Adder(object):
		def __init__(self, n):
			self.n = n
		def __call__(self, k):
			return self.n + k
>>> add_three_obj = Adder(3)
>>> add_three_obj(4)
7
```



算术运算。特定的方法也可以定义应用在用户定义的对象上的内置操作符的行为。为了提供这种通用性，Python 遵循特定的协议来应用每个操作符。例如，为了计算包含 `+` 操作符的表达式，Python 会检查操作符左右两侧的运算对象上是否有特定的方法。首先 Python 在左侧运算对象上检查其是否有 `__add__` 方法，然后在右侧运算对象上检查其是否有 `__radd__` 方法。如果找到了其中一个方法，就会将另一个运算对象的值作为它的参数来调用这个方法。



## 25.泛型函数

泛型函数是适用于不同类型的参数的方法或函数

类型派发。一种实现跨类型操作的方式是选择基于函数或方法的参数类型来选择相应的行为。类型派发的思想是写一个能够检查它所收到的参数的类型的函数，然后根据参数类型执行恰当的代码。

内置的函数 `isinstance` 接受一个对象或一个类。如果对象的类是所给的类或者继承自所给的类，它会返回一个真值。

```
>>> c = ComplexRI(1, 1)
>>> isinstance(c, ComplexRI)
True
>>> isinstance(c, Complex)
True
>>> isinstance(c, ComplexMA)
False
```

类型派发并不总是使用 `isinstance`，对于算术运算，我们会提供一个 `type_tag` 的属性给 `Ratinoal` 和 `Complex` 实例，这个属性拥有一个字符串值。当两个值 `x` 和 `y` 有相同的 `type_tag` 时，我们可以直接使用 `x.add(y)` 来结合它们，否则我们需要跨类型操作。

强制转换。在完全不相关的操作和完全不相关的类型的一般情况下，实现显式的跨类型操作即是会有些繁琐，但仍然是我们所能期待的最好的结果。幸运的是，通过利用类型系统中可能潜在额外的结构，有时我们可以做得更好。通常不同的数据类型并不是完全不相关的，可能存在一些方法将一种类型视为另一种类型。这个过程被称为强制转换。

即是有很多优势，但是强制转换也有潜在的缺点。例如，强制转换函数可能在应用时丢失信息。在我们的例子中，分数是准确的表示，但是当它被转换成复数时则变成了近似值。

有些编程语言拥有内置的强制转换系统。实际上，早期版本的 Python 的对象有一个专门的 `__coerce__` 方法。最终，内置强制转换系统的复杂性无法证明它的用处，所以它被移除了。相反，特定的运算符会根据需要对其参数进行强制转换。











