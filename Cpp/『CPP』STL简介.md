---
title: C++STL初阶
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-22.webp
description: "\U0001F680C++STL：编程的魔法仙境，数据奇幻冒险，轻松高效！"
mathjax: true
tags:
  - CPP语言特性
categories:
  - CPP
abbrlink: 82ac79c6
date: 2023-11-21 00:00:00
updated: 2023-11-21 00:00:00
swiper_index:
---
## 引言

> 嘿！C++编程的探险者们，准备好进入编程的奇幻世界了吗？🚀
>
> C++STL（Standard Template Library，标准模板库）就像是你的魔法工具箱，为你开启了一扇通向编程奇境的大门。🔮 这里有一系列通用的模板类和函数，可以让你轻松实现各种常见的数据结构和算法。📚
>
> 想象一下，STL就是你的超能力，它设计的初衷是为了让你的C++程序更高效、更可重用。无论你是新手还是高手，STL都能为你提供魔法般的帮助，让你以更轻松的方式编写出高质量的代码。✨

> STL有三个**超级组件**：容器（Containers）、算法（Algorithms）和迭代器（Iterators）。容器就像是编程的宝藏，让你轻松地保存和组织数据。算法则是你的智慧秘籍，可以帮你完成排序、搜索等任务。而迭代器，就像是你的探险指南，带领你逐个探索容器中的元素。🌟
>
> 无论你是要创建数据的奇幻旅程，还是寻找编程的宝藏，STL都会是你的忠实伙伴。让我们一起踏上STL之旅，探索编程世界的魔法！🌌🔍

## 一、STL基本概念

> C++STL（Standard Template Library，**标准模板库**）是C++编程语言中的一个重要组成部分，它提供了一组通用的模板类和函数，用于实现常用的数据结构和算法。STL的设计目标是为了提高C++程序的效率和可重用性，使程序员能够更轻松地编写高质量的代码。**STL主要由三个组件组成**：**容器**（Containers）、**算法**（Algorithms）和**迭代器**（Iterators）。

## 二、具体介绍

### 2.1 容器

> 容器是STL中用于存储和管理数据的类模板。它们提供了不同的数据结构，如向量（vector）、链表（list）、队列（queue）、栈（stack）、映射（map）、集合（set）等。**容器提供了统一的接口，使得可以在不同的数据结构之间进行切换，而无需修改大部分代码**。

当涉及到C++的STL容器时，**最简单和常见的容器是`vector`**，它是一个**动态数组**，可以根据需要自动扩展或缩小。

下面是一个简单的示例，演示如何创建一个`vector`、添加元素、访问元素和获取容器的大小：

```cpp
#include <iostream>
#include <vector>

int main() {
    // 创建一个整数型的vector
    std::vector<int> myVector;

    // 向vector添加元素
    myVector.push_back(5);
    myVector.push_back(10);
    myVector.push_back(20);

    // 访问元素
    std::cout << "First element: " << myVector[0] << std::endl;
    std::cout << "Second element: " << myVector[1] << std::endl;

    // 获取vector的大小
    std::cout << "Vector size: " << myVector.size() << std::endl;

    return 0;
}
```

**代码解释** 

1. **包含头文件： `#include <vector>`** 导入了用于vector的STL头文件，使我们能够使用vector容器。
2. **创建vector：** **`std::vector<int> myVector;`** 创建了一个名为`myVector`的整数型vector。
3. **添加元素：** 使用**`push_back`函数**向vector添加元素。在示例中，我们添加了三个整数元素。
4. **访问元素：** 通过使用下标操作符（`[]`）访问vector中的元素。示例中演示了如何访问第一个和第二个元素。
5. **获取大小：** 使用**`size()`**函数获取vector中元素的数量。

这个示例展示了如何使用`vector`容器来存储和管理整数元素。容器会根据需要自动分配内存，因此你无需担心容器大小的管理。`vector`是STL中最常用的容器之一，尤其适用于需要动态增长或缩小大小的情况。

### 2.2 算法

> STL（标准模板库）中的算法是**预定义的函数**，用于执行各种常见的操作，如排序、搜索、复制、变换等。这些算法被设计成通用的，可以适用于不同的容器类型，例如向量、链表、数组等。下面我将为您提供一些常见的STL算法示例以及解释。

假设我们有一个整数向量 `std::vector<int>`，我们将使用这个向量来展示不同的STL算法。

#### **2.2.1 排序（Sort）**

STL中的 `std::sort` 算法用于对容器中的元素进行排序。它可以接受两个迭代器作为参数，表示排序的范围（**关于迭代器下面会进行介绍**）。以下是一个示例：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> numbers = {5, 2, 8, 1, 9};
    
    // 使用 std::sort 对 numbers 进行升序排序
    std::sort(numbers.begin(), numbers.end());
    
    // 打印排序后的结果
    for (int num : numbers) {
        std::cout << num << " ";
    }
    
    return 0;
}
```

#### 2.2.2 **查找（Find）**

`std::find` 算法用于在容器中查找特定的值，它返回一个迭代器，指向第一个匹配的元素。如果没有找到，返回的迭代器等于容器的 `end()` 迭代器。以下是一个示例：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> numbers = {5, 2, 8, 1, 9};
    
    // 使用 std::find 查找值为 8 的元素
    auto it = std::find(numbers.begin(), numbers.end(), 8);
    
    if (it != numbers.end()) {
        std::cout << "Found at index: " << std::distance(numbers.begin(), it) << std::endl;
    } else {
        std::cout << "Not found." << std::endl;
    }
    
    return 0;
}
```

#### 2.3.2 **复制（Copy）**

`std::copy` 算法用于将一个容器的元素复制到另一个容器。以下是一个示例：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> source = {5, 2, 8, 1, 9};
    std::vector<int> destination(source.size());
    
    // 使用 std::copy 将 source 中的元素复制到 destination
    std::copy(source.begin(), source.end(), destination.begin());
    
    // 打印复制后的结果
    for (int num : destination) {
        std::cout << num << " ";
    }
    
    return 0;
}
```

### 2.3 迭代器

> 迭代器（iterator）是STL中的一个关键概念，它提供了一种统一的方式来遍历和访问容器中的元素，不论容器的类型是何种。
>
> 迭代器的作用类似于指针，它允许你在容器中移动，并且能够访问容器内的元素。迭代器可以用于各种STL容器，如vector、list、set、map等。

**STL中的迭代器分为几个不同种类，每种种类对应于不同的容器类型和访问方式。**

**常见的迭代器种类有：**

> 1. **输入迭代器（Input Iterator）：**只能读取容器中的元素，而不能修改。一般用于单向遍历，比如算法中的 `std::find`。
> 2. **输出迭代器（Output Iterator）：**只能向容器中写入元素，而不能读取。一般用于单向遍历，比如算法中的某些输出操作。
> 3. **前向迭代器（Forward Iterator）：**能够在容器中向前遍历，并且可以多次读取和写入。比输入迭代器更强大。
> 4. **双向迭代器（Bidirectional Iterator）：**可以在容器中双向移动（前进和后退），支持读取和写入。
> 5. **随机访问迭代器（Random Access Iterator）：**最强大的迭代器类型，可以在容器中任意位置跳跃式地移动，支持读取和写入，类似于指针的功能。适用于像数组这样支持随机访问的容器。

可以使用迭代器来**遍历容器中的元素**，如下所示的示例代码：

```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> myVector = {1, 2, 3, 4, 5};

    // 使用迭代器遍历 vector
    for (std::vector<int>::iterator it = myVector.begin(); it != myVector.end(); ++it) {
        std::cout << *it << " ";
    }

    return 0;
}
```

上述代码中，使用的是C++的STL容器`std::vector`，并且使用的是`std::vector<int>::iterator`作为迭代器类型。这个迭代器类型是**双向迭代器**（Bidirectional Iterator）。

**双向迭代器可以在容器中双向移动，即可以前进也可以后退，而且支持读取和写入操作。**在你的代码中，`std::vector<int>::iterator`是用来遍历`myVector`中的整数元素的。

在这段代码中，`it` 是一个迭代器，它从`myVector.begin()`（**容器起始位置**）开始，逐步遍历到 `myVector.end()`（**容器末尾位置之后的位置，表示遍历结束**）。通过 `*it`，你可以访问当前迭代器指向的元素。在每次迭代中，将当前元素打印出来。

在C++11及更高版本中，你还可以使用自动类型推断的 `auto` 关键字来简化迭代器的声明，例如：

> **图片未上传**

##  三、分析STL

### **优势：**

> 1. **高度抽象的数据结构和算法：** STL提供了一系列高度抽象的数据结构（如vector、list、map等）和算法（如排序、查找、复制等），使得开发者可以更专注于解决问题，而不需要重复实现这些基本功能。
> 2. **可重用性和可维护性：** 使用STL可以减少代码的重复性，增加代码的可重用性。开发者可以通过使用STL容器和算法来构建复杂的程序，从而降低错误的发生概率，提高代码的可维护性。
> 3. **高性能：** STL中的算法和数据结构经过优化，通常具有高性能。它们在底层进行了许多优化，使得在大多数情况下，STL提供的算法效率相当高。
> 4. **标准化：** STL是C++标准库的一部分，因此可以在任何支持C++标准库的编译器和平台上使用，无需额外的依赖。这种标准化使得代码更具可移植性。
> 5. **简化开发过程：** STL提供了一组丰富的容器和算法，可以极大地简化常见的编程任务。开发人员不需要从头开始实现这些功能，而是可以直接使用STL提供的工具。

### **缺点：**

> 1. **学习曲线：** 对于初学者来说，STL可能会有一定的学习曲线。理解迭代器、容器和算法之间的关系以及如何正确使用它们可能需要一些时间。
> 2. **性能问题：** 尽管STL通常具有高性能，但在某些特定情况下，直接使用STL可能不是最优选择。一些特定的问题可能需要更精细的优化，甚至需要使用手动编写的数据结构和算法来获得更好的性能。
> 3. **内存占用：** STL容器可能在某些情况下占用较多的内存。例如，vector在动态增长时可能会引发重新分配和复制，导致额外的内存开销。
> 4. **不适用于特定需求：** 尽管STL提供了广泛的数据结构和算法，但并不一定能满足所有需求。在一些特定的问题领域，可能需要更专门的数据结构和算法，而不是通用的STL组件。