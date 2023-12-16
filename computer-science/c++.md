---
description: c++入门从此开始>_<
---

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



## iostream

### std：：cout 已缓冲

这个类比类似于发送到 std：：cout 的输出通常在 C++ 中处理。程序中的语句请求将输出发送到控制台。但是，该输出通常不会立即发送到控制台。相反，请求的输出“排成一列”，并存储在留出用于收集此类请求的内存区域（称为**缓冲区**）中。缓冲区会定期**刷新**，这意味着缓冲区中收集的所有数据都将传输到其目标（在本例中为控制台）。这也意味着，如果程序在刷新缓冲区之前崩溃、中止或暂停（例如出于调试目的），则不会显示缓冲区中仍在等待的任何输出。



### std：：endl 与 '\n'

使用 std：：endl 可能有点低效，因为它实际上执行了两项工作：它将光标移动到控制台的下一行，并刷新缓冲区。将文本写入控制台时，我们通常不需要刷新每行末尾的缓冲区。让系统定期进行自我冲洗会更有效（它被设计为有效地完成）。

因此，通常首选使用“\n”字符。“\n”字符将光标移动到控制台的下一行，但不请求刷新，因此它通常性能更好。“\n”字符也更简洁，因为它更短，可以嵌入到现有文本中。



### std::cin

`std::cin` is another predefined variable that is defined in the `iostream` library. Whereas `std::cout` prints data to the console using the insertion operator (`<<`), `std::cin` (which stands for “character input”) reads input from keyboard using the **extraction operator (`>>`)**. The input must be stored in a variable to be used.



## 回顾 main()

您现在拥有概念工具来理解该`main`函数的实际工作原理。当程序执行时，操作系统会调用函数`main`。然后执行跳转到 的顶部`main`。中的语句`main`按顺序执行。最后，`main`返回一个整数值（通常为`0`），然后程序终止。返回值`main`有时称为**状态代码**（有时也称为**退出代码**，或很少称为**返回代码**），因为它用于指示程序是否成功运行。

根据定义，状态代码表示`0`程序执行成功。

C++标准只定义了3种状态码的含义：0、EXIT\_SUCCESS和EXIT\_FAILURE。0和EXIT\_SUCCESS都表示程序执行成功。EXIT\_FAILURE 表示程序未成功执行。

EXIT\_SUCCESS 和 EXIT\_FAILURE 是 \<cstdlib> 标头中定义的预处理器宏：

```cpp
#include <cstdlib> // for EXIT_SUCCESS and EXIT_FAILURE

int main()
{
    return EXIT_SUCCESS;
}
```

如果要最大化可移植性，则应该仅使用 0 或 EXIT\_SUCCESS 来指示成功终止，或使用 EXIT\_FAILURE 来指示不成功终止。

> C++ disallows calling the `main()` function explicitly.
>
> As an aside…
>
> C does allow `main()` to be called explicitly, so some C++ compilers will allow this for compatibility reasons.

如果没有提供 return 语句，函数 main 将隐式返回 0

返回值函数必须通过 return 语句返回值的规则的唯一例外是 for function `main()`。如果没有提供 return 语句，该函数`main()`将隐式返回该值。`0`也就是说，最好的做法是显式地从 中返回一个值`main`，既可以表明您的意图，也可以与其他函数保持一致（如果未指定返回值，它将表现出未定义的行为）



函数只能返回单个值

返回值函数每次调用时只能将单个值返回给调用者。

请注意，return 语句中提供的值不必是文字 - 它可以是任何有效表达式的结果，包括变量，甚至是对另一个返回值的函数的调用。在`getValueFromUser()`上面的示例中，我们返回一个变量`input`，其中保存用户输入的数字。



## 什么是命名空间？

在命名空间内，所有名称必须是唯一的，否则将导致命名冲突。

命名空间通常用于对大型项目中的相关标识符进行分组，以帮助确保它们不会无意中与其他标识符发生冲突。例如，如果您将所有数学函数放在名为_math 的_命名空间中，那么您的数学函数不会与_数学_命名空间外的同名函数发生冲突。

### 全局命名空间

在 C++ 中，任何未在类、函数或命名空间内定义的名称都被视为隐式定义的命名空间的一部分，称为全局命名空间**（**有时也称为**全局作用域**）。

### std 命名空间

_当 C++ 最初设计时，C++ 标准库中的所有标识符（包括 std::cin 和 std::cout）都可以在没有std::_前缀的情况下使用（它们是全局命名空间的一部分）。但是，这意味着标准库中的任何标识符都可能与您为自己的标识符选择的任何名称（也在全局命名空间中定义）发生冲突。当您 #included 标准库中的新文件时，正在运行的代码可能会突然出现命名冲突。或者更糟糕的是，可以在某一版本的 C++ 下编译的程序可能无法在未来版本的 C++ 下编译，因为引入标准库的新标识符可能与已编写的代码发生命名冲突。因此，C++ 将标准库中的所有功能移至名为“std”（标准的缩写）的命名空间中。

事实证明，_std::cout_的名称实际上并不是_std::cout_。它实际上只是_cout_，而_std是标识符cout_所属的命名空间的名称。因为_cout_是在_std_命名空间中定义的，所以名称_cout_不会与我们在全局命名空间中创建的任何名为_cout的对象或函数冲突。_

类似地，当访问命名空间中定义的标识符（例如_std::cout_）时，您需要告诉编译器我们正在寻找命名空间（_std_）内定义的标识符。



## 预处理器

在编译之前，每个代码 (.cpp) 文件都会经历一个**预处理**阶段。**在此阶段，称为预处理器的**程序对代码文件的文本进行各种更改。预处理器实际上不会以任何方式修改原始代码文件——相反，预处理器所做的所有更改要么临时发生在内存中，要么使用临时文件。

> 从历史上看，预处理器是与编译器分开的程序，但在现代编译器中，预处理器可以直接构建到编译器本身中。

预处理器所做的大部分工作都是相当无趣的。例如，它删除注释，并确保每个代码文件以换行符结尾。然而，预处理器确实有一个非常重要的作用：它处理`#include`指令（我们稍后将详细讨论）。当预处理器完成对代码文件的处理时，结果称为**翻译单元**。该翻译单元随后由编译器编译。

### 预处理器指令

当预处理器运行时，它会扫描代码文件（从上到下），查找预处理器指令。**预处理器指令**（通常简称为_指令_）是以_#_符号开头并以换行符（不是分号）结尾的指令。这些指令告诉预处理器执行某些文本操作任务。请注意，预处理器不理解 C++ 语法——相反，指令有自己的语法（在某些情况下类似于 C++ 语法，而在其他情况下则不太相似）。



当_#include_文件时，预处理器会将 #include 指令替换为所包含文件的内容。然后对包含的内容进行预处理（这可能会导致递归地预处理额外的#include），然后对文件的其余部分进行预处理。

### 宏定义

_#define_指令可用于创建宏。在 C++ 中，**宏**是定义如何将输入文本转换为替换输出文本的规则。

宏有两种基本类型：_类对象宏_和_类函数宏_。

_类函数宏的_行为类似于函数，并且具有相似的用途。它们的使用通常被认为是不安全的，几乎它们能做的任何事情都可以通过普通函数完成。

带有替换文本的类似对象的宏

当预处理器遇到此指令时，任何进一步出现的标识符都会被替换为_replacement\_text_。传统上，标识符全部以大写字母键入，并使用下划线表示空格。

考虑以下程序：

```cpp
#include <iostream>

#define MY_NAME "Alex"

int main()
{
    std::cout << "My name is: " << MY_NAME << '\n';

    return 0;
}
```

预处理器将上面的内容转换为以下内容：

```cpp
// The contents of iostream are inserted here

int main()
{
    std::cout << "My name is: " << "Alex" << '\n';

    return 0;
}
```

运行时，打印输出`My name is: Alex`。

使用带有替换文本的类似对象的宏（在 C 中）作为将名称分配给文字的方法。这不再是必要的，因为 C++ 中有更好的方法。具有替换文本的类对象宏现在通常只能在遗留代码中看到。

### 没有替换文本的类似对象的宏

_类似对象的宏_也可以在没有替换文本的情况下定义。

例如：

```cpp
#define USE_YEN
```

这种形式的宏的工作方式与您可能期望的一样：任何进一步出现的标识符都将被删除并替换为任何内容！

这可能看起来毫无用处，而且对于进行文本替换也_毫无用处。_然而，这并不是这种形式的指令的通常用途。我们稍后将讨论此表单的用途。

与带有替换文本的类对象宏不同，这种形式的宏通常被认为可以使用。

### 条件编译

条件_编译_预处理器指令允许您指定在什么条件下某些内容将或不会编译。有很多不同的条件编译指令，但我们只介绍迄今为止最常用的三个：_#ifdef_、_#ifndef_和_#endif_。

_#ifdef_预处理器指令允许预处理器检查标识符是否先前已被_#define_过。如果是这样，则编译_#ifdef_和匹配的_#endif_之间的代码。如果不是，则忽略该代码。

考虑以下程序：

```cpp
#include <iostream>

#define PRINT_JOE

int main()
{
#ifdef PRINT_JOE
    std::cout << "Joe\n"; // will be compiled since PRINT_JOE is defined
#endif

#ifdef PRINT_BOB
    std::cout << "Bob\n"; // will be excluded since PRINT_BOB is not defined
#endif

    return 0;
}
```

因为 PRINT\_JOE 已被 #define，所以该行将`std::cout << "Joe\n"`被编译。由于 PRINT\_BOB 尚未#define，因此该行将`std::cout << "Bob\n"`被忽略。

_#ifndef与#ifdef_相反，它允许您检查标识符是否_尚未#define_。

```cpp
#include <iostream>

int main()
{
#ifndef PRINT_BOB
    std::cout << "Bob\n";
#endif

    return 0;
}
```

该程序打印“Bob”，因为 PRINT\_BOB 从未被_#define_过。

您还会看到`#ifdef PRINT_BOB`和来代替和。它们的作用相同，但使用稍微更 C++ 风格的语法。`#ifndef PRINT_BOB#if defined(PRINT_BOB)#if !defined(PRINT_BOB)`



### # if 0

条件编译的一种更常见的用法是使用_#if 0_来排除代码块的编译（就好像它位于注释块内一样）：

```cpp
#include <iostream>

int main()
{
    std::cout << "Joe\n";

#if 0 // Don't compile anything starting here
    std::cout << "Bob\n";
    std::cout << "Steve\n";
#endif // until this point

    return 0;
}
```

_上面的代码只打印“Joe”，因为“Bob”和“Steve”被#if 0_预处理器指令排除在编译之外。

这提供了一种便捷的方式来“注释掉”包含多行注释的代码（由于**多行注释是不可嵌套的**，因此无法使用另一个多行注释来注释掉）：

```cpp
#include <iostream>

int main()
{
    std::cout << "Joe\n";

#if 0 // Don't compile anything starting here
    std::cout << "Bob\n";
    /* Some
     * multi-line
     * comment here
     */
    std::cout << "Steve\n";
#endif // until this point

    return 0;
}
```

要暂时重新启用已包装在 中的代码`#if 0`，您可以将 更改`#if 0`为`#if 1`：

```cpp
#include <iostream>

int main()
{
    std::cout << "Joe\n";

#if 1 // always true, so the following code will be compiled
    std::cout << "Bob\n";
    /* Some
     * multi-line
     * comment here
     */
    std::cout << "Steve\n";
#endif

    return 0;
}
```

类似对象的宏不会影响其他预处理器指令

现在您可能想知道：

```cpp
#define PRINT_JOE

#ifdef PRINT_JOE
// ...
```

既然我们将_PRINT\_JOE_定义为空，那么预处理器为什么不将_#ifdef PRINT\_JOE_中的_PRINT\_JOE_替换为空呢？

宏仅导致非预处理器命令的文本替换。由于_#ifdef PRINT\_JOE_是预处理器命令，因此文本替换不适用于该命令中的_PRINT\_JOE_。

另一个例子：

```cpp
#define FOO 9 // Here's a macro substitution

#ifdef FOO // This FOO does not get replaced because it’s part of another preprocessor directive
    std::cout << FOO << '\n'; // This FOO gets replaced with 9 because it's part of the normal code
#endif
```

然而，预处理器的最终输出根本不包含任何指令——它们都在编译之前被解析/删除，因为编译器不知道如何处理它们。

### #define 的范围

指令在编译之前被解析，从上到下逐个文件地解析。

考虑以下程序：

```cpp
#include <iostream>

void foo()
{
#define MY_NAME "Alex"
}

int main()
{
	std::cout << "My name is: " << MY_NAME << '\n';

	return 0;
}
```

尽管看起来_#define MY\_NAME “Alex”_是在函数_foo_内定义的，但预处理器不会注意到，因为它不理解函数等 C++ 概念。_因此，该程序的行为与在函数foo_之前或之后定义_#define MY\_NAME “Alex” 的_程序相同。为了便于阅读，您通常需要在函数之外 #define 标识符。

一旦预处理器完成，该文件中所有定义的标识符都将被丢弃。**这意味着指令仅从定义点到定义它们的文件末尾有效。**一个代码文件中定义的指令不会影响同一项目中的其他代码文件。

function.cpp:

```cpp
#include <iostream>

void doSomething()
{
#ifdef PRINT
    std::cout << "Printing!\n";
#endif
#ifndef PRINT
    std::cout << "Not printing!\n";
#endif
}
```

main.cpp:

```cpp
void doSomething(); // forward declaration for function doSomething()

#define PRINT

int main()
{
    doSomething();

    return 0;
}
```

The above program will print:

```
Not printing!
```

### 不要#include .cpp 文件

尽管预处理器很乐意这样做，但您通常不应该使用`#include`.cpp 文件。这些应该添加到您的项目中并进行编译。

造成这种情况的原因有很多：

* 这样做可能会导致源文件之间的命名冲突。
* 在大型项目中，很难避免单一定义规则 (ODR) 问题。
* 对此类 .cpp 文件的任何更改都将导致该 .cpp 文件和包含该文件的任何其他 .cpp 文件重新编译，这可能需要很长时间。标头的更改频率往往低于源文件。
* 这样做是非常规的。



### 尖括号与双引号

您可能好奇为什么我们使用尖括号表示`iostream`，使用双引号表示`add.h`。多个目录中可能存在具有相同文件名的头文件。我们使用尖括号与双引号有助于为预处理器提供应在何处查找头文件的线索。

当我们使用尖括号时，我们告诉预处理器这是一个不是我们自己编写的头文件。预处理器将仅在 .txt 文件指定的目录中搜索标头`include directories`。它们`include directories`被配置为项目/IDE 设置/编译器设置的一部分，通常默认为包含编译器和/或操作系统附带的头文件的目录。预处理器不会在项目的源代码目录中搜索头文件。

当我们使用双引号时，我们告诉预处理器这是我们编写的头文件。预处理器首先会在当前目录中查找头文件。如果在那里找不到匹配的标头，它将搜索`include directories`.

> 为什么 iostream 没有 .h 扩展名？
>
> 另一个常见问题是“为什么 iostream（或任何其他标准库头文件）没有 .h 扩展名？”。答案是_iostream.h是与iostream_不同的头文件！要进行解释，需要简短的历史课。
>
> 当 C++ 最初创建时，标准库中的所有文件都以_.h_后缀结尾。生活是一致的，而且很好。_cout_和_cin_的原始版本在_iostream.h_中声明。当 ANSI 委员会对该语言进行标准化时，他们决定将标准库中使用的所有名称移至_std_命名空间中，以帮助避免与用户声明的标识符发生命名冲突。然而，这提出了一个问题：如果他们将所有名称移到_std_命名空间中，则所有旧程序（包括 iostream.h）都将不再工作！
>
> 为了解决此问题，引入了一组缺少_.h_扩展名的新头文件。_这些新的头文件声明了std_命名空间内的所有名称。这样，包含的旧程序`#include <iostream.h>`不需要重写，而新程序则可以`#include <iostream>`。



### 头文件的#include顺序

如果你的头文件编写正确并且 #include 他们需要的一切，那么包含的顺序并不重要。

现在考虑以下场景：假设标头 A 需要标头 B 的声明，但忘记包含它。在我们的代码文件中，如果我们在标头 A 之前包含标头 B，我们的代码仍然可以编译！这是因为编译器将先编译 B 中的所有声明，然后再编译 A 中依赖于这些声明的代码。

但是，如果我们首先包含标头 A，那么编译器会抱怨，因为 A 中的代码将在编译器看到 B 中的声明之前被编译。这实际上是更好的选择，因为错误已经浮出水面，然后我们可以修复它。



### Header guards

```cpp
#ifndef SOME_UNIQUE_NAME_HERE
#define SOME_UNIQUE_NAME_HERE

// your declarations (and certain types of definitions) here

#endif
```

When this header is #included, the preprocessor checks whether _SOME\_UNIQUE\_NAME\_HERE_ has been previously defined. If this is the first time we’re including the header, _SOME\_UNIQUE\_NAME\_HERE_ will not have been defined. Consequently, it #defines _SOME\_UNIQUE\_NAME\_HERE_ and includes the contents of the file. If the header is included again into the same file, _SOME\_UNIQUE\_NAME\_HERE_ will already have been defined from the first time the contents of the header were included, and the contents of the header will be ignored (thanks to the #ifndef).

All of your header files should have header guards on them. _SOME\_UNIQUE\_NAME\_HERE_ can be any name you want, but by convention is set to the full filename of the header file, typed in all caps, using underscores for spaces or punctuation. For example, _square.h_ would have the header guard:

square.h:

```cpp
#ifndef SQUARE_H
#define SQUARE_H

int getSquareSides()
{
    return 4;
}

#endif
```

Even the standard library headers use header guards. If you were to take a look at the iostream header file from Visual Studio, you would see:

```cpp
#ifndef _IOSTREAM_
#define _IOSTREAM_

// content here

#endif
```



### Header guards do not prevent a header from being included once into different code files

Note that the goal of header guards is to prevent a code file from receiving more than one copy of a guarded header. By design, header guards do _not_ prevent a given header file from being included (once) into separate code files. This can also cause unexpected problems. Consider:

square.h:

```cpp
#ifndef SQUARE_H
#define SQUARE_H

int getSquareSides()
{
    return 4;
}

int getSquarePerimeter(int sideLength); // forward declaration for getSquarePerimeter

#endif
```

square.cpp:

```cpp
#include "square.h"  // square.h is included once here

int getSquarePerimeter(int sideLength)
{
    return sideLength * getSquareSides();
}
```

main.cpp:

```cpp
#include "square.h" // square.h is also included once here
#include <iostream>

int main()
{
    std::cout << "a square has " << getSquareSides() << " sides\n";
    std::cout << "a square of length 5 has perimeter length " << getSquarePerimeter(5) << '\n';

    return 0;
}
```

Note that _square.h_ is included from both _main.cpp_ and _square.cpp_. This means the contents of _square.h_ will be included once into _square.cpp_ and once into _main.cpp_.

Let’s examine why this happens in more detail. When _square.h_ is included from _square.cpp_, _SQUARE\_H_ is defined until the end of _square.cpp_. This define prevents _square.h_ from being included into _square.cpp_ a second time (which is the point of header guards). However, once _square.cpp_ is finished, _SQUARE\_H_ is no longer considered defined. This means that when the preprocessor runs on _main.cpp_, _SQUARE\_H_ is not initially defined in _main.cpp_.

The end result is that both _square.cpp_ and _main.cpp_ get a copy of the definition of _getSquareSides_. This program will compile, but the linker will complain about your program having multiple definitions for identifier _getSquareSides_!

The best way to work around this issue is simply to put the function definition in one of the .cpp files so that the header just contains a forward declaration:

square.h:

```cpp
#ifndef SQUARE_H
#define SQUARE_H

int getSquareSides(); // forward declaration for getSquareSides
int getSquarePerimeter(int sideLength); // forward declaration for getSquarePerimeter

#endif
```

square.cpp:

```cpp
#include "square.h"

int getSquareSides() // actual definition for getSquareSides
{
    return 4;
}

int getSquarePerimeter(int sideLength)
{
    return sideLength * getSquareSides();
}
```

main.cpp:

```cpp
#include "square.h" // square.h is also included once here
#include <iostream>

int main()
{
    std::cout << "a square has " << getSquareSides() << " sides\n";
    std::cout << "a square of length 5 has perimeter length " << getSquarePerimeter(5) << '\n';

    return 0;
}
```

Now when the program is compiled, function _getSquareSides_ will have just one definition (via _square.cpp_), so the linker is happy. File _main.cpp_ is able to call this function (even though it lives in _square.cpp_) because it includes _square.h_, which has a forward declaration for the function (the linker will connect the call to _getSquareSides_ from _main.cpp_ to the definition of _getSquareSides_ in _square.cpp_).

### pragma once

Modern compilers support a simpler, alternate form of header guards using the `#pragma` preprocessor directive:

```cpp
#pragma once

// your code here
```

`#pragma once` serves the same purpose as header guards: to avoid a header file from being included multiple times. With traditional header guards, the developer is responsible for guarding the header (by using preprocessor directives `#ifndef`, `#define`, and `#endif`). With `#pragma once`, we’re requesting that the compiler guard the header. How exactly it does this is an implementation-specific detail.



## 通过 static\_cast 运算符进行显式类型转换的简介

回到我们最近的`print()`示例，如果我们_有意_想要将双精度值传递给一个采用整数的函数（知道转换后的值会删除任何小数部分？）关闭“将警告视为错误”只是为了让我们的程序编译是一个坏主意，因为这样我们每次编译时都会收到警告（其中我们很快就会学会忽略），并且我们可能会忽视有关更严重问题的警告。

C++ 支持第二种类型转换方法，称为显式类型转换。 **显式类型转换**允许我们（程序员）显式告诉编译器将值从一种类型转换为另一种类型，并且我们对此承担全部责任该转换的结果（意味着如果转换导致价值损失，那是我们的错）。

要执行显式类型转换，在大多数情况下我们将使用 `static_cast` 运算符。 `static cast` 的语法看起来有点有趣：

```
static_cast
```

static\_cast 将表达式中的值作为输入，并返回转换为 _new\_type_ 指定类型的值（例如 int、bool、字符，双精度）。

```cpp
#include <iostream>

void print(int x)
{
	std::cout << x << '\n';
}

int main()
{
	print( static_cast<int>(5.5) ); // explicitly convert double value 5.5 to an int

	return 0;
}
```



## 编译时常量

A **编译时常量**是一个常量，其值为常量表达式。文字（例如“1”、“2.3”和“Hello，world！”）是一种编译时常量。

Const 变量可能是也可能不是编译时常量（取决于它们的初始化方式）。

编译时常量

如果 const 变量的初始值设定项是常量表达式，则该变量是编译时常量。

考虑一个与上面类似的使用 const 变量的程序：

```cpp
#include <iostream>

int main()
{
	const int x { 3 };  // x is a compile-time const
	const int y { 4 };  // y is a compile-time const

	const int z { x + y }; // x + y is a constant expression, so z is compile-time const

	std::cout << z << '\n';

	return 0;
}
```

由于`x`和`y`的初始化值为常量表达式，`x`和`y` 是编译时常量。这意味着 `x + y` 也是常量表达式。因此，当编译器编译此程序时，它可以计算 `x + y` 的值，并将常量表达式替换为结果文字`7`。

请注意，编译时 const 的初始值设定项可以是任何常量表达式。以下所有内容都将是编译时 const 变量：

```cpp
const int z { 3 };     // 3 is a constant expression, so z is compile-time const
const int a { 1 + 2 }; // 1 + 2 is a constant expression, so a is compile-time const
const int b { z * 2 }; // z * 2 is a constant expression, so b is compile-time const
```

编译时 const 变量通常用作命名常量：

```cpp
const double gravity { 9.8 };
```



### Constexpr/consteval 函数



## string

### 字符串输入`std::cin`

将 `std::string` 与 `std::cin` 一起使用可能会产生一些惊喜！考虑以下示例：

```cpp
#include <iostream>
#include <string>

int main()
{
    std::cout << "Enter your full name: ";
    std::string name{};
    std::cin >> name; // this won't work as expected since std::cin breaks on whitespace

    std::cout << "Enter your favorite color: ";
    std::string color{};
    std::cin >> color;

    std::cout << "Your name is " << name << " and your favorite color is " << color << '\n';

    return 0;
}
```

以下是该程序示例运行的结果：

```
输入您的全名：John Doe
输入您最喜欢的颜色：您的名字是 John，您最喜欢的颜色是 Doe
```

嗯，这是不对的！发生了什么？事实证明，当使用 `operator>>` 从 `std::cin` 中提取字符串时，`operator>>` 仅返回遇到的第一个空格之前的字符。任何其他字符都留在`std::cin`内，等待下一次提取。

因此，当我们使用 `operator>>` 将输入提取到变量 `name` 时，仅提取了 `"John"`，留下了 而不是等待我们输入颜色。然后程序结束。 时，它会提取 将输入提取到变量 。然后，当我们使用 `" Doe"` 内 `std::cinoperator>>color"Doe"`

使用`std::getline()`输入文字

要将整行输入读取到字符串中，最好使用 `std::getline()` 函数。 `std::getline()` 需要两个参数：第一个是 `std::cin`，第二个是字符串变量。

这里是使用 `std::getline()` 的与上面相同的程序：

```cpp
#include <iostream>
#include <string> // For std::string and std::getline

int main()
{
    std::cout << "Enter your full name: ";
    std::string name{};
    std::getline(std::cin >> std::ws, name); // read a full line of text into name

    std::cout << "Enter your favorite color: ";
    std::string color{};
    std::getline(std::cin >> std::ws, color); // read a full line of text into color

    std::cout << "Your name is " << name << " and your favorite color is " << color << '\n';

    return 0;
}
```

现在我们的程序按预期工作：

```
输入您的全名：John Doe
输入您最喜欢的颜色：蓝色
你的名字是 John Doe，你最喜欢的颜色是蓝色
```



### std::ws



在 C++20 中，还可以使用 `std::ssize()` 函数获取 `std::string` 作为带符号整数值的长度：

```cpp
#include <iostream>
#include <string>

int main()
{
    std::string name{ "Alex" };
    std::cout << name << " has " << std::ssize(name) << " characters\n";

    return 0;
}
```



### 初始化 `std::string` 成本高昂

每当初始化 std::string 时，都会创建用于初始化它的字符串的副本。复制字符串的成本很高，因此应注意尽量减少复制的数量。

### 不要按值传递`std::string`

当 `std::string` 按值传递给函数时，`std::string` 函数形参必须使用实参进行实例化和初始化。这会产生昂贵的副本。 - - > 使用 `std::string_view` 参数

### 返回一个`std::string`

当函数按值返回给调用者时，返回值通常会从函数复制回调用者。因此，您可能认为不应按值返回 `std::string`，因为这样做会返回 `std::string` 的昂贵副本。

但是，根据经验，当 return 语句的表达式解析为以下任何一种情况时，可以按值返回 `std::string`：

* 类型为 `std::string` 的局部变量。
* A `std::string` 由函数调用或运算符返回的值。
* A `std::string` 作为 return 语句的一部分创建。

在大多数其他情况下，不要按值返回`std::string`，因为这样做会产生昂贵的副本。

如果返回 C 样式字符串文字，请使用 `std::string_view` 返回类型



## std::string\_view C++17

为了解决 `std::string` 初始化（或复制）成本高昂的问题，C++17 引入了 `std::string_view` （位于 `std::string_view` 提供对 _现有_ 字符串（C 风格字符串、`std::string`，或另一个`std::string_view`），无需制作副本。 **只读**表示我们可以访问和使用正在查看的值，但不能修改它。

```cpp
#include <iostream>
#include <string_view> // C++17

// str provides read-only access to whatever argument is passed in
void printSV(std::string_view str) // now a std::string_view
{
    std::cout << str << '\n';
}

int main()
{
    std::string_view s{ "Hello, world!" }; // now a std::string_view
    printSV(s);

    return 0;
}
```

### `std::string_view`可以用许多不同类型的字符串进行初始化

a 的优点之一`std::string_view` 就是它的灵活性。 `std::string_view` 对象可以使用 C 样式字符串、`std::string` 或其他 `std::string_view` 进行初始化：

```cpp
#include <iostream>
#include <string>
#include <string_view>

int main()
{
    std::string_view s1 { "Hello, world!" }; // initialize with C-style string literal
    std::cout << s1 << '\n';

    std::string s{ "Hello, world!" };
    std::string_view s2 { s };  // initialize with std::string
    std::cout << s2 << '\n';

    std::string_view s3 { s2 }; // initialize with std::string_view
    std::cout << s3 << '\n';

    return 0;
}
```

### `std::string_view`参数将接受许多不同类型的字符串参数

C 样式字符串和 `std::string` 都会隐式转换为 `std::string_view`。因此，`std::string_view` 参数将接受 C 样式字符串、`std::string` 或 `std::string_view` 类型的参数：

```cpp
#include <iostream>
#include <string>
#include <string_view>

void printSV(std::string_view str)
{
    std::cout << str << '\n';
}

int main()
{
    printSV("Hello, world!"); // call with C-style string literal

    std::string s2{ "Hello, world!" };
    printSV(s2); // call with std::string

    std::string_view s3 { s2 };
    printSV(s3); // call with std::string_view

    return 0;
}
```

### `std::string_view`不会隐式转换为`std::string`

因为 `std::string` 会复制其初始值设定项（成本高昂），C++ 不允许将 `std::string_view` 隐式转换为 参数，并在可能不需要此类副本的情况下无意中生成昂贵的副本。 参数传递给 `std::string`。这是为了防止意外地将 `std::string_viewstd::string`

### Assignment changes what the `std::string_view` is viewing

Assigning a new string to a `std::string_view` causes the `std::string_view` to view the new string. It does not modify the prior string being viewed in any way.

The following example illustrates this:

```cpp
#include <iostream>
#include <string>
#include <string_view>

int main()
{
    std::string name { "Alex" };
    std::string_view sv { name }; // sv is now viewing name
    std::cout << sv << '\n'; // prints Alex

    sv = "John"; // sv is now viewing "John" (does not change name)
    std::cout << sv << '\n'; // prints John

    std::cout << name << '\n'; // prints Alex

    return 0;
}
```

In the above example, `sv = "John"` causes `sv` to now view the string `"John"`. It does not change the value held by `name` (which is still `"Alex"`).

### Literals for `std::string_view`

默认情况下，双引号字符串文字是 C 样式字符串文字。我们可以通过在双引号字符串文字后面使用 后缀来创建类型为 `std::string_view` 的字符串文字。`sv`

```cpp
#include <iostream>
#include <string>      // for std::string
#include <string_view> // for std::string_view

int main()
{
    using namespace std::string_literals;      // access the s suffix
    using namespace std::string_view_literals; // access the sv suffix

    std::cout << "foo\n";   // no suffix is a C-style string literal
    std::cout << "goo\n"s;  // s suffix is a std::string literal
    std::cout << "moo\n"sv; // sv suffix is a std::string_view literal

    return 0;
};
```

### 常量表达式`std::string_view`

与 `std::string` 不同，`std::string_view` 完全支持 constexpr：

```cpp
#include <iostream>
#include <string_view>

int main()
{
    constexpr std::string_view s{ "Hello, world!" }; // s is a string symbolic constant
    std::cout << s << '\n'; // s will be replaced with "Hello, world!" at compile-time

    return 0;
}
```

这使得 `constexpr std::string_view` 成为需要字符串符号常量时的首选。

### 使用不当`std::string_view`

让我们看一下误用 `std::string_view` 给我们带来麻烦的几个案例。

这是我们的第一个例子：

```cpp
#include <iostream>
#include <string>
#include <string_view>

int main()
{
    std::string_view sv{};

    { // create a nested block
        std::string s{ "Hello, world!" }; // create a std::string local to this nested block
        sv = s; // sv is now viewing s
    } // s is destroyed here, so sv is now viewing an invalid string

    std::cout << sv << '\n'; // undefined behavior

    return 0;
}
```

在此示例中，我们在嵌套块内创建 `std::string s`（先不用担心嵌套块是什么）。然后我们设置`sv`查看`s`。 `s` 然后在嵌套块的末尾被销毁。 `sv` 不知道 `s` 已被销毁。当我们使用 `sv` 时，我们将访问无效对象，并导致未定义的行为。

这是同一问题的另一个变体，我们使用函数的 返回值初始化 `std::string_view`：`std::string`

```cpp
#include <iostream>
#include <string>
#include <string_view>

std::string getName()
{
    std::string s { "Alex" };
    return s;
}

int main()
{
  std::string_view name { getName() }; // name initialized with return value of function
  std::cout << name << '\n'; // undefined behavior

  return 0;
}
```

这与前面的示例类似。 `getName()` 函数返回包含字符串“Alex”的 `std::string`。返回值是临时对象，在包含函数调用的完整表达式末尾被销毁。我们必须立即使用这个返回值，或者复制它以供稍后使用。

但是`std::string_view`不复印。相反，它创建临时返回值的视图，然后将其销毁。这使得我们的 `std::string_view` 悬空（查看无效对象），并且打印视图会导致未定义的行为。

以下是上述内容的不太明显的变体：

```cpp
#include <iostream>
#include <string>
#include <string_view>

int main()
{
    using namespace std::string_literals;
    std::string_view name { "Alex"s }; // "Alex"s creates a temporary std::string
    std::cout << name << '\n'; // undefined behavior

    return 0;
}
```

A `std::string` 文字（通过 `s` 文字后缀创建）创建一个临时 `std::string` 对象。因此，在本例中，`"Alex"s` 创建一个临时的 `std::string`，然后我们将其用作 `name` 的初始值设定项。此时，`name`查看临时`std::string`。然后临时的 `std::string` 被销毁，留下 `name` 悬空。当我们使用 `name` 时，我们会得到未定义的行为。

### Be careful returning a `std::string_view`

`std::string_view` can be used as the return value of a function. However, this is often dangerous.

Because local variables are destroyed at the end of the function, returning a `std::string_view` to a local variable will result in the returned `std::string_view` being invalid, and further use of that `std::string_view` will result in undefined behavior. For example:

```cpp
#include <iostream>
#include <string>
#include <string_view>

std::string_view getBoolName(bool b)
{
    std::string t { "true" };  // local variable
    std::string f { "false" }; // local variable

    if (b)
        return t;  // return a std::string_view viewing t

    return f; // return a std::string_view viewing f
} // t and f are destroyed at the end of the function

int main()
{
    std::cout << getBoolName(true) << ' ' << getBoolName(false) << '\n'; // undefined behavior

    return 0;
}
```

In the above example, when `getBoolName(true)` is called, the function returns a `std::string_view` that is viewing `t`. However, `t` is destroyed at the end of the function. This means the returned `std::string_view` is viewing an object that has been destroyed. So when the returned `std::string_view` is printed, undefined behavior results.

Your compiler may or may not warn you about such cases.

There are two main cases where a `std::string_view` can be returned safely. First, because C-style string literals exist for the entire program, it’s fine to return C-style string literals from a function that has a return type of `std::string_view`.

```cpp
#include <iostream>
#include <string_view>

std::string_view getBoolName(bool b)
{
    if (b)
        return "true";  // return a std::string_view viewing "true"

    return "false"; // return a std::string_view viewing "false"
} // "true" and "false" are not destroyed at the end of the function

int main()
{
    std::cout << getBoolName(true) << ' ' << getBoolName(false) << '\n'; // ok

    return 0;
}
```

### 查看修改功能

想象一下你房子里的一扇窗户，看着停在街上的一辆汽车。你可以透过窗户看到汽车，但你不能触摸或移动汽车。您的窗户仅提供汽车的视野，汽车是一个完全独立的对象。

许多窗户都有窗帘，可以让我们改变视野。我们可以关闭左侧或右侧的窗帘以减少我们能看到的东西。我们不改变外面的东西，我们只是减少可见区域。

因为`std::string_view`是一个视图，它包含让我们通过“关闭窗帘”来修改视图的函数。这不会以任何方式修改正在查看的字符串，只是修改视图本身。

* `remove_prefix()` 成员函数删除视图左侧的字符。
* `remove_suffix()` 成员函数删除视图右侧的字符。

```cpp
#include <iostream>
#include <string_view>

int main()
{
	std::string_view str{ "Peach" };
	std::cout << str << '\n';

	// Remove 1 character from the left side of the view
	str.remove_prefix(1);
	std::cout << str << '\n';

	// Remove 2 characters from the right side of the view
	str.remove_suffix(2);
	std::cout << str << '\n';

	str = "Peach"; // reset the view
	std::cout << str << '\n';

	return 0;
}
```

This program produces the following output:

```
Peach
each
ea
Peach
```

### `std::string_view`可能或可能不是空终止

仅查看较大字符串的子字符串的能力会带来一个值得注意的结果：`std::string_view` 可能会也可能不会以 null 结尾。考虑字符串“snowball”，它以 null 结尾。如果 `std::string_view` 查看整个字符串，那么它正在查看一个以 null 结尾的字符串。但是，如果 `std::string_view` 仅查看子字符串“now”，则该子字符串不是以 null 结尾的（下一个字符是“b”）。



## &#x20;Internal linkage

具有内部链接的标识符**内部链接**可以在单个翻译单元中查看和使用，但无法从其他翻译单元访问（即也就是说，它没有暴露给链接器）。这意味着，如果两个源文件具有具有内部链接的相同命名标识符，则这些标识符将被视为独立的（并且不会因重复定义而导致 ODR 违规）。

具有内部链接的全局变量

具有内部链接的全局变量有时称为**内部变量**。

要将非常量全局变量设为内部变量，我们使用`static` 关键字。

```cpp
#include <iostream>

static int g_x{}; // non-constant globals have external linkage by default, but can be given internal linkage via the static keyword

const int g_y{ 1 }; // const globals have internal linkage by default
constexpr int g_z{ 2 }; // constexpr globals have internal linkage by default

int main()
{
    std::cout << g_x << ' ' << g_y << ' ' << g_z << '\n';
    return 0;
}
```

Const 和 constexpr 全局变量默认具有内部链接（因此不需要 `static` 关键字 - 如果使用它，它将被忽略）。



## 内联函数和变量

现代的 inline 关键字

在前面的章节中，我们提到不应在头文件中实现函数（具有外部链接），因为当这些头文件包含在多个 .cpp 文件中时，函数定义将被复制到多个 .cpp 文件中。然后这些文件将被编译，链接器将抛出一个错误，因为它会注意到您多次定义了同一个函数，这违反了单一定义规则。

在现代 C++ 中，术语`inline` 已演变为“允许多个定义”的意思。因此，内联函数是一种允许在多个文件中定义的函数。

内联函数有两个主要要求：

* 编译器需要能够在使用该函数的每个翻译单元中查看内联函数的完整定义（前向声明本身是不够的）。如果还提供了前向声明，则定义可以在使用点之后发生。
* 内联函数的每个定义必须相同，否则将导致未定义的行为。

pi.h:

```cpp
#ifndef PI_H
#define PI_H

inline double pi() { return 3.14159; }

#endif
```

main.cpp:

```cpp
#include <iostream>
#include "pi.h" // will include a copy of pi() here

double circumference(double radius); // forward declaration

int main()
{
    std::cout << pi() << '\n';
    std::cout << circumference(2.0) << '\n';

    return 0;
}
```

math.cpp

```cpp
#include "pi.h" // will include a copy of pi() here

double circumference(double radius)
{
    return 2.0 * pi() * radius;
}
```

这对于**仅头文件库**特别有用，这些库是实现某些功能的一个或多个头文件（没有 .cpp 文件）包括）。仅标头库很受欢迎，因为不需要将源文件添加到项目中即可使用它们，也不需要链接任何内容。您只需 #include 仅标头库，然后就可以使用它。

在大多数情况下，您不应将函数或变量标记为内联，除非您在头文件中定义它们（并且它们尚未隐式内联）。

### 内联变量C++17

在上面的示例中，`pi()` 被编写为返回常量值的函数。如果将 `pi` 实现为 (const) 变量，则会更加直接。然而，在 C++17 之前，这样做存在一些障碍并且效率低下。

C++17 引入了**内联变量**，即允许在多个文件中定义的变量。内联变量的工作方式与内联函数类似，并且具有相同的要求（编译器必须能够在使用该变量的任何地方看到相同的完整定义）。

以下是隐式内联的：

* 静态 constexpr 变量

与 constexpr 函数不同，（非静态）constexpr 变量默认不是内联的！

## 在多个文件之间共享全局常量

作为内联变量的全局常量

constants.h:

```cpp
#ifndef CONSTANTS_H
#define CONSTANTS_H

// define your own namespace to hold constants
namespace constants
{
    inline constexpr double pi { 3.14159 }; // note: now inline constexpr
    inline constexpr double avogadro { 6.0221413e23 };
    inline constexpr double myGravity { 9.2 }; // m/s^2 -- gravity is light on this planet
    // ... other related constants
}
#endif
```

main.cpp:

```cpp
#include "constants.h"

#include <iostream>

int main()
{
    std::cout << "Enter a radius: ";
    double radius{};
    std::cin >> radius;

    std::cout << "The circumference is: " << 2 * radius * constants::pi << '\n';

    return 0;
}
```

We can include `constants.h` into as many code files as we want, but these variables will only be instantiated once and shared across all code files.

This method does retain the downside of requiring every file that includes the constants header be recompiled if any constant value is changed.



## 静态局部变量

局部变量默认具有`automatic duration` ，这意味着它们在定义时创建，并在退出块时销毁。

在局部变量上使用 `static` 关键字会将其持续时间从 `automatic duration` 更改为 `static duration`。这意味着变量现在在程序开始时创建，并在程序结束时销毁（就像全局变量一样）。因此，静态变量即使超出范围也将保留其值！

避免使用`static`局部变量，除非该变量永远不需要重置。



## 类的成员默认是私有的

具有_私有_访问级别的成员称为_私有成员_. **私有成员**是类类型的成员，只能由同一类的其他成员访问。

```cpp
#include <iostream>

class Date // now a class instead of a struct
{
    // class members are private by default, can only be accessed by other members
    int m_year {};     // private by default
    int m_month {};    // private by default
    int m_day {};      // private by default

    void print() const // private by default
    {
        // private members can be accessed in member functions
        std::cout << m_year << '/' << m_month << '/' << m_day;
    }
};

int main()
{
    Date today { 2020, 10, 14 }; // compile error: can no longer use aggregate initialization

    // private members can not be accessed by the public
    today.m_day = 16; // compile error: the m_day member is private
    today.print();    // compile error: the print() member function is private

    return 0;
}
```

### 通过访问说明符设置访问级别

默认情况下，结构（和联合）的成员是公共的，类的成员是私有的。

但是，我们可以使用**访问说明符**显式设置成员的访问级别。访问说明符设置该说明符后的_所有成员_的访问级别。 C++ 提供了三种访问说明符：`public:`、`private:` 和 `protected:`。

在下面的示例中，我们使用 `public:` 访问说明符来确保 `print()` 成员函数可供公众使用，并且 < /span>`private:` 访问说明符使我们的数据成员私有。

```cpp
class Date
{
// Any members defined here would default to private

public: // here's our public access specifier

    void print() const // public due to above public: specifier
    {
        // members can access other private members
        std::cout << m_year << '/' << m_month << '/' << m_day;
    }

private: // here's our private access specifier

    int m_year { 2020 };  // private due to above private: specifier
    int m_month { 14 }; // private due to above private: specifier
    int m_day { 10 };   // private due to above private: specifier
};

int main()
{
    Date d{};
    d.print();  // okay, main() allowed to access public members

    return 0;
}
```

该示例可编译。由于 `print()` 由于 `public:` 访问说明符而成为公共成员，因此允许 `main()`（属于公共成员）访问它。

因为我们有私有成员，所以无法聚合初始化`d`。对于此示例，我们使用默认成员初始化（作为临时解决方法）。

由于类默认为私有访问，因此可以省略前导`private:` 访问说明符：

```cpp
class Foo
{
// private access specifier not required here since classes default to private members
    int m_something {};  // private by default
};
```

### 访问功能

**访问函数**是一个普通的公共成员函数，其作用是检索或更改私有成员变量的值。

访问函数有两种类型：getter 和 setter。 **获取器**（有时也称为**访问器**）是返回私有成员变量的值。 **Setter**（有时也称为**mutators**）是公共成员函数，用于设置私有成员变量的值。

Getter 通常被设为 const，因此可以在 const 和非 const 对象上调用它们。 Setter 应该是非常量的，这样他们就可以修改数据成员。

出于说明目的，让我们更新我们的 `Date` 类以拥有完整的 getter 和 setter 集：

```cpp
#include <iostream>

class Date
{
private:
    int m_year { 2020 };
    int m_month { 10 };
    int m_day { 14 };

public:
    void print()
    {
        std::cout << m_year << '/' << m_month << '/' << m_day << '\n';
    }

    int getYear() const { return m_year; }        // getter for year
    void setYear(int year) { m_year = year; }     // setter for year

    int getMonth() const  { return m_month; }     // getter for month
    void setMonth(int month) { m_month = month; } // setter for month

    int getDay() const { return m_day; }          // getter for day
    void setDay(int day) { m_day = day; }         // setter for day
};

int main()
{
    Date d{};
    d.setYear(2021);
    std::cout << "The year is: " << d.getYear() << '\n';

    return 0;
}
```

## 返回数据成员引用的成员函数

“通过引用返回的对象必须在函数返回后存在”。这意味着我们不应该通过引用返回局部变量，因为在局部变量被销毁后，引用将保持悬空状态。但是，通常可以通过引用返回通过引用传递的函数参数或具有静态持续时间的变量（静态局部变量或全局变量），因为它们通常不会在函数返回后被销毁。

例如：

```cpp
// Takes two std::string objects, returns the one that comes first alphabetically
const std::string& firstAlphabetical(const std::string& a, const std::string& b)
{
	return (a < b) ? a : b; // We can use operator< on std::string to determine which comes first alphabetically
}

int main()
{
	std::string hello { "Hello" };
	std::string world { "World" };

	std::cout << firstAlphabetical(hello, world); // either hello or world will be returned by reference

	return 0;
}
```

### 右值隐式对象并按引用返回

但是，如果我们的隐式对象是右值（例如某些按值返回的函数的返回值）怎么办？右值对象在创建它们的完整表达式结束时被销毁。当右值对象被销毁时，对该右值成员的任何引用都将失效并悬空，并且使用此类引用将产生未定义的行为。

因此，对右值对象成员的引用只能在创建右值对象的完整表达式中安全地使用。

```cpp
#include <iostream>
#include <string>
#include <string_view>

class Employee
{
	std::string m_name{};

public:
	void setName(std::string_view name) { m_name = name; }
	const auto& getName() const { return m_name; } //  getter returns by const reference
};

// createEmployee() returns an Employee by value (which means the returned value is an rvalue)
Employee createEmployee(std::string_view name)
{
	Employee e;
	e.setName(name);
	return e;
}

int main()
{
	// Case 1: okay: use returned reference to member of rvalue class object in same expression
	std::cout << createEmployee("Frank").getName();

	// Case 2: bad: save returned reference to member of rvalue class object for use later
	const std::string& ref { createEmployee("Garbo").getName() }; // reference becomes dangling when return value of createEmployee() is destroyed
	std::cout << ref; // undefined behavior

	// Case 3: okay: copy referenced value to local variable for use later
	std::string val { createEmployee("Hans").getName() }; // makes copy of referenced member
	std::cout << val; // okay: val is independent of referenced member

	return 0;
}
```

## 构造函数简介

一旦我们将任何成员变量设为私有（以隐藏我们的数据），我们的类类型就不再是聚合（因为聚合不能拥有私有成员）。这意味着我们不再能够使用聚合初始化：

```cpp
class Foo // Foo is not an aggregate (has private members)
{
    int m_x {};
    int m_y {};
};

int main()
{
    Foo foo { 6, 7 }; // compile error: can not use aggregate initialization

    return 0;
}
```

### **构造函数**是一个特殊的成员函数，在创建非聚合类类型对象后自动调用。

当定义非聚合类类型对象时，编译器会查看是否可以找到与调用者提供的初始化值（如果有）匹配的可访问构造函数。

* 如果找到可访问的匹配构造函数，则为该对象分配内存，然后调用构造函数。
* 如果找不到可访问的匹配构造函数，则会生成编译错误。

### 命名构造函数

与普通成员函数不同，构造函数对于如何命名有特定的规则：

* 构造函数必须与类具有相同的名称（大小写相同）。对于模板类，此名称不包括模板参数。
* 构造函数没有返回类型（甚至没有`void`）。

由于构造函数通常是类接口的一部分，因此它们通常是公共的。

```cpp
#include <iostream>

class Foo
{
private:
    int m_x {};
    int m_y {};

public:
    Foo(int x, int y) // here's our constructor function that takes two initializers
    {
        std::cout << "Foo(" << x << ", " << y << ") constructed\n";
    }

    void print() const
    {
        std::cout << "Foo(" << m_x << ", " << m_y << ")\n";
    }
};

int main()
{
    Foo foo{ 6, 7 }; // calls Foo(int, int) constructor
    foo.print();

    return 0;
}
```

## 构造函数成员初始值设定项列表

```cpp
#include <iostream>

class Foo
{
private:
    int m_x {};
    int m_y {};

public:
    Foo(int x, int y)
        : m_x { x }, m_y { y } // here's our member initialization list
    {
        std::cout << "Foo(" << x << ", " << y << ") constructed\n";
    }

    void print() const
    {
        std::cout << "Foo(" << m_x << ", " << m_y << ")\n";
    }
};

int main()
{
    Foo foo{ 6, 7 };
    foo.print();

    return 0;
}
```

成员初始值设定项列表在构造函数参数之后定义。它以冒号 (:) 开头，然后列出要初始化的每个成员以及该变量的初始化值，并用逗号分隔。必须在这里使用直接形式的初始化（最好使用大括号，但圆括号也可以）——使用复制初始化（使用等于）在这里不起作用。另请注意，成员初始值设定项列表不以分号结尾。

### 成员初始值设定项列表格式

C++ 提供了很大的自由度来根据您的喜好格式化成员初始值设定项列表，因为它不关心您将冒号、逗号或空格放在哪里。

以下样式都是有效的（并且您可能会在实践中看到这三种样式）：

```cpp
Foo(int x, int y) : m_x { x }, m_y { y }
{
}
```

```cpp
Foo(int x, int y) :
    m_x { x },
    m_y { y }
{
}
```

```cpp
Foo(int x, int y)
    : m_x { x }
    , m_y { y }
{
}
```

### 成员初始化顺序

因为 C++ 标准是这样规定的，所以成员初始值设定项列表中的成员始终按照它们在类内部定义的顺序（而不是按照它们在成员初始值设定项列表中定义的顺序）进行初始化。

在上面的例子中，由于在类定义中`m_x`是在`m_y`之前定义的，所以`m_x`会先被初始化（即使它没有在成员初始值设定项列表中首先列出）。

因为我们直观地期望变量从左到右初始化，这可能会导致发生微妙的错误。考虑以下示例：

```cpp
#include <algorithm> // for std::max
#include <iostream>

class Foo
{
private:
    int m_x{};
    int m_y{};

public:
    Foo(int x, int y)
        : m_y{ std::max(x, y) }, m_x{ m_y } // issue on this line
    {
    }

    void print() const
    {
        std::cout << "Foo(" << m_x << ", " << m_y << ")\n";
    }
};

int main()
{
    Foo foo{ 6, 7 };
    foo.print();

    return 0;
}
```

打印出以下结果：

```cpp
Foo(-858993460, 7)
```

发生了什么？尽管 `m_y` 在成员初始化列表中首先列出，但由于 `m_x` 在类中首先定义，因此 `m_x` 首先被初始化。并且 `m_x` 被初始化为 `m_y` 的值，该值尚未初始化。最后，`m_y` 被初始化为较大的初始化值。

为了帮助防止此类错误，成员初始值设定项列表中的成员应按照它们在类中定义的顺序列出。如果成员初始化无序，某些编译器会发出警告。

### 隐式默认构造函数

如果非聚合类类型对象没有用户声明的构造函数，编译器将生成一个公共默认构造函数（以便该类可以进行值或默认初始化）。此构造函数称为**隐式默认构造函数**。

考虑以下示例：

```cpp
#include <iostream>

class Foo
{
private:
    int m_x{};
    int m_y{};

    // Note: no constructors declared
};

int main()
{
    Foo foo{};

    return 0;
}
```

该类没有用户声明的构造函数，因此编译器将为我们生成一个隐式的默认构造函数。该构造函数将用于实例化`foo{}`。

隐式默认构造函数相当于没有参数、没有成员初始值设定项列表且构造函数主体中没有语句的构造函数。换句话说，对于上面的 `Foo` 类，编译器会生成以下内容：

```cpp
public:
    Foo() // implicitly generated default constructor
    {
    }
```

当我们的类没有数据成员时，隐式默认构造函数最有用。如果一个类有数据成员，我们可能希望使用用户提供的值来初始化它们，而隐式默认构造函数不足以实现这一点。

### 使用`= default`生成默认构造函数

如果我们编写一个与隐式生成的默认构造函数等效的默认构造函数，我们可以使用以下语法告诉编译器为我们生成隐式默认构造函数：

```cpp
#include <iostream>

class Foo
{
private:
    int m_x {};
    int m_y {};

public:
    Foo() = default; // generate an implicit default constructor

    Foo(int x, int y)
        : m_x { x }, m_y { y }
    {
        std::cout << "Foo(" << x << ", " << y << ") constructed\n";
    }
};

int main()
{
    Foo foo{}; // calls Foo() default constructor

    return 0;
}
```

在上面的示例中，由于我们有一个用户声明的构造函数 (`Foo(int, int)`)，因此通常不会生成隐式定义的默认构造函数。然而，因为我们已经告诉编译器生成这样的构造函数，所以它会生成。此构造函数随后将被我们的 `foo{}` 实例化使用。

## 类型别名

在 C++ 中，**using** 是为现有数据类型创建别名的关键字。要创建这样的类型别名，我们使用 `using` 关键字，后跟类型别名的名称，然后是等号和现有数据类型。例如：

```cpp
using Distance = double; // define Distance as an alias for type double
```

一旦定义，类型别名就可以在任何需要类型的地方使用。例如，我们可以创建一个以类型别名作为类型的变量：

```cpp
Distance milesToDestination{ 3.4 }; // defines a variable of type double
```

当编译器遇到类型别名时，它将替换为别名类型。例如：

```cpp
#include <iostream>

int main()
{
    using Distance = double; // define Distance as an alias for type double

    Distance milesToDestination{ 3.4 }; // defines a variable of type double

    std::cout << milesToDestination << '\n'; // prints a double value

    return 0;
}
```

这打印：

```
3.4
```

在上面的程序中，我们首先定义`Distance`作为类型`double`的别名。

接下来，我们定义一个名为 `milesToDestination` 且类型为 alias `Distance` 的变量。因为编译器知道 `Distance` 是类型别名，所以它将使用别名类型，即 `double`。因此，变量 `milesToDestination` 实际上被编译为 `double` 类型的变量，并且在所有方面它的行为都与 `double` 相同。

最后，我们打印 `milesToDestination` 的值，该值打印为 `double` 值。

### 类型定义

A **typedef**（“类型定义”的缩写）是一种为类型创建别名的较旧方法。要创建 typedef 别名，我们使用 `typedef` 关键字：

```cpp
// The following aliases are identical
typedef long Miles;
using Miles = long;
```

出于向后兼容性的原因，Typedef 仍在 C++ 中使用，但它们已在很大程度上被现代 C++ 中的类型别名所取代。

## 析构函数

与构造函数一样，析构函数也有特定的命名规则：

1. 析构函数必须与类同名，前面有波浪号 (\~)。
2. 析构函数不能接受参数。
3. 析构函数没有返回类型。

一个类只能有一个析构函数。

一般来说，不应该显式调用析构函数（因为当对象被销毁时它将自动调用），因为很少有想要多次清理对象的情况。

析构函数可以安全地调用其他成员函数，因为直到析构函数执行后对象才会被销毁。

```cpp
#include <iostream>

class Simple
{
private:
    int m_id {};

public:
    Simple(int id)
        : m_id { id }
    {
        std::cout << "Constructing Simple " << m_id << '\n';
    }

    ~Simple() // here's our destructor
    {
        std::cout << "Destructing Simple " << m_id << '\n';
    }

    int getID() const { return m_id; }
};

int main()
{
    // Allocate a Simple
    Simple simple1{ 1 };
    {
        Simple simple2{ 2 };
    } // simple2 dies here

    return 0;
} // simple1 dies here
```

### 隐式析构函数

如果非聚合类类型对象没有用户声明的析构函数，则编译器将生成一个具有空主体的析构函数。该析构函数称为隐式析构函数，它实际上只是一个占位符。

如果类不需要在销毁时进行任何清理，那么根本不定义析构函数就可以了，让编译器为类生成一个隐式析构函数。







