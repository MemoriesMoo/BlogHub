---
title: C指针进阶
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-27.webp
description: "\U0001F58B️指针进阶：编程魔法杖，解锁内存奥秘，舞动数据的奇迹。"
mathjax: true
tags:
  - C语言基础
categories:
  - C
abbrlink: ae18156d
date: 2023-11-25 00:00:00
updated: 2023-11-25 00:00:00
swiper_index:
---
## 引言

> 🌟📚 "指针是编程的魔法棒，它们能在内存中画出绚丽的图景。而在今天的探索中，我们将穿越指针的彩虹，解锁高级的指针魔法！就像在魔法学校里学习新的咒语，我们将探索字符指针、数组指针、函数指针等进阶内容，为你的编程技能注入更多的魔力。快来加入我们的奇妙旅程吧，一起解开指针的高级谜团！" 🌈✨

## 第一阶段： 独特的字符指针

> 欢迎来到指针大冒险的第一站！在本阶段，我们将揭开字符指针的神秘面纱，探索它在C语言中的精彩应用。

### 什么是字符指针？

> 字符指针是指向字符数据的指针，它的灵活性和用途让人惊叹不已。**在C语言中，字符串实际上是以字符数组的形式存在的，而字符指针则是指向字符串首字符的指针。**

### 字符指针的用途

> 字符指针的应用极其广泛，尤其在字符串处理方面。它能够让我们以一种高效的方式操作字符串，实现诸如**字符串拷贝**、**连接**、**比较**等操作。此外，字符指针还为我们提供了一种遍历字符串的简洁方法。

### 演示：使用字符指针拷贝字符串

让我们通过一个示例来演示如何使用字符指针拷贝一个字符串。以下是一个**自定义的字符串拷贝函数：**

```objectivec
#include <stdio.h>

void stringCopy(char *dest, const char *src) {
    while (*src != '\0') {
        *dest = *src;
        dest++;
        src++;
    }
    *dest = '\0';  // 添加字符串结尾的空字符
}

int main() {
    char source[] = "Hello, World!";
    char destination[20];

    stringCopy(destination, source);
    printf("Copied string: %s\n", destination);

    return 0;
}
```

在这个例子中，我们使用字符指针 `src` 和 `dest` 分别指向源字符串和目标字符串。通过**逐个字符地拷贝**，我们可以将源字符串的内容复制到目标字符串中，然后**在末尾添加一个空字符**。

### 字符指针与字符串常量

> 字符指针也常用于处理字符串常量，这些常量是以字符数组的形式存储在内存中。通过字符指针，我们可以轻松地访问和操作这些常量：

```objectivec
#include <stdio.h>

int main() {
    const char *message = "Hello, C Pointers!";
    printf("Message: %s\n", message);

    return 0;
}
```

### 小试牛刀

```objectivec
#include <stdio.h>

int main() {
    char str1[] = "Hello, World!";
    char str2[] = "Hello, World!";
    const char *str3 = "Hello, World!";
    const char *str4 = "Hello, World!";

    if (str1 == str2)
        printf("str1 and str2 are same\n");
    else
        printf("str1 and str2 are not same\n");

    if (str3 == str4)
        printf("str3 and str4 are same\n");
    else
        printf("str3 and str4 are not same\n");

    return 0;
}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**在这段代码中，我们比较了字符数组和指向字符串常量的字符指针之间的区别。以下是代码的详细解释：**

> 1. 在 `main` 函数中，我们定义了两个**字符数组** `str1` 和 `str2`，它们都包含相同的字符串 `"Hello, World!"`。
> 2. 我们还定义了两个**指向字符串常量的字符指针** `str3` 和 `str4`，同样指向字符串 `"Hello, World!"`。
> 3. 接下来，我们使用 `==` 运算符分别比较 `str1` 和 `str2`，以及 `str3` 和 `str4`。注意，`str1` 和 `str2` 是字符数组，而 `str3` 和 `str4` 是指向字符串常量的指针。
> 4. **当比较 `str1` 和 `str2` 时，由于它们分别是不同的字符数组，它们的地址也是不同的，因此条件为假，将输出 "str1 and str2 are not same"。**
> 5. **当比较 `str3` 和 `str4` 时，由于它们指向相同的字符串常量，它们的地址也是相同的，因此条件为真，将输出 "str3 and str4 are same"。**

## 第二阶段：玩转指针数组

> 欢迎来到指针大冒险的第二站！在本阶段，我们将深入探讨指针数组的奥秘，适合各位小白朋友学习，让我们一起探索它在C语言中的精彩应用。

### 指针数组是什么？

> 首先，让我们来理解一下什么是指针数组。在C语言中，**指针数组是一种特殊的数组类型**，**它的每个元素都是指针**。**每个指针可以指向不同的数据**，如整数、字符、字符串等。**这意味着，指针数组实际上是存储了多个指针的数组。**

### 指针数组的用途

指针数组在许多编程场景中都扮演着关键角色。它的灵活性使得它在多种情况下都非常有用：

> - **字符串数组**：一个常见的应用是创建字符串数组，其中每个元素指向不同的字符串。这使得我们能够轻松地管理和操作多个字符串。
> - **函数参数**：当我们需要将多个数据项作为参数传递给函数时，指针数组是一个不错的选择。**它可以帮助我们将不同类型的数据集合在一起传递给函数。**
> - **多项选择题**：在编程中，有时我们需要处理多个选项，指针数组可以用来存储这些选项的指针，便于处理和操作。

### 演示：创建和使用指针数组

让我们通过一个示例来演示如何创建和使用指针数组，并展示它在字符串数组中的应用：

```objectivec
#include <stdio.h>

int main() {
    char *fruits[] = {
        "Apple",
        "Banana",
        "Orange",
        "Grapes"
    };

    int numFruits = sizeof(fruits) / sizeof(fruits[0]);

    for (int i = 0; i < numFruits; i++) {
        printf("Fruit %d: %s\n", i + 1, fruits[i]);
    }

    return 0;
}
```

在这个例子中，我们创建了一个指针数组 `fruits`，**其中的每个元素都是指向字符串的指针。**我们使用花括号初始化语法，**将几个水果名字的字符串赋值给指针数组。**然后，我们通过循环遍历指针数组，并打印出每个水果的名称。

## 第三阶段：探索数组指针的神奇之旅

> 欢迎来到指针大冒险的第三站！在本阶段，我们将深入探讨数组指针的奥秘，为C语言新手们揭示其精彩应用。

### 数组指针：是指针还是数组？

> 在C语言中，数组指针是一种非常有趣的概念。**虽然它名字中包含了"数组"，但它实际上是一种指针。**数组指针允许我们以一种更加灵活的方式来处理数组，从而克服了使用普通数组下标的一些限制。

### 数组指针的定义

> 以往，我们熟悉了整型指针和浮点型指针，它们分别指向整数和浮点数类型的数据。类似地，**数组指针**是一种能够指向数组的指针。通过使用数组指针，我们能够以更加自由和灵活的方式操作数组中的元素。

### 解释数组指针的应用

> 数组指针在处理数组时发挥着重要作用。它不仅仅是一个单纯的指针，**它更像是一个为数组开辟的一扇智能门。**它允许我们以指针的方式移动、访问和操作数组中的元素，无需受限于普通数组下标的顺序

### 小试牛刀

在该小节，我们将深入探索两种看似相似但实际截然不同的指针类型：**`int \*p1[10];`** 和 **`int (\*p2)[10];`**。虽然它们都涉及指针和数组，但它们的应用和含义却截然不同。

#### `int *p1[10];`：指针数组

> 首先，让我们揭开 `int *p1[10];` 的面纱。这段代码定义了一个数组，这个数组中有10个元素，每个元素都是指向整数的指针。具体来说，`p1` 是一个指针数组，它可以存储多个整数指针。这使得我们能够方便地管理多个整数指针，每个指针可以指向不同的整数。

#### `int (*p2)[10];`：数组指针

> 接下来，我们来解释 `int (*p2)[10];`。这行代码定义了一个指针，这个指针指向一个包含10个整数的数组。简而言之，`p2` 是一个数组指针，它可以帮助我们处理整数数组。通过操作这个指针，我们可以以更加灵活的方式访问整数数组中的元素。

**注意：[]的优先级要高于\*号的，所以必须加上()来保证p先和\*结合。**

### `&数组名` 与 数组名：剖析引用与地址

#### `&数组名`：取地址操作

> 首先，我们来解释 `&数组名` 的作用。`&数组名` 表示取得整个数组的起始地址，它**不是数组本身，而是指向数组的指针。**这种操作可以让我们**获取整个数组在内存中的位置。**

#### 数组名：首元素的指针

> **数组名不仅仅是一个名称，它还是一个指向数组首元素的指针。**因此，数组名可以用来表示整个数组，也可以用来访问数组中的元素。

#### 比较一下

- **`&数组名`**：表示整个数组的起始地址，是指向数组的指针。
- **数组名**：指向数组首元素的指针，可以用来表示整个数组或访问数组元素。

> **举个例子，假设有一个整型数组 `int numbers[5];`，我们可以使用 `&numbers` 来获得整个数组在内存中的位置，而使用 `numbers` 则是数组首元素的指针，可以用于遍历和访问数组中的元素。**

#### 代码解释

```c
#include <stdio.h>

int main() {
    int arr[10] = {0};
    printf("%p\n", arr);   // 打印数组首元素的地址
    printf("%p\n", &arr);  // 打印整个数组的起始地址
    return 0;
}
```

```bash
0x7ffeb174f5c
00x7ffeb174f5c0
```

> - `printf("%p\n", arr);` 打印的是数组首元素 `arr[0]` 的地址。**由于数组名本身就是指向数组首元素的指针，所以 `arr` 和 `&arr[0]` 是等价的。**因此，它们打印的地址是相同的，都是数组的首地址。
> - `printf("%p\n", &arr);` 打印的是**整个数组 `arr` 的起始地址。**在内存中，数组占据了一段连续的内存空间，**`&arr` 就是整个数组的起始地址。**

**再来一个**

```c
#include <stdio.h>

int main() {
    int arr[10] = {0};
    printf("arr = %p\n", arr);       // 打印数组首元素的地址
    printf("&arr = %p\n", &arr);     // 打印整个数组的起始地址
    printf("arr+1 = %p\n", arr+1);   // 打印数组第二个元素的地址
    printf("&arr+1 = %p\n", &arr+1); // 打印下一个数组的起始地址
    return 0;
}
```

```bash
arr = 0x7ffea8d5f3c0
&arr = 0x7ffea8d5f3c0
arr+1 = 0x7ffea8d5f3c4
&arr+1 = 0x7ffea8d5f3e8
```

> - **`printf("arr = %p\n", arr);`** 和 **`printf("&arr = %p\n", &arr);`** 打印的都是**数组 `arr` 的起始地址**。因为数组名 `arr` 本身就是指向数组首元素的指针，所以这两者的值是相同的。
> - **`printf("arr+1 = %p\n", arr+1);`** 打印的是**数组的第二个元素的地址。**由于整型数组中每个元素占用4个字节（假设为32位系统），所以 `arr+1` 就是 `arr` 的起始地址加上4个字节，即指向数组的第二个元素。
> - **`printf("&arr+1 = %p\n", &arr+1);`** 打印的是**下一个数组的起始地址。**在内存中，相邻的数组之间有一个间隔，这个间隔的大小是整个数组的大小。因此，`&arr+1` 实际上是指向下一个数组的起始地址。

### 数组指针的使用

**看一段代码.**

```c
#include <stdio.h>

// 打印二维数组，使用数组作为参数
void print_arr1(int arr[3][5], int row, int col) {
    int i, j;
    for(i = 0; i < row; i++) {
        for(j = 0; j < col; j++) {
            printf("%d ", arr[i][j]);
        }
        printf("\n");
    }
}

// 打印二维数组，使用数组指针作为参数
void print_arr2(int (*arr)[5], int row, int col) {
    int i, j;
    for(i = 0; i < row; i++) {
        for(j = 0; j < col; j++) {
            printf("%d ", arr[i][j]);
        }
        printf("\n");
    }
}

int main() {
    int arr[3][5] = {
        {1, 2, 3, 4, 5},
        {6, 7, 8, 9, 10},
        {11, 12, 13, 14, 15}
    };

    printf("Printing using print_arr1:\n");
    print_arr1(arr, 3, 5);
    
    printf("\nPrinting using print_arr2:\n");
    print_arr2(arr, 3, 5);
    
    return 0;
}
```

#### `print_arr1` 函数解释

```c
void print_arr1(int arr[3][5], int row, int col)
```

> 这个函数的参数 `arr` 是一个二维数组，它是一个 `3x5` 的整数数组。参数 `row` 和 `col` 分别表示行数和列数。在函数内部，我们使用嵌套循环遍历二维数组的每个元素，并使用 `arr[i][j]` 访问它们。

#### `print_arr2` 函数解释

```c
void print_arr2(int (*arr)[5], int row, int col)
```

> 这个函数的参数 `arr` 是一个指向包含5个整数的数组的指针。参数 `row` 和 `col` 仍然表示行数和列数。在函数内部，我们同样使用嵌套循环遍历二维数组的每个元素，但是这里使用 `arr[i][j]` 访问它们。

值得注意的是，尽管在 `print_arr2` 中，我们传递的是 `arr`，但**实际上传递的是指向第一行的指针。**这是因为在 C 语言中，数组名作为函数参数传递时，会退化为指向数组首元素的指针。因此，`print_arr2` 函数可以通过指针来遍历整个二维数组。

#### 练习

```c
int arr[5];
int *parr1[10];
int (*parr2)[10];
int (*parr3[10])[5];
```

> 1. **`int arr[5];`：**这是一个包含5个整数的一维数组。
> 2. **`int \*parr1[10];`：**这是一个数组，数组中包含了10个整型指针。每个元素 `parr1[i]` 可以指向一个整数。
> 3. **`int (\*parr2)[10];`：**这是一个指向包含10个整数的数组的指针。通过 `parr2` 可以访问整个数组。
> 4. **`int (\*parr3[10])[5];`：**这是一个数组，数组中包含了10个指向包含5个整数的数组的指针。每个元素 `parr3[i]` 可以指向一个长度为5的整数数组。

## 第四阶段：数组参数、指针参数的传递方式

> 欢迎来到第四阶段！在这里，我们将继续深入探讨C语言中有关函数参数传递的话题，重点介绍一维数组、二维数组、一级指针和二级指针作为函数参数的传递方式。通过了解这些传参方式的独特特点和灵活应用，我们将在C语言编程的旅程中迈出坚实一步。

### 一维数组传参

> 当你将一维数组作为函数参数传递时，实际上**传递的是数组的首地址**。这种方式使得函数内部可以操作数组元素，但无法获取数组的长度。下面，我们一起来看一个生动的例子吧！

```objectivec
#include <stdio.h>

void printArray(int arr[], int size) {
    for (int i = 0; i < size; i++) {
        printf("%d ", arr[i]);
    }
}

int main() {
    int arr[5] = {1, 2, 3, 4, 5};
    printArray(arr, 5); // 传递数组和数组长度作为参数

    return 0;
}
```

### 二维数组传参

> 在传递二维数组作为函数参数时，需要**明确指定第二维的大小**。这种传递方式在处理矩阵和图等二维数据结构时非常实用。让我们通过一个更详细的示例来深入理解！

```objectivec
#include <stdio.h>

// 函数接受二维数组和行列数作为参数
void print2DArray(int arr[][3], int rows, int cols) {
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            printf("%d ", arr[i][j]);
        }
        printf("\n");
    }
}

int main() {
    int arr[3][3] = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
    
    printf("Original 2D Array:\n");
    print2DArray(arr, 3, 3); // 传递二维数组和行列数作为参数
    
    return 0;
}
```

> 在这个示例中，我们定义了一个函数 `print2DArray`，它接受一个二维数组和行列数作为参数，然后按行列的方式输出二维数组的内容。在 `main` 函数中，我们创建了一个3x3的二维数组，并通过 `print2DArray` 函数将其内容打印出来。

### 一级指针传参

> 一级指针作为函数参数传递时，实际上传递的是**指针的地址**。这种方式允许在函数内部修改指针指向的内容，但无法修改指针本身。让我们看看一个有趣的例子！

```objectivec
#include <stdio.h>

void modifyPointer(int *ptr) {
    *ptr = 42; // 修改指针指向的内容
}

int main() {
    int num = 10;
    int *ptr = &num;
    
    modifyPointer(ptr); // 传递指针作为参数

    printf("Value: %d\n", *ptr); // 输出修改后的值

    return 0;
}
```

### 二级指针传参

> 二级指针是指向指针的指针。传递二级指针作为函数参数时**，传递的是指针的地址**，允许在函数内部修改指针本身的值。来看一个有趣的示例吧：

```objectivec
#include <stdio.h>

void modifyDoublePointer(int **pptr) {
    int newVal = 42;
    *pptr = &newVal; // 修改二级指针指向的值
}

int main() {
    int num = 10;
    int *ptr = &num;
    int **pptr = &ptr;
    
    modifyDoublePointer(pptr); // 传递二级指针作为参数

    printf("Value: %d\n", **pptr); // 输出修改后的值

    return 0;
}
```

## 第五阶段： 探索函数指针的神奇世界

> 欢迎来到第五阶段！在这里，我们将深入研究C语言中的一个强大而神秘的概念——**函数指针**。函数指针为我们提供了更高级的编程能力，**允许我们像操作数据一样操作函数**。让我们一起探索函数指针的神奇世界吧！

### 函数指针简介

> 函数指针是指向函数的指针变量。与指向数据的指针类似，函数指针存储函数的地址，使我们能够通过指针调用该函数。这种能力在回调函数、动态函数调用和多态性等场景中非常有用。

### 使用函数指针

以下是一个简单的例子，演示如何声明、赋值和调用函数指针：

```objectivec
#include <stdio.h>

// 声明函数指针类型
typedef int (*Operation)(int, int);

// 加法函数
int add(int a, int b) {
    return a + b;
}

// 减法函数
int subtract(int a, int b) {
    return a - b;
}

int main() {
    Operation operationPtr; // 声明函数指针变量
    operationPtr = add; // 赋值为add函数

    int result = operationPtr(5, 3); // 通过指针调用函数
    printf("Result: %d\n", result); // 输出结果

    return 0;
}
```

### 函数指针数组

函数指针可以组成数组，称为函数指针数组。这使我们可以通过索引来选择不同的函数。以下是一个使用函数指针数组的示例：

```objectivec
#include <stdio.h>

int add(int a, int b) {
    return a + b;
}

int subtract(int a, int b) {
    return a - b;
}

int multiply(int a, int b) {
    return a * b;
}

int main() {
    int (*operationPtr[3])(int, int); // 声明函数指针数组
    operationPtr[0] = add;
    operationPtr[1] = subtract;
    operationPtr[2] = multiply;

    int result = operationPtr[1](10, 5); // 调用subtract函数
    printf("Result: %d\n", result);

    return 0;
}
```

### 指向函数指针数组的指针

我们可以声明指向函数指针数组的指针，让我们一起看看它的用法：

```objectivec
#include <stdio.h>

int add(int a, int b) {
    return a + b;
}

int subtract(int a, int b) {
    return a - b;
}

int multiply(int a, int b) {
    return a * b;
}

int main() {
    int (*(*operationPtrPtr)[3])(int, int); // 声明指向函数指针数组的指针
    operationPtrPtr = &operationPtr; // 赋值为函数指针数组的地址

    int result = (*operationPtrPtr)[2](7, 3); // 通过指针调用multiply函数
    printf("Result: %d\n", result);

    return 0;
}
```



## 第六阶段： 回调函数：让程序更灵活

> 欢迎来到第六阶段！**在这里，我们将深入探讨C语言中的另一个重要概念——回调函数。**回调函数是一种让程序更灵活的技术，通过它，我们可以将函数作为参数传递给其他函数，从而实现更动态的操作。让我们一起探索回调函数的奇妙之处！

### 回调函数简介

> 回调函数是指传递一个函数的指针作为参数给另一个函数，然后在后者中调用这个传递进来的函数。这种方式使得我们可以在运行时决定要执行哪些函数，实现更灵活的程序逻辑。

### 回调函数的应用

以下是一个简单的示例，展示如何使用回调函数来排序一个整数数组：

```objectivec
#include <stdio.h>
#include <stdlib.h>

// 比较函数：升序排列
int compareAsc(const void *a, const void *b) {
    return (*(int *)a - *(int *)b);
}

// 比较函数：降序排列
int compareDesc(const void *a, const void *b) {
    return (*(int *)b - *(int *)a);
}

// 使用回调函数对数组进行排序
void sortArray(int arr[], int size, int (*compare)(const void *, const void *)) {
    qsort(arr, size, sizeof(int), compare);
}

int main() {
    int arr[5] = {5, 2, 8, 1, 3};
    
    sortArray(arr, 5, compareAsc); // 使用升序比较函数排序
    printf("Ascending Order: ");
    for (int i = 0; i < 5; i++) {
        printf("%d ", arr[i]);
    }
    
    sortArray(arr, 5, compareDesc); // 使用降序比较函数排序
    printf("\nDescending Order: ");
    for (int i = 0; i < 5; i++) {
        printf("%d ", arr[i]);
    }

    return 0;
}
```

**在这个示例中，我们使用了两个不同的比较函数，分别用于升序和降序排序。通过将比较函数作为参数传递给 `sortArray` 函数，我们实现了动态选择排序方式的功能。**

## 结语：尖叫指针，飞跃编程界！

来到本篇博客的终点，你是否感受到了指针的神奇魅力呢？从字符指针到数组指针，从函数指针到回调函数，我们一路探索，揭开了指针的奥秘面纱。

指针，如同瞬间连接你与计算机内部的魔法纽带，让操作数据、调用函数变得轻松自如。无论是在数组的海洋中航行，还是在函数的宇宙中穿梭，指针都是你最忠实的伙伴。

相信你已经在这趟指针之旅中汲取了丰富的知识。保持好奇心，继续探索，愿你的编程旅程一路飞升，创造出更多令人惊叹的代码艺术！不忘初心，一起向编程的星辰大海进发吧！🌟