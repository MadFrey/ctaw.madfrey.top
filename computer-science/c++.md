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









