---
title: 入门基础（下）
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-32.webp
description: "\U0001F3A9C++特色：auto魔法、范围for循环妙用、nullptr指针巧思。"
mathjax: true
tags:
  - CPP基础
categories:
  - CPP
abbrlink: '96871e07'
date: 2023-11-23 00:00:00
updated: 2023-11-23 00:00:00
swiper_index:
---
##  一、 auto关键字(C++11)

### 1.1 引入

> 随着学习的深入，程序和用到的类型也越来越复杂，例如：**类型难于拼写/含义不明确导致容易出错。**

**eg：**

```cpp
#include <string>
#include <map>

int main() {
    std::map<std::string, std::string> m{{"apple", "苹果"},
                                         {"orange","橙子"},
                                         {"pear",  "梨"}};
    std::map<std::string, std::string>::iterator it = m.begin();
    while (it != m.end()) {
        //....
    }
    return 0;
}
```

**std::map<std::string, std::string>::iterator**是一个类型，但是该类型太长特别容易写错，用起来相对较麻烦。

### 1.2 简介

> 在**早期C/C++**中auto的含义是：使用auto修饰的变量，是**具有自动存储器的局部变量**，但遗憾的 是一直没有人去使用它，大家可思考下为什么？
>
> C++11中，标准委员会赋予了auto全新的含义即：auto不再是一个存储类型指示符，而是作为一 个新的类型指示符来指示编译器，auto声明的变量必须由编译器在编译时期推导而得。

```cpp
#include <iostream>
#include <typeinfo>
using namespace std;

int TestAuto()
{
    return 10;
}

int main() {
    int a = 10;
    auto b = a;
    auto c = 'a';
    auto d = TestAuto();
    cout << typeid(b).name() << endl;
    cout << typeid(c).name() << endl;
    cout << typeid(d).name() << endl;
    //auto e; 无法通过编译，使用auto定义变量时必须对其进行初始化
    return 0;
}
```

> 在C++中，`typeid`是一个运算符，用于获取表达式的类型信息。当你调用`typeid(d).name()`时，它将返回一个表示表达式`d`的类型的字符串。这个字符串通常是编译器特定的，可能不易于阅读，但可以用于调试或其他目的。
>
> 要使用`typeid`，你需要包含头文件`<typeinfo>`。

提醒：使用auto定义变量时必须对其进行初始化，在编译阶段编译器需要根据初始化表达式来推导auto 的实际类型。因此auto并非是一种“类型”的声明，而是一个类型声明时的“占位符”，编译器在编 译期会将auto替换为变量实际的类型。

### 1.3 使用细则

#### 1.3.1 auto与指针和引用结合使用

> 用auto声明指针类型时，用**auto**和**auto\***没有任何区别，但用auto声明引用类型时则必须加**&**

```cpp
int main() {
    int x = 10;
    auto a = &x;
    auto *b = &x;
    auto &c = x;
    cout << typeid(a).name() << endl;
    cout << typeid(b).name() << endl;
    cout << typeid(c).name() << endl;
    *a = 20;
    *b = 30;
    c = 40;
    return 0;
}
```

> 此处会推导出**a**与**b**均为**int\***类型，而**c**为**int**类型

#### 1.3.2 在同一行定义多个变量

> 当在同一行声明多个变量时，这些变量必须是相同的类型，否则编译器将会报错。
>
> 因为编译器实际只对第一个类型进行推导，然后用推导出来的类型定义其他变量。

```cpp
void TestAuto()
{
    auto a = 1, b = 2;
    auto c = 3, d = 4.0;  // 该行代码会编译失败，因为c和d的初始化表达式类型不同
}
```

#### 1.3.3 其他应用

auto在实际中最常见的优势用法就是跟以后会讲到的C++11提供的新式for循环，还有 lambda表达式等进行配合使用。

#### 1.3.4 C++11 auto

> 为了避免与C++98中的auto发生混淆，C++11只保留了auto作为类型指示符的用法。

### 1.4 auto不能推导的场景

#### 1.4.1 auto不能作为函数的参数

```cpp
// 此处代码编译失败，auto不能作为形参类型，因为编译器无法对a的实际类型进行推导
void TestAuto(auto a)
{}
```

> C++标准规定，函数的形参类型必须是显式指定的类型，而不是使用类型推导。因此，像这样使用`auto`作为函数形参类型的代码将导致编译失败。

#### 1.4.2 . auto不能直接用来声明数组

```cpp
void TestAuto()
{
    int a[] = {1,2,3};
    auto b[] = {4，5，6};
}
```

在C++中，使用`auto`来声明变量时，编译器会根据初始化表达式的类型来推导变量的类型。

然而，`auto b[] = {4, 5, 6};` 这样的语法是不允许的。

对于这种情况，编译器无法推导出数组`b`的类型，因为初始化表达式`{4, 5, 6}`是一个初始化列表，而不是一个具体的数组类型。在使用`auto`时，编译器需要明确知道变量的类型，以便正确地分配内存和进行类型检查。

## 二、基于范围的for循环(C++11)

> C++11引入了一种新的for循环语法，称为基于范围的for循环（Range-based for loop）。它允许你更方便地遍历容器中的元素，不再需要使用迭代器或索引。这种循环适用于所有支持迭代器的容器，如数组、向量、列表、集合等。

### 2.1 基本语法

```cpp
for (element_type variable : container) {
    // 循环体，使用 variable 访问容器中的元素
}
```

其中，`element_type`是容器中元素的类型，`variable`是你在循环中使用的变量名，`container`是要遍历的容器。

举个例子，我们使用基于范围的for循环来遍历一个向量（vector）：

```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};

    // 使用基于范围的for循环遍历向量
    for (int num : numbers) {
        std::cout << num << " ";
    }

    return 0;
}
```

在这个例子中，我们声明了一个名为`numbers`的向量，然后使用基于范围的for循环遍历该向量中的元素，并将每个元素打印到标准输出。

**基于范围的for循环在代码可读性和简洁性方面非常有优势，特别是对于遍历容器中所有元素的情况。然而，如果需要在循环体内访问索引或迭代器，仍然需要使用传统的for循环。**

**看到这里由于读者可能没有接触过容器，笔者再使用数组示例：**

```cpp
#include <iostream>

int main() {
    int arr[] = {1, 2, 3, 4, 5};

    // 使用基于范围的for循环遍历数组
    for (int num : arr) {
        std::cout << num << " ";
    }

    return 0;
}
```

输出将是：`1 2 3 4 5`

在上述例子中，我们声明了一个名为`arr`的数组，并使用基于范围的for循环遍历该数组中的元素，并将每个元素打印到标准输出。

需要注意的是，在使用基于范围的for循环遍历数组时，循环变量的类型必须与数组元素的类型**匹配**，否则会导致编译错误。

### 2.2 使用条件

**for循环迭代的范围必须是确定的**

对于数组而言，就是数组中第一个元素和最后一个元素的范围；对于类而言，应该提供 begin和end的方法，begin和end就是for循环迭代的范围。

示例：

```cpp
void TestFor(int array[])
{
    for(auto& e : array)
        cout<< e <<endl;
}
```

 以上代码就有问题，因为数组传进来以后，array不再是数组，而是指针，导致for的范围不确定。

## 三、指针空值nullptr(C++11)

### 3.1 C++98中的指针空值

> 在良好的C/C++编程习惯中，声明一个变量时最好给该变量一个合适的初始值，否则可能会出现不可预料的错误，比如**未初始化的指针**。如果一个指针没有合法的指向，我们基本都是按照如下方式对其进行初始化：

> 在C++11及更高版本中，`nullptr`是一个关键字，用于表示空指针常量。在早期的C++版本使用`NULL`来表示空指针，但这会导致一些潜在的问题，因为`NULL`实际上被定义为0，这可能导致在某些上下文中发生类型转换问题。
>
> `nullptr`的引入解决了这个问题。它是一个字面量，表示空指针，且没有特定的整数类型使用`nullptr`可以避免空指针的类型转换问题，并且可以更明确地表示空指针。

```cpp
void TestPtr()
{
    int* p1 = NULL;
    int* p2 = 0;
// ……
}
```

NULL实际是一个宏，在传统的C头文件(stddef.h)中，可以看到如下代码：

```cpp
#ifndef NULL
#ifdef __cplusplus
#define NULL   0
#else
#define NULL   ((void *)0)
#endif
#endif
```

可以看到，NULL可能被定义为字面常量0，或者被定义为无类型指针(void*)的常量。不论采取何 种定义，在使用空值的指针时，都不可避免的会遇到一些麻烦，比如:

```cpp
void f(int) {
    cout << "f(int)" << endl;
}

void f(int *) {
    cout << "f(int*)" << endl;
}

int main() {
    f(0);
    f(NULL);
    f((int *) NULL);
    return 0;
}
```

程序本意是想通过f(NULL)调用指针版本的f(int*)函数，但是由于NULL被定义成0，因此与程序的 初衷相悖。

在C++98中，字面常量0既可以是一个整形数字，也可以是无类型的指针(void*)常量，但是编译器 **默认情况**下将其看成是一个整形常量，如果要将其按照指针方式来使用，必须对其进行**强转(void \*)0**。

### 3.2 注意事项

1. 1. 在使用nullptr表示指针空值时，不需要包含头文件，因为nullptr是C++11作为新关键字引入 的。
2. 2. 在C++11中，sizeof(nullptr) 与 sizeof((void*)0)所占的字节数相同
3. 3. 为了提高代码的健壮性，在后续表示指针空值时建议最好使用nullptr。

**至此，C++入门篇到此结束！！**