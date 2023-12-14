# C++

在 C++ 中初始化变量有 6 种基本方法：

```cpp
int a;         // no initializer (default initialization)
int b = 5;     // initializer after equals sign (copy initialization)
int c( 6 );    // initializer in parenthesis (direct initialization)

// List initialization methods (C++11) (preferred)
int d { 7 };   // initializer in braces (direct list initialization)
int e = { 8 }; // initializer in braces after equals sign (copy list initialization)
int f {};      // initializer is empty braces (value initialization)
```

当未提供初始化值（例如上面的变量a）时，这称为**默认初始化**。在大多数情况下，默认初始化会使变量具有不确定的值。



初始化多个变量

在上一节中，我们注意到可以通过用逗号分隔名称在单个语句中定义_相同类型的_多个变量：

```cpp
int a, b;
```

我们还注意到，最佳做法是完全避免这种语法。但是，由于您可能会遇到使用此样式的其他代码，因此，如果没有其他原因，除了强调您应该避免使用的一些原因之外，多讨论一下它仍然很有用。

您可以初始化在同一行上定义的多个变量：

```cpp
int a = 5, b = 6;          // copy initialization
int c( 7 ), d( 8 );        // direct initialization
int e { 9 }, f { 10 };     // direct brace initialization
int g = { 9 }, h = { 10 }; // copy brace initialization
int i {}, j {};            // value initialization
```



不幸的是，当程序员错误地尝试使用一个初始化语句初始化两个变量时，可能会发生一个常见的陷阱：

```cpp
int a, b = 5; // wrong (a is not initialized!)

int a = 5, b = 5; // correct
```

在 top 语句中，变量 “a” 将保持未初始化状态，编译器可能会也可能不会抱怨。如果没有，这是让程序间歇性崩溃或产生零星结果的好方法。我们稍后将详细讨论如果您使用未初始化的变量会发生什么。

记住这是错误的最好方法是考虑直接初始化或大括号初始化的情况：

```cpp
int a, b( 5 );
int c, d{ 5 };
```

由于括号或大括号通常位于变量名称的旁边，因此值 5 仅用于初始化变量 _b_ 和 _d_，而不是 _a_ 或 _c_，这似乎更清楚一些。



C++17 引入了该属性，它允许我们告诉编译器我们可以接受未使用的变量。编译器不会为此类变量生成未使用的变量警告。`[[maybe_unused]]`

以下程序不应生成任何警告/错误：

```cpp
int main()
{
    [[maybe_unused]] double pi { 3.14159 };
    [[maybe_unused]] double gravity { 9.8 };
    [[maybe_unused]] double phi { 1.61803 };

    // the above variables will not generate unused variable warnings

    return 0;
}
```

此外，编译器可能会在程序之外优化这些变量，因此它们不会影响性能。















