---
title: 内存管理
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-25.webp
description: "\U0001F4E6栈堆大战，内存乐无穷。"
mathjax: true
tags:
  - 内存管理
categories:
  - CPP
abbrlink: 41e39f2b
date: 2023-11-26 00:00:00
updated: 2023-11-26 00:00:00
swiper_index:
---
## 引言

> 在计算机编程领域，内存管理是一项关键的任务，特别是在低级编程语言如C和C++中。良好的内存管理实践可以确保程序的性能和稳定性。本文将深入探讨C/C++内存管理的各个方面，**帮助读者更好地理解如何正确地分配、释放内存，并通过优化策略来改进程序。**

## 	一、栈与堆内存

### 1.1 栈内存

栈内存是一种存储在计算机内存中的区域，用于**管理局部变量和函数调用**。栈内存的工作方式如下：

- **自动管理**：栈内存的分配和释放是由编译器自动处理的。当进入一个函数时，编译器会在栈上为其局部变量分配内存，当函数退出时，这些局部变量所占用的内存会被自动释放。这种自动管理机制确保了局部变量的生命周期与函数调用的生命周期相匹配。
- **速度快**：由于栈内存的管理是编译器自动完成的，栈上的内存分配和释放操作非常快速。这使得栈内存适合用于临时性的、短时间存在的变量。
- **大小有限**：栈的大小通常是固定的，这取决于操作系统和编译器的设置。这意味着栈内存的容量有限，过多的局部变量可能导致栈溢出。
- **后进先出（LIFO）**：栈内存遵循后进先出的原则。也就是说，**最后分配的局部变量会首先被释放。**

栈内存通常用于存储函数的参数、局部变量和函数调用信息。它在处理函数调用和返回时起到了重要的作用。

### 1.2 堆内存

堆内存是在程序运行**时动态分配**的一块内存区域，用于存储数据结构和对象。堆内存的特点包括：

- **手动管理**：与栈不同，堆内存的分配和释放需要程序员显式地进行。在C中，使用`malloc`函数分配堆内存，而在C++中，可以使用`new`运算符来实现。而释放堆内存则需要使用相应的函数（`free`和`delete`）来手动释放，以**避免内存泄漏**。
- **灵活大小**：堆内存的大小没有固定限制，允许在运行时动态分配内存。这使得堆内存**适用于需要存储大小不确定的数据结构**，如动态数组和复杂的对象。
- **速度较慢**：由于堆内存的分配和释放需要程序员显式地操作，操作速度相对较慢。**频繁的堆内存分配和释放可能会影响程序的性能**。
- **注意内存泄漏**：由于堆内存需要手动释放，如果在不再需要内存块时没有正确释放它，就会发生内存泄漏。内存泄漏会导致程序占用越来越多的内存，最终可能导致崩溃。

堆内存通常用于存储具有长生命周期、大小不确定或需要动态分配的数据。

### 1.3 示例

**让我们看一些例子：**

**C示例**

**C++示例**

 **两者区别仅在与开辟空间与释放空间的方式不同，但基本逻辑类似！！**

## **二 、C语言内存管理方式**

> 当涉及到C语言中的动态内存管理函数（`malloc`、`calloc`、`realloc`和`free`）时，理解其用法和原理非常重要。以下是每个函数的详细介绍、使用示例以及内部工作原理的剖析。

### 2.1 malloc函数

####  介绍

> `malloc` 函数用于分配指定大小的内存块，返回一个指向分配内存的指针。它分配的内存中的初始值是**未定义的**，通常为**垃圾值**。如果分配失败，返回 **`NULL`**。

#### 用法示例

```objectivec
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *ptr;

    ptr = (int *)malloc(sizeof(int));  // 分配一个 int 大小的内存块

    if (ptr != NULL) {
        *ptr = 42;
        printf("Value: %d\n", *ptr);

        free(ptr);  // 释放分配的内存
    }

    return 0;
}
```

#### **原理剖析**

> `malloc` 函数根据请求的大小在堆内存中分配一块连续的内存区域。它会返回一个指向分配内存区域起始地址的指针。当调用 `malloc` 时，系统会搜索合适的空闲内存块，并将其标记为已占用，以便将来的分配不会重叠。

### 2.2 calloc函数

#### 介绍

> `calloc` 函数用于分配指定数量和大小的内存块，并**将分配的内存初始化为零**。它接受两个参数，即**所需内存块的数量和每个块的字节数**，返回一个指向分配内存的指针。如果分配失败，返回 `NULL`。

#### 用法示例

```objectivec
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *ptr;

    ptr = (int *)calloc(1, sizeof(int));  // 分配一个 int 大小的内存块，初始化为零

    if (ptr != NULL) {
        printf("Value: %d\n", *ptr);

        free(ptr);  // 释放分配的内存
    }

    return 0;
}
```

#### 原理剖析

> `calloc` 函数在堆内存中分配一块连续的内存区域，其大小为所需内存块的数量乘以每个块的字节数。**分配完成后，系统会将分配的内存区域中的所有字节设置为零**。

### 2.3 realloc函数

#### 介绍

> `realloc` 函数用于重新分配已分配内存的大小。它接受一个指向已分配内存的指针和所需内存的字节数，返回一个指向重新分配后内存的指针。如果重新分配失败，返回 `NULL`。在调用 `realloc` 后，原始指针可能会被释放，因此应使用返回的新指针。

#### **解释**

> - **函数原型：**`void *realloc(void *ptr, size_t size);`
> - **参数 `ptr`：**一个指向之前由 `malloc`、`calloc` 或 `realloc` 分配的内存块的指针。如果 `ptr` 是一个空指针（即 `NULL`），则 `realloc` 的行为就相当于 `malloc`。
> - **参数 `size`：**要重新分配的内存块的新大小，以字节为单位。

#### 作用

> 1. 如果传入的 `ptr` 指针是 `NULL`，那么 `realloc` 将表现得和 `malloc` 一样，分配一个新的内存块，并返回指向这块内存的指针。
> 2. 如果传入的 `size` 参数为 0，那么 `realloc` 的行为会根据不同的实现而有所不同，但通常会释放 `ptr` 指向的内存块，并返回一个空指针。这可以用来释放已分配的内存块。
> 3. 如果传入的 `ptr` 指针不是 `NULL`，且 `size` 大于 0，`realloc` 将尝试重新分配 `ptr` 指向的内存块，使其大小为 `size` 字节。这可能导致以下情况之一：
>    - 如果原内存块的大小足够容纳新的大小，那么该内存块将被重新分配，不会改变其地址，`realloc` 返回原指针。
>    - 如果原内存块的大小不够容纳新的大小，`realloc` 将尝试在内存中找到足够大的块来容纳新大小的数据，然后将原数据复制到新分配的内存块中，原内存块将被释放，`realloc` 返回指向新分配内存的指针。

**需要注意的是，`realloc` 在重新分配内存块的过程中可能会导致数据的复制和内存块的移动，因此在性能敏感的场景中需要谨慎使用**。同时，使用 `realloc` 后，**原指针可能已经失效**，应始终使用 `realloc` 的返回指针来访问重新分配的内存。

#### 用法示例

```objectivec
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *ptr;

    ptr = (int *)malloc(sizeof(int));  // 分配一个 int 大小的内存块

    if (ptr != NULL) {
        *ptr = 42;

        ptr = (int *)realloc(ptr, sizeof(int) * 2);  // 重新分配为 2 个 int 大小的内存块
        if (ptr != NULL) {
            printf("Value: %d\n", *ptr);

            free(ptr);  // 释放分配的内存
        }
    }

    return 0;
}
```

#### 原理剖析

> `realloc` 函数重新分配已分配内存的大小。如果新大小比原大小小，可能会缩小内存块。如果新大小比原大小大，系统可能会在原内存块旁边或其他合适位置重新分配更大的内存块，并将数据从原内存块复制到新的内存块。如果重新分配失败，原内存块将保持不变。

### 2.4 free函数

#### 介绍

> `free` 函数用于释放之前通过 `malloc`、`calloc` 或 `realloc` 分配的内存。释放内存后，指向该内存的指针变为无效，不应再被使用。

#### 用法示例

```objectivec
#include <stdlib.h>

int main() {
    int *ptr;

    ptr = (int *)malloc(sizeof(int));  // 分配一个 int 大小的内存块

    if (ptr != NULL) {
        *ptr = 42;

        free(ptr);  // 释放分配的内存
    }

    return 0;
}
```

#### 原理剖析

> `free` 函数将之前通过动态内存分配函数分配的内存块标记为空闲，以便在将来的分配中可以重复使用。被释放的内存不会立即返回给操作系统，而是留在堆中，供以后的 `malloc` 或 `calloc` 调用使用。

总之，动态内存管理函数（`malloc`、`calloc`、`realloc` 和 `free`）允许在运行时分配和释放内存，从而实现更灵活的内存使用。但要确保适时释放内存，避免内存泄漏和悬挂指针问题。同时，要注意重新分配内存时可能会导致数据的复制，可能会影响性能。

### 2.5 悬挂指针

#### 概念

> 悬挂指针（Dangling Pointer）是指指向已释放或无效内存的指针。当一个指针指向已经释放的内存块或者指向的对象不再有效时，这个指针就变成了悬挂指针。访问悬挂指针可能导致程序崩溃、未定义的行为或者不可预测的结果。

#### **产生的原因**

**1.释放已分配的内存后未置空指针：** 在使用 `free` 或 `delete` 释放内存后，如果不将指针设置为 `NULL`，指针仍然保持之前的地址，但是指向的内存已经无效。这样的指针就是悬挂指针。

```objectivec
int *ptr = (int *)malloc(sizeof(int));
free(ptr);  // 内存释放
// 这时 ptr 是一个悬挂指针
```

**2.函数返回局部变量的指针：** 如果一个函数返回了一个指向其**局部变量**的指针，并且在函数返回后尝试使用该指针，就会得到一个悬挂指针，因为局部变量的生命周期在函数返回后结束。

```objectivec
int *getLocalPtr() {
    int x = 10;
    return &x;  // 返回局部变量的指针
}

int main() {
    int *ptr = getLocalPtr();  // ptr 变成悬挂指针
    // 后续对 ptr 的使用将导致未定义行为
    return 0;
}
```

## 三、 C++内存管理方式

> 当谈到C++编程中的动态内存管理时，"new"和"delete"是两个非常重要的关键词。它们用于在程序运行时分配和释放内存，以便在堆（heap）上创建和销毁对象。以下是关于"new"和"delete"的详细解释：

### 3.1 new操作符

"new" 是C++中用于在堆上动态分配内存的操作符。它的基本语法如下： 

```cpp
T *pointer = new T;
```

其中，`T` 是要分配内存的数据类型，`pointer` 是指向分配内存的指针。"new" 操作符会执行以下步骤：

- 分配足够大小的内存以存储类型 `T` 的对象。
- 调用 `T` 类型的构造函数来初始化新对象。
- 返回指向新对象的指针。

**示例**

```cpp
int* numPtr = new int;	// 分配一个int大小的内存块，并返回它的指针
```

### 3.2 delete操作符

"delete" 是用于释放由 "new" 分配的内存的操作符。它的语法如下：

```cpp
delete pointer;
```

其中，`pointer` 是通过 "new" 分配的指针。"delete" 操作符会执行以下步骤：

- 调用指向对象的析构函数来清理资源。
- 释放对象所占用的内存。
- 将指针置为无效，以避免对已释放内存的访问。

**示例**

```cpp
delete numPtr;	// 释放之前分配的int内存块
```

### **3.3 注意事项**

> - 必须对每个 "new" 调用使用对应的 "delete" 操作来释放内存，以避免内存泄漏。
> - 不要尝试对相同的指针多次调用 "delete"，这可能会导致未定义的行为。
> - 为了避免悬挂指针（dangling pointers），在释放内存后将指针设置为 nullptr。

C++11 引入了 "new" 和 "delete" 的替代品：**"new[]" 和 "delete[]"**，它们用于分配和释放数组。例如：

```cpp
int* arrPtr = new int[10];	//分配一个包含 10 个 int 元素的数组
delete[] arrPtr;	//释放数组内存
```

>  然而，更好的做法是在现代C++中使用智能指针（如`std::unique_ptr`、`std::shared_ptr`），以减少手动内存管理的复杂性和风险。这些智能指针可以在超出作用域时自动释放内存。

### 3.4 new/delete操作内置类型

> "new" 和 "delete" 操作符在处理内置类型（如整数、浮点数等）时的用法与处理自定义类型类似。以下是关于如何使用 "new" 和 "delete" 操作符来处理内置类型的示例：

#### **1. 使用 new 分配内存：**

```cpp
int* intPtr = new int;	// 分配一个 int 大小的内存块
double* doublePtr = new double; // 分配一个 double 大小的内存块
```

#### **2. 使用 delete 释放内存：**

```cpp
delete intPtr;	//放之前分配的 int 内存块
delete doublePtr;	// 释放之前分配的 double 内存块
```

需要注意的是，"new" 操作符分配的内存需要通过相应的 "delete" 操作符来释放，以避免内存泄漏。此外，如果你在分配内存后忘记调用 "delete"，就会导致内存泄漏。

然而，在处理内置类型时，通常更推荐使用栈上的自动存储（automatic storage），这意味着变量会在其所在作用域结束时自动释放。这样可以避免手动内存管理的复杂性和风险。例如：

```cpp
int intValue;	//在栈上分配一个 int 变量，无需手动释放
double doubleValue;	// 在栈上分配一个 double 变量，无需手动释放
```

### 3.5 new和delete操作自定义类型

> 在 C++ 中，你可以使用 "new" 和 "delete" 操作符来动态分配和释放自定义类型的内存，即用户自定义的类对象。以下是如何在处理自定义类型时使用 "new" 和 "delete" 操作符的示例：

```cpp
class Person {
public:
    Person(const std::string& name, int age) : name(name), age(age) {}
    void DisplayInfo() const {
        std::cout << "Name: " << name << ", Age: " << age << std::endl;
    }
private:
    std::string name;
    int age;
};
```

**1. 使用 new 分配自定义类型对象：**

```cpp
Person* personPtr = new Person("Alice"，25); // 分配一个 Person 对象
```

 **2. 使用 delete 释放自定义类型对象：**

```cpp
delete personPtr;	//释放之前分配的 Person 对象
```

**需要注意以下两点：**

- "new" 操作符会**调用类的构造函数**来初始化对象。
- "delete" 操作符会调**用类的析构函数**来清理资源并释放内存。

然而，为了更好地管理内存，现代 C++ 推荐使用智能指针来代替显式的 "new" 和 "delete" 操作。下面是使用 `std::unique_ptr` 来处理自定义类型对象的示例：

```cpp
#include <memory>

// ...

std::unique_ptr<Person> personPtr = std::make_unique<Person>("Bob", 30); 
 // 使用 unique_ptr 分配一个 Person 对象
```

在这种情况下，当 `personPtr` 超出作用域时，关联的内存会自动释放，无需手动调用 "delete"。

**如果多个智能指针共享一个对象，你可以使用 `std::shared_ptr`：**

```cpp
#include <memory>

// ...

std::shared_ptr<Person> sharedPersonPtr = std::make_shared<Person>("Charlie", 40);  
// 使用 shared_ptr 分配一个 Person 对象
```

**使用智能指针能够避免许多手动内存管理的问题，并提供更好的内存安全性。**

## 四、new和delete的实现原理

> 当我们在C++中使用动态内存分配和释放时，如 `new` 和 `delete` 操作符，实际上是调用了C++标准库中定义的一组用于内存分配和释放的函数。这些函数被称为 "全局配置的分配函数"，并可以通过包含 `<new>` 头文件来访问。

### **4.1. `operator new` 和 `operator delete`：**

这对函数用于单个对象的内存分配和释放。它们的原型分别为：

```cpp
void* operator new(std::size_t size);
void operator delete(void* ptr) noexcept;
```

> - `operator new`：这个函数用于在堆上分配指定大小的内存块，并返回一个指向分配内存的指针。它会考虑内存对齐等底层细节。如果分配失败，它可以抛出 `std::bad_alloc` 异常。
> - `operator delete`：这个函数用于释放之前通过 `operator new` 分配的内存块。它接受一个指向内存块的指针作为参数，释放内存后可以执行一些必要的清理工作。

**示例用法**

```cpp
#include <new>

int* ptr = new int; // 使用 operator new 分配一个 int 大小的内存块
delete ptr;	// 使用 operator delete 释放之前分配的内存块
```

###  4.2 **`operator new[]` 和 `operator delete[]`：**

这对函数用于分配和释放数组内存。它们的原型分别为：

```cpp
void* operator new[](std::size_t size);
void operator delete[](void* ptr) noexcept;
```

> - `operator new[]`：这个函数用于在堆上分配指定大小的数组内存，并返回一个指向分配内存的指针。它与 `operator new` 类似，但用于数组分配。
> - `operator delete[]`：这个函数用于释放之前通过 `operator new[]` 分配的数组内存。它接受一个指向内存块的指针作为参数，释放内存后可以执行一些必要的清理工作。

**示例用法**

```cpp
#include <new>

int arrPtr = new int[10]; // 使用 operator new[] 分配一个包含 10 个 int 元素空间
delete[] arrPtr; // 使用 operator delete[] 释放数组内存
```

> 这些函数通常会调用底层的内存分配器（例如操作系统提供的分配器）来执行实际的分配和释放操作。它们在多线程环境中通常会保证一些基本的线程安全性。
>
> 需要注意的是，虽然这些函数在大多数情况下都能很好地工作，但在某些特殊情况下，你可能会考虑重载这些函数来实现自定义的内存管理逻辑，如实现内存池或进行内存使用跟踪。

## 4.3 补充

> 在 C++ 中，`new` 和 `delete` 操作符主要用于动态分配和释放类类型对象的内存。当然，它们也可以用于分配和释放内置数据类型（如整数、浮点数等）的内存，但在大多数情况下，内置数据类型通常在栈上自动分配和释放。

### **1. `new` 的类类型处理：**

当使用 `new` 操作符创建类类型的对象时，它会执行以下步骤：

1. 分配足够大小的内存以存储类类型的对象。
2. 调用类的构造函数来初始化新对象。
3. 返回指向新对象的指针。

#### **代码示例**

```cpp
class MyClass {
public:
    MyClass() { std::cout << "Constructor called." << std::endl; }
    ~MyClass() { std::cout << "Destructor called." << std::endl; }
};

MyClass* objPtr = new MyClass;  // 使用 new 创建一个 MyClass 对象
delete objPtr;                  // 使用 delete 释放 MyClass 对象
```

###  **2. `delete` 的类类型处理：**

当使用 `delete` 操作符释放类类型的对象时，它会执行以下步骤：

1. 调用类的析构函数来清理对象资源和执行必要的清理工作。
2. 释放对象占用的内存。

#### 代码示例

```cpp
MyClass* objPtr = new MyClass;  // 使用 new 创建一个 MyClass 对象
delete objPtr;                  // 使用 delete 释放 MyClass 对象
```

**需要注意的是，这些步骤确保了在动态分配内存的情况下，对象在适当的时候得以正确构造和析构。对于内置数据类型，通常不需要显式使用 `new` 和 `delete`，因为它们通常会在栈上自动分配和释放。**

**无论是使用 `new` 还是 `new[]`，以及使用 `delete` 还是 `delete[]`，都会为数组中的每个对象分别调用构造函数和析构函数，以确保对象在正确的时机得以构造和析构。**