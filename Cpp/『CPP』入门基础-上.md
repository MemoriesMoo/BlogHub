---
title: 入门基础（上）
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-30.webp
description: "\U0001F916C++探秘：关键字魔法、命名空间冒险、IO流奇谋、缺省参数秘籍。"
mathjax: true
tags:
  - CPP基础
categories:
  - CPP
abbrlink: 2e3b7962
date: 2023-11-20 00:00:00
updated: 2023-11-20 00:00:00
swiper_index:
---
## 引言

> 本系列旨在为初学者提供一份全面且易懂的**C++入门指南**。我们将从C++的关键字开始，逐步探索C++的各个方面，包括命名空间、输入输出、函数特性以及**C++11**的一些新增特性，如`auto`关键字、基于范围的for循环和`nullptr`。每个主题都会有简明扼要的解释和示例代码，帮助你更好地理解和运用这些知识。
>
> 无论你是刚刚踏入编程的大门，还是想要从其他编程语言转换到C++，本指南都将帮助你建立对C++的扎实基础。通过学习本系列内容，你将掌握C++中的核心概念和常用特性，为后续学习和项目开发打下坚实的基础。

## **一、C++关键字**

### 1.1 概念

C++总计**63**个关键字，C语言**32**个关键字。

C++关键字是一组由C++语言定义的具有特殊含义的**保留字**。这些关键字在C++编程中具有特殊的用途，不能用作标识符或变量名。

此处仅对关键字进行列举，后续文章再进行详细介绍。

### 1.2 关键字列表

| 关键字       | 关键字        | 关键字           | 关键字     |
| ------------ | ------------- | ---------------- | ---------- |
| alignas      | alignof       | and              | and_eq     |
| asm          | auto          | bitand           | bitor      |
| bool         | break         | case             | catch      |
| char         | char8_t       | char16_t         | char32_t   |
| class        | compl         | concept          | const      |
| consteval    | constexpr     | constinit        | const_cast |
| continue     | co_await      | co_return        | co_yield   |
| decltype     | default       | delete           | do         |
| double       | dynamic_cast  | else             | enum       |
| explicit     | export        | extern           | false      |
| float        | for           | friend           | goto       |
| if           | inline        | int              | long       |
| mutable      | namespace     | new              | noexcept   |
| not          | not_eq        | nullptr          | operator   |
| or           | or_eq         | private          | protected  |
| public       | register      | reinterpret_cast | requires   |
| return       | short         | signed           | sizeof     |
| static       | static_assert | static_cast      | struct     |
| switch       | synchronized  | template         | this       |
| thread_local | throw         | true             | try        |
| typedef      | typeid        | typename         | union      |
| unsigned     | using         | virtual          | void       |
| volatile     | wchar_t       | while            | xor        |
| xor_eq       |               |                  |            |

注意：该表格列出了C++中的所有关键字，包括C++11、C++14、C++17、C++20以及之后版本引入的关键字。不同版本可能支持不同的关键字，具体使用时需要查阅相应的C++标准文档。

## 二、命名空间

### 2.1 问题示例

在学习**命名空间**之前，我们来看一个C语言问题。

此处定义的**变量rand**与**库中函数rand**名称冲突，但是C语言特性无法解决此问题。

```objectivec
#include <stdio.h>
#include <stdlib.h>
int rand = 10;
// C语言没办法解决类似这样的命名冲突问题，所以C++提出了namespace来解决
int main()
{
 printf("%d\n", rand);
return 0;
}
// 编译后后报错：error C2365: “rand”: 重定义；以前的定义是自带的rand“函数”
// 就与你在一个工程代码中无法定义两个同名的标识符是一个原理
```

### 2.2 产生原因

命名空间的产生是为了解决在大型程序中**命名冲突**的问题。

在大规模软件开发中，可能会有多个开发者同时编写代码，每个人都可能使用相同的变量名、函数名或类名,其中大部分变量、函数和类的名称将都存在于全局作用域中,代码合并会导致命名冲突，使得代码出现错误或不可预料的行为。

其次上述问题示例也是原因之一，C++若没有命名空间也会产生相同问题！！！

使用命名空间的目的是对标识符的名称进行**本地化**， 以避免命名冲突或名字污染，命名空间由此诞生。

### 2.3 演示示例

```cpp
// 定义命名空间MyNameSpace
namespace MyNamespace {
    int add(int a, int b) {
        return a + b;
    }

    int subtract(int a, int b) {
        return a - b;
    }
}

int main() {
    // 调用MyNamespace命名空间中的add函数
    int result1 = MyNamespace::add(5, 3); 
    // 调用MyNamespace命名空间中的subtract函数       
    int result2 = MyNamespace::subtract(10, 4);  

    return 0;
}
```

### 2.4 详解命名空间

提醒：许多市面上的书籍中的程序示例，都会加上using namespace std;这行代码，书籍上会解释是为了编写方便，导致许多读者认为这是固定示例，造成理解错误，需要摒弃此观念！！！

**eg：**

```cpp
#include<iostream>
using namespace std;

int main()
{
    cout << "Hello World!" << endl;
    return 0;
}
```

#### **2.4.1** 笔者对命名空间的理解

以将命名空间**`namespace`**看作私人领域的围墙，每个人都可以在自己的领域做任何事情，但是别人不能擅闯你的区域，除非获得你的许可。

在编程人员单人开发时，使用命名空间可以将不同模块的代码区分开避免冲突，并提供更好的代码隔离性。

多人开发时，同样可以避免项目整合是发生的命名冲突等问题。

#### **2.4.2 使用方式**

**如何获取许可证呢？**

正如上面示例的`HelloWorld`代码，使用`using namespace std`即可。

**这行代码该如何解释呢？**

**using namespace std**

`std`是C++标准库的命名空间，其中包含了许多常用的类、函数和对象，如输入输出、字符串处理、容器、算法等等。

在没有使用`using namespace std;`的情况下，访问标准库中的成员需要在其名称前加上`std::`前缀，例如`std::cout`。

**eg:**

```cpp
#include<iostream>
//using namespace std;

int main()
{
    std::cout << "Hello World!" << std::endl;
    return 0;
}
```

`using`就相当于许可证，使用`using`就可以获取围墙内的信息，并且随意使用内部内容。

正如笔者之前提的建议，尽量不要直接使用`using namespace std`，利弊参半。**谨慎使用**！！

**原因如下：**

使用`using namespace std`可以简化代码，使得在使用标准库成员时不需要写出完整的命名空间前缀，而可以直接使用它们。

例如，在使用了`using namespace std`后，可以直接使用`cout`、`string`而不需要加上`std::`前缀。

需要注意的是，`using namespace std`将整个`std`命名空间引入当前的作用域中。

这样可以减少代码中的冗余，但也可能引入命名冲突的风险。在大型项目中或需要与其他命名空间中的成员进行区分的情况下，可以避免使用`using namespace std`，而是使用具体的`using`声明来引入需要的特定成员。

**eg:**

```cpp
#include<iostream>
//using namespace std;
using std::cout;
using std::endl;

int main()
{
    cout << "Hello World!" << endl;
    return 0;
}
```

#### 2.4.3 作用域解析符

在C++中，作用域解析运算符（Scope Resolution Operator），用双冒号（::）表示，用于访问命名空间、类、结构体、枚举等的成员。

这也是通行证的一种，不过每次使用内部内容时，都需要加上该符号去获取，需要什么便可以取什么。

相对于使用`using namespace + 命名空间`全部展开，此法与`using 命名空间::需要内容`相似，减小冲突风险。

**eg:**

```cpp
#include<iostream>
usingn namespace std;

namespace kaite{
	int rand = 0;
}

int main(){
	cout << kaite::rand << endl;
    return 0;
}
```

此处使用`using namespace std`是为了展示冲突，建议读者使用另外两种方式编写。

上述代码定义了命名空间`kaite`，即使我将命名空间`std`全部展开，其中有`rand`函数，但是我并没有展开自己的命名空间，而是使用作用域解析符跨围墙寻找，因此不会和全局冲突。

相信解释到这里，读者应该已经对命名空间有一定的了解。下面列出一些注意事项。

#### 2.4.4注意事项

**命名空间可以嵌套定义**

**eg：**

```cpp
namespace N1
{
int a;
int b;
namespace N2
 {
     int c;
     int d;
 }
}

//想使用命名空间N2中的变量也需要嵌套寻求
N1::N2::c = 0;
N1::N2::d = 0;
```

 **同项目中的同名命名空间会进行合并**

同一个工程中允许存在多个相同名称的命名空间,编译器最后会合成同一个命名空间中。

一个命名空间就定义了一个新的作用域，命名空间中的所有内容都局限于该命名空间中

#### 2.4.5 总结

命名空间的使用有**三种方式**

1. 加命名空间名称及作用域限定符
2. 使用using将命名空间中某个成员引入
3. 使用using namespace 命名空间名称引入



## 三、C++输入&输出

> 在C语言中，我们经常使用**printf**以及**scanf**进行输入输出。那么当谈到C++编程，输入和输出同样是最基本且必要的操作之一。它们使得我们可以与用户交互，并将程序结果展示给用户。
>
> C++中的输入和输出通常是通过**流（stream）**对象来实现的。
>
> 流是一种抽象的概念，它提供了一种在程序和外部设备（如键盘、屏幕、文件）之间传输数据的方法。

### 3.1 流

在谈及C++输入输出时，我们首先需要了解一下流这个重要概念。

> 当谈论编程语言中的**流（stream）**时，它是一种用于在**程序**和**外部设备**（如键盘、屏幕、文件）之间传输数据的抽象概念。流提供了一种方便、统一的方式来处理输入和输出操作。

在C++中，流是通过标准库中的流类（stream class）来实现的。标准C++库提供了三个主要的流类：

1. **输入流（input stream）：** 输入流用于从外部设备（通常是键盘或文件）读取数据。在C++中，主要使用`std::istream`类来表示输入流。`std::cin`就是`std::istream`类的一个对象，通常用于从标准输入读取数据。
2. **输出流（output stream）：** 输出流用于向外部设备（通常是屏幕或文件）写入数据。在C++中，主要使用`std::ostream`类来表示输出流。`std::cout`就是`std::ostream`类的一个对象，通常用于向标准输出写入数据。
3. **输入/输出流（input/output stream）：** 输入/输出流既可以读取数据，也可以写入数据。在C++中，主要使用`std::iostream`类来表示输入/输出流。`std::cin`和`std::cout`的底层类型都是`std::iostream`，因此它们可以同时进行输入和输出操作。

流的工作方式类似于一条水流，数据从流中源源不断地流入或流出。在读取数据时，流会按顺序逐个提供数据元素，直到没有更多的数据为止。在写入数据时，数据会按顺序依次写入流中。

### 3.2 流的操作符

为了与流进行交互，C++提供了一组特殊的操作符：

- **输入操作符 `>>`：** 用于从输入流中提取数据。它会从流中读取数据并将其存储到指定的变量中。
- **输出操作符 `<<`：** 用于将数据写入输出流。它会将数据输出到流中，以便在屏幕上显示或写入到文件中。

这些操作符的使用使得流操作非常直观和易于理解，比如使用`cin >> num;`读取整数，或使用`cout << "Hello, world!";`输出字符串。

> 总结起来，流是C++中一种用于处理输入和输出操作的强大工具。它提供了统一的方式来读取和写入数据，使得与用户交互和处理文件操作变得简单和高效。理解流的基本概念和使用方法对于编写C++程序是非常重要的。

### 3.3 C++输入（输入流）

> 在C++中，我们通常使用**`cin`**来进行输入操作。`cin`是C++标准库中的一个输入流对象，它通常与键盘输入关联。通过`cin`，我们可以读取用户输入的数据并将其存储到我们定义的变量中。

让我们从一个简单的例子开始，读取用户输入的整数：

```cpp
#include <iostream>

int main() {
    int num;

    std::cout << "Please enter an integer: ";
    std::cin >> num; // 从用户输入读取整数并存储在num变量中

    std::cout << "You entered: " << num << std::endl;

    return 0;
}
```

运行程序后，它会要求你输入一个整数。在你输入整数后，程序将把输入的整数显示出来。

除了整数，我们还可以使用`cin`来读取其他数据类型，例如`double`、`char`和`std::string`（字符串）。使用方法也是相同。

### 3.4 C++输出（输出流）

> C++中，我们主要使用**`cout`**来进行输出操作。`cout`是C++标准库中的一个输出流对象，它通常与屏幕输出关联。通过`cout`，我们可以将数据展示给用户。

让我们来看一个简单的输出示例：

```cpp
#include <iostream>

int main() {
    int num = 42;

    std::cout << "The number is: " << num << std::endl;

    return 0;
}
```

运行这个程序，它会输出："The number is: 42"。

你还可以使用`printf`函数实现更复杂的格式化输出，类似于C语言中的用法。不过，在C++中，使用`cout`更加C++风格，并且更易于阅读和使用。



## 四、缺省参数

> 缺省参数（Default Arguments）是一种在函数声明中指定参数默认值的特性。当调用函数时，如果调用者没有提供对应参数的值，则函数将使用预定义的默认值。这使得函数的调用更加简洁和灵活，因为在一些情况下，可以省略一些参数，而不必在每次调用时都提供所有参数的值。
>
> 在C++中，缺省参数可以在函数的声明中指定，而不是在函数的定义中。这样做是为了避免将默认参数的信息重复多次，因为函数**通常**在头文件中声明，并在实现文件中定义

### **4.1 代码示例**

```cpp
#include <iostream>

// 带有缺省参数的函数声明
void printMessage(std::string message = "Hello, World!");

int main() {
    // 调用函数时不提供参数
    printMessage(); // 输出: "Hello, World!"

    // 调用函数时提供参数
    printMessage("Hi there!"); // 输出: "Hi there!"

    return 0;
}

// 带有缺省参数的函数定义
void printMessage(std::string message) {
    std::cout << message << std::endl;
}
```

> 在上面的例子中，我们定义了一个名为`printMessage`的函数，它带有一个缺省参数`message`，默认值为`"Hello, World!"`。在`main`函数中，我们调用`printMessage`两次：一次不提供参数，一次提供了一个字符串参数。第一次调用时，由于没有提供参数，函数使用了缺省值，输出了`"Hello, World!"`；第二次调用时，提供了一个参数，函数输出了该参数值。

### 4.2 缺省参数分类

#### 全缺省参数

```cpp
void Func(int a = 10, int b = 20, int c = 30)
 {
     cout << "a = "<< a <<endl;
     cout << "b = "<< b <<endl;
     cout << "c = "<< c <<endl;
 }
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

#### 半缺省参数

```cpp
void Func(int a, int b = 20, int c = 30)
 {
     cout << "a = "<< a <<endl;
     cout << "b = "<< b <<endl;
     cout << "c = "<< c <<endl;
 }
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 4.3注意事项

1. 缺省参数通常应该在函数参数列表的**末尾声明**。这样做是为了在调用函数时能够省略尾部参数而不会产生歧义。即半缺省参数必须从右往左依次来给出，不能间隔着给。
2. 缺省参数不能在函数声明和定义中同时出现。
3. 缺省值必须是常量或者全局变量/常量。
4. C语言不支持（编译器不支持）。