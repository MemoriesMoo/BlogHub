---
title: 函数模板初阶
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-20.webp
description: "\U0001F389C++模板：数据通用操控高招，函数、类灵活处理，编程无界。"
mathjax: true
tags:
  - CPP语言特性
categories:
  - CPP
abbrlink: 2846869a
swiper_index: 7
date: 2023-11-16 00:00:00
updated: 2023-11-16 00:00:00
---

## 一、 泛型编程

> 当谈到泛型编程时，我们通常指的是一种在编程语言中编写通用代码，以便在不同的数据类型上都能有效工作的方法。这使得代码可以更加灵活、通用和重用。**在C++中，泛型编程主要通过模板来实现，允许我们创建可以适用于多种数据类型的通用代码。**

### 1.1 为什么需要泛型编程？

> 在编写代码时，我们经常需要处理各种不同类型的数据。如果每次都为每种数据类型编写专门的代码，将会导致代码冗余并降低效率。泛型编程的目标是通过创建通用代码来解决这个问题，这些代码可以适用于不同的数据类型，而无需重复编写。

**举个例子：**

我们要实现一个通用的交换数据的函数，在C语言中我们只能通过不同名的函数实现，很麻烦，C++中我们可以通过函数重载实现，但是也有弊端：重载的函数仅仅是类型不同，代码复用率比较低，只要有新类型出现时，就需要手动添加对应的函数实现，同时代码的可维护性也随之降低！

```cpp
void Swap(int &left, int &right) {
    int temp = left;
    left = right;
    right = temp;
}

void Swap(double &left, double &right) {
    double temp = left;
    left = right;
    right = temp;
}

void Swap(char &left, char &right) {
    char temp = left;
    left = right;
    right = temp;
}

//其他类型
.........
```

**那么是否有其他方法呢？我只要提供我需要的类型，编译器会自动实现对应版本的函数！**

**C++模板正是解决此问题的利器。**

## **二、模板**

### 2.1 概念

> C++模板是一种用于实现泛型编程的重要特性，允许您编写通用的代码，可以适用于多种数据类型。模板在C++中被广泛使用，特别是在标准库中，用于创建通用的容器、算法和数据结构。

**C++中的模板有两种主要类型：函数模板和类模板。**

### **2.2 函数模板**

#### 2.2.1 概念

> 函数模板是C++中用于创建通用函数的一种机制，它允许您编写一个通用的函数，可以用于多种数据类型而不必针对每种数据类型编写单独的函数。函数模板在C++中是实现泛型编程的重要工具之一。

#### 2.2.2 语法

函数模板的语法很简单，它由**模板头和函数体组成**：

> **图片未上传**

 其中，`template <typename T>` 声明了一个模板，`T` 是类型参数的**占位符**。在函数参数和返回类型中使用 `T`，编译器会根据参数类型自动确定实际的数据类型。（typename是用来定义模板参数关键字，也可以使用class（**切记：不能使用struct代替class**））。

#### 2.2.3 示例

可以创建可以用于不同数据类型的通用函数，例如最大值函数：

> **图片未上传**

####  2.2.4 模板实例化

> 函数模板实例化是指在使用函数模板时，编译器根据传递的实际参数类型，生成特定类型的函数实现代码的过程。这个过程在编译阶段进行，以确保为特定类型的参数生成正确的代码。
>
> 模板参数实例化分为：**隐式实例化和显式实例化**

##### **隐式实例化**

**指的是在代码中调用函数模板时，编译器会根据传递的参数类型隐式地生成特定数据类型的函数实现。这是函数模板常见的实例化方式。**

```cpp
template <typename T>
T Max(T a, T b) {
    return a > b ? a : b;
}

int main() {
    int result = Max(5, 10); // 隐式实例化为 int Max(int a, int b)
    return 0;
}
```

假设我们调用 `Max(5, 10)`，编译器会执行以下步骤来进行函数模板实例化：

1. 编译器看到调用 `Max(5, 10)`，需要实例化函数模板 `Max`。
2. 编译器分析参数 `5` 和 `10`，确定它们的类型为 `int`。
3. 编译器将模板参数 `T` 替换为 `int`，生成如下的特定实现：

> **图片未上传**

 **那么我这样调用呢？**

```cpp
int a = 2;
double b = 3.0;

Max(a, b);
```

**问题本质**

**该语句不能通过编译**，当编译器遇到多个不同类型的实参时，需要确定一个适合的模板参数类型。然而，在某些情况下，可能出现无法明确确定模板参数类型的情况，因为模板参数列表中只有一个模板参数，但实际实参可能是不同的类型。

**类型推断和类型转换**

在模板实例化过程中，编译器通常不会进行隐式的类型转换，因为这可能导致不明确的结果。例如，在您的例子中，编译器不知道应该将模板参数 `T` 推断为 `int` 还是 `double`，因此无法进行正确的实例化。

**注意：在模板中，编译器一般不会进行类型转换操作**

此时有两种常用处理方式：

1. 用户强制转化
2. 显式实例化

**强制转换**

```cpp
Max(a, (int)b);

//或者

Max((double)a, b);
```

##### 显示实例化

> **在函数名后的<>中指定模板参数的实际类型，可以显式告知编译器实例化的类型，以避免模板参数推断的问题**

```cpp
Max<int>(a, b);

//或者

Max<double>(a, b);
```

**如果类型不匹配，编译器会尝试进行隐式类型转换，如果无法转换成功编译器将会报错**。

#### 2.2.5 模板参数的匹配原则

当存在非模板函数和同名函数模板时，编译器在进行函数调用时会根据匹配规则选择合适的函数。具体情况如下：

> 1. **优先选择非模板函数：**
>
>    ​	如果存在一个与实际参数类型完全匹配的非模板函数，那么编译器会优先选择调用这个非模板函数，因为它在类型匹配上更为特化。
>
> 2. **模板参数匹配：**
>
>    ​	如果不存在与实际参数类型完全匹配的非模板函数，编译器会尝试进行模板参数匹配，查找最匹配的函数模板。
>
> 3. **更好的匹配：**
>
>    ​	如果模板函数能够生成更好匹配的实例，编译器会选择调用这个模板函数。例如，如果模板参数可以通过隐式类型转换更好地匹配实际参数，那么模板将被选中。

**我们来看一看例子**

```cpp
#include <iostream>

// 通用输出函数模板
template<class T>
void Print(T value) {
    std::cout << value << std::endl;
}

// 重载的输出函数，专门处理 const char* 类型
void Print(const char* value) {
    std::cout << "String: " << value << std::endl;
}

int main() {
    Print(42);          // 调用通用函数模板 Print<T>
    Print("Hello");     // 调用重载函数 Print(const char*)
    Print(3.14);        // 调用通用函数模板 Print<T>
    Print<const char*>("Hello");    //显示调用通用函数模板 Print<T>

    return 0;
}
```

1. `template<class T> void Print(T value)` 是一个通用的输出函数模板，用于输出不同类型的数据。
2. `void Print(const char* value)` 是一个专门处理 `const char*` 类型的输出函数重载。
3. 在 `main` 函数中，调用 `Print(42)` 会匹配到通用函数模板，因为类型匹配。
4. 调用 `Print("Hello")` 会匹配到重载函数，因为 `const char*` 更为特化。
5. 调用 `Print(3.14)` 仍会匹配到通用函数模板。
6. 显示调用模板，Print<const char*>("Hello")显然会调用函数模板。

**再来一个例子**

```cpp
#include <iostream>

// 专门处理 int 的加法函数
int Add(int left, int right) {
    return left + right;
}

// 通用加法函数模板
template<class T1, class T2>
T1 Add(T1 left, T2 right) {
    return left + right;
}

void Test() {
    Add(1, 2);       // 调用非函数模板，与 int Add(int left, int right) 匹配
    Add(1, 2.0);     // 调用函数模板，生成更匹配的版本
}
```

1. `int Add(int left, int right)` 是一个专门处理 `int` 类型的加法函数。
2. `template<class T1, class T2> T1 Add(T1 left, T2 right)` 是一个通用的加法函数模板，可以适用于不同类型的数据。
3. 在调用 `Add(1, 2)` 时，编译器会选择调用非函数模板，因为它与实际参数类型完全匹配。
4. 在调用 `Add(1, 2.0)` 时，编译器会选择调用函数模板。虽然非函数模板也可以匹配，但是函数模板可以生成更匹配的版本，编译器会根据实际参数生成更匹配的 `Add` 函数。

### 2.3 类模板

#### 2.3.1 概念

> 当涉及到类模板时，就是在创建一个可以根据不同的数据类型生成不同类的模板。类模板允许您编写通用的类定义，以便适用于多种数据类型，而不必为每种类型编写单独的代码。

#### 2.3.2 语法

类模板的语法类似于函数模板，但是应用于类的定义。以下是一个简单的类模板示例：

```cpp
template <class T>
class MyContainer {
private:
    T value;

public:
    MyContainer(T val) : value(val) {}

    T GetValue() {
        return value;
    }
};
```

#### 2.3.3 示例

```cpp
int main() {
    MyContainer<int> intContainer(42);
    MyContainer<double> doubleContainer(3.14);

    std::cout << intContainer.GetValue() << std::endl;    // 输出: 42
    std::cout << doubleContainer.GetValue() << std::endl; // 输出: 3.14

    return 0;
}
```

#### 2.3.4 注意事项

1. **成员函数定义：** 类模板的成员函数通常也需要在模板类内定义，否则需要在定义外使用 `template` 关键字进行模板声明和实现。
2. **模板参数推导：** 在实例化类模板时，编译器可以自动推导模板参数的类型，也可以使用显式指定的方式进行实例化。
3. **模板特化：** 类模板也可以进行特化，针对特定类型提供自定义实现，类似于函数模板的模板特化。
4. **实例化时代码生成：** 类模板实例化时，编译器会根据实际的类型参数生成相应的类定义，从而创建特定类型的类。

#### **2.3.5 解释注意事项**

##### **1. 成员函数定义：**

在类模板中，成员函数可以在模板类内部定义，也可以在类外部使用 `template` 关键字进行模板声明和实现。

```cpp
template <class T>
class MyContainer {
private:
    T value;

public:
    MyContainer(T val) : value(val) {}

    T GetValue() {
        return value;
    }
};

// 在类外部定义成员函数模板
template <class T>
T MyContainer<T>::GetValue() {
    return value * 2;
}
```

##### **2. 模板参数推导：**

编译器在实例化类模板时可以自动推导模板参数的类型，也可以使用显式指定的方式进行实例化。

```cpp
int main() {
    MyContainer intContainer(42);
    MyContainer<double> doubleContainer(3.14);

    std::cout << intContainer.GetValue() << std::endl;    // 输出: 42
    std::cout << doubleContainer.GetValue() << std::endl; // 输出: 3.14

    return 0;
}
```

##### **3. 模板特化：**

类模板也可以进行特化，为特定类型提供自定义实现，类似于函数模板的模板特化。

```cpp
// 类模板定义
template <class T>
class MyContainer {
private:
    T value;

public:
    MyContainer(T val) : value(val) {}

    T GetValue() {
        return value;
    }
};

// 类模板的特化版本
template <>
class MyContainer<int> {
private:
    int value;

public:
    MyContainer(int val) : value(val) {}

    int GetValue() {
        return value * 2; // 自定义的实现
    }
};
```

## **三、模板分文件编写（特殊）**

> 与平时代码分文件编写不同，模板的分文件编写涉及到一些特殊的注意事项和方法，以确保模板的正确实例化和链接。我将为您提供一个简单的示例，演示如何将模板分为头文件和源文件。

**示例：**

假设我们有一个类模板 `MyTemplate`，其中包含成员函数，我们希望将其分为头文件和源文件。

**MyTemplate.h（头文件）：**

```cpp
#ifndef MYTEMPLATE_H
#define MYTEMPLATE_H

template <typename T>
class MyTemplate {
public:
    MyTemplate(T value);
    void PrintValue();
    
private:
    T data;
};

#endif
```

**MyTemplate.cpp（源文件）：**

```cpp
#include <iostream>
#include "MyTemplate.h"

template <typename T>
MyTemplate<T>::MyTemplate(T value) : data(value) {}

template <typename T>
void MyTemplate<T>::PrintValue() {
    std::cout << "Value: " << data << std::endl;
}

// 显式实例化模板
template class MyTemplate<int>;
template class MyTemplate<double>;
```

**main.cpp（主文件）：**

```cpp
#include "MyTemplate.h"

int main() {
    MyTemplate<int> intObj(42);
    MyTemplate<double> doubleObj(3.14);
    
    intObj.PrintValue();
    doubleObj.PrintValue();
    
    return 0;
}
```

在源文件 `MyTemplate.cpp` 中，我们**提供了类模板的实现**，包括构造函数和成员函数的定义。我们还在源文件中使用了**显式实例化**（`template class MyTemplate<int>;` 和 `template class MyTemplate<double>;`），以确保在编译期间生成特定类型的模板实例。

最后，在主文件 `main.cpp` 中，我们只需要包含头文件 `MyTemplate.h` 并使用类模板。编译时，编译器会将类模板的实现部分从 `MyTemplate.cpp` 中提取出来并进行实例化。

虽然这种分文件编写模板的方式需要一些额外的步骤，但它确实可以使代码更有组织性和可维护性。

**进一步解释**

> 在源文件 `MyTemplate.cpp` 中使用显式实例化是为了确保编译器在编译期间生成特定类型的模板实例化代码。虽然函数模板的定义通常放在头文件中，但由于 C++ 的分离编译模型，模板的实现必须位于同一个编译单元中，以便编译器能够在需要时进行实例化。
>
> 在类模板的实现中，由于模板参数可能是多种不同的类型，编译器不会自动为每种类型生成实例化代码，除非在需要的地方显式使用模板。这就是使用显式实例化的原因：您告诉编译器为特定的类型生成实例化代码，以确保在链接时能够找到正确的模板实现。

**函数分文件编写亦是如此原理！！！**

**函数模板初阶到此结束！笔者会继续更新进阶模板教程！！**