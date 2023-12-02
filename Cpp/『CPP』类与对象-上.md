---
title: 类与对象（上）
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-18.webp
description: "\U0001F389面向过程与面向对象，类与对象初识。"
mathjax: true
tags:
  - 类与对象
categories:
  - CPP
abbrlink: ccd28287
date: 2023-11-13 00:00:00
updated: 2023-11-14 00:00:00
swiper_index:
sticky: 6
---
## 一、认识面向过程和面向对象

面向过程（Procedural Programming）和面向对象（Object-Oriented Programming）是两种不同的编程范式。它们在程序设计的思想、结构和实现方式上有显著的区别。

### 1.1 面向过程

> 面向过程是一种**基于过程（或函数）的编程方式**。在面向过程编程中，程序的执行流程通过依次执行一系列函数或过程来实现。数据和函数是分离的，即数据和操作数据的函数是分开的。程序被分解为多个子任务，每个子任务由一个或多个函数来完成。面向过程的编程风格适合简单、线性的问题，但随着问题的复杂性增加，程序可能变得难以维护和扩展。

**特点：**

- 程序由函数组成，**函数是核心概念**。
- 数据和函数是**分离**的。
- 适合简单、线性的问题。
- 编程的思想更加**自顶向下**。

**示例：**

```c
#include <stdio.h>

// 函数：计算矩形的面积
float calculateArea(float length, float width) {
    return length * width;
}

// 函数：计算矩形的周长
float calculatePerimeter(float length, float width) {
    return 2 * (length + width);
}

int main() {
    float length = 5;
    float width = 3;
    float area = calculateArea(length, width);
    float perimeter = calculatePerimeter(length, width);

    printf("矩形的面积：%.2f\n", area);
    printf("矩形的周长：%.2f\n", perimeter);

    return 0;
}
```

### 1.2 面向对象

> 面向对象是一种**以对象为中心的编程方式**。在面向对象编程中，将数据和操作数据的函数（称为方法）**封装**在一起，形成对象。对象是类的实例，类是对象的抽象。通过类定义了对象的属性（数据）和方法（行为），并且可以通过继承机制派生出子类，实现代码的复用和扩展性。面向对象的编程风格更加灵活和模块化，适合处理复杂问题。

**特点：**

- 程序由对象组成，**对象是核心概念**。
- 数据和操作数据的方法**封装**在一起。
- 适合处理复杂、多层次的问题。
- 编程的思想更加**自底向上**。

**示例：**

```cpp
#include <iostream>

class Rectangle {
public:
    // 构造函数
    Rectangle(float length, float width) {
        this->length = length;
        this->width = width;
    }

    // 方法：计算矩形的面积
    float calculateArea() {
        return length * width;
    }

    // 方法：计算矩形的周长
    float calculatePerimeter() {
        return 2 * (length + width);
    }

private:
    float length;
    float width;
};

int main() {
    float length = 5;
    float width = 3;

    // 创建Rectangle对象
    Rectangle rectangle(length, width);

    // 计算面积和周长
    float area = rectangle.calculateArea();
    float perimeter = rectangle.calculatePerimeter();

    std::cout << "矩形的面积：" << area << std::endl;
    std::cout << "矩形的周长：" << perimeter << std::endl;

    return 0;
}
```

> 在面向过程示例中，我们使用了函数来执行计算，而在面向对象示例中，我们创建了一个名为**`Rectangle`**的类，封装了数据（长度和宽度）和相关的方法（计算面积和周长）。C++的面向对象编程使代码更加模块化和可扩展，适合处理复杂问题。

## 二、初步了解类与结构体

### 2.1 结构体（`struct`）

**特点：**

- 结构体是C语言中的一种**数据类型**，也被许多其他编程语言所支持。
- 结构体可以包含多个不同类型的成员变量（字段），这些字段在内存中是**依次存储**的。
- **结构体没有成员函数（方法），只包含数据成员。**
- 默认情况下，结构体的成员是公共的（public），所有的代码都可以**直接**访问结构体的成员。
- 结构体**不能继承**其他结构体或类型。
- 结构体的定义类似于类似于变量定义，使用关键字`struct`。

**示例：**

```c
#include <stdio.h>

// 定义矩形结构体
struct Rectangle {
    float length; // 矩形的长度
    float width;  // 矩形的宽度
};

int main() {
    // 创建Rectangle结构体变量
    struct Rectangle rect;

    // 初始化矩形的长度和宽度
    rect.length = 5.0;
    rect.width = 3.0;

    // 计算矩形的面积和周长
    float area = rect.length * rect.width;
    float perimeter = 2 * (rect.length + rect.width);

    return 0;
}
```

### 2.2 类（`class`）

**特点：**

1. 类是面向对象编程（如C++）中的概念，用于创建**自定义的数据类型**。
2. 类可以包含数据成员（属性）和成员函数（方法），将数据和操作数据的方法**封装**在一起。
3. 类可以实现访问控制，通过关键字`public`、`private`和`protected`来定义成员的**可访问性**。
4. 类支持继承机制，可以派生出子类，实现代码的复用和扩展性。
5. 类是对象的抽象，对象是类的**实例化**。在使用类创建对象时，对象会占用内存并包含类定义中的成员变量和成员函数。

**示例：**

```cpp
#include <iostream>

// 定义矩形类
class Rectangle {
public:
    float length; // 矩形的长度
    float width;  // 矩形的宽度

    // 计算矩形的面积
    float calculateArea() {
        return length * width;
    }

    // 计算矩形的周长
    float calculatePerimeter() {
        return 2 * (length + width);
    }
};

int main() {
    // 创建Rectangle对象
    Rectangle rect;

    // 初始化矩形的长度和宽度
    rect.length = 5.0;
    rect.width = 3.0;

    // 计算矩形的面积和周长
    float area = rect.calculateArea();
    float perimeter = rect.calculatePerimeter();

    return 0;
}
```

**提醒：**

> 在C++当中，**struct**和**class**具有相同功能，**struct**可以当作类的关键字使用，在结构体内定义数据与函数进行封装，同时也可以使用**访问限定符**，**但是在C++中更加倾向于使用class定义类**。同时，C语言中的**struct**关键字定义的结构体内部的数据是**默认公有**的，通过结构体定义的变量可以直接访问，相当与C++类中的**public**权限，C++类中的数据和函数是**默认私有**的，类外无法进行访问，根据实际情况使用三种访问限定符。
>
> **注意：在继承和模板参数列表位置，struct和class也有区别，后序介绍。**

## 三、类

### 3.1 语法

```cpp
class className
{
    // 类体：由成员函数和成员变量组成
};  // 一定要注意后面的分号
```

> **class**为定义类的**关键字**，**className**为**类的名字**，**{}中为类的主体**，注意类定义结束时后面**分号不能省略**。类体中内容称为类的成员：类中的变量称为成员**属性或成员变量**; 类中的函数称为**成员方法或者成员函数**。

### 3.2 定义方式

#### 3.2.1 不分文件编写

```cpp
#ifndef RECTANGLE_H
#define RECTANGLE_H

// 定义矩形类
class Rectangle {
public:
    // 构造函数：初始化矩形的长度和宽度
    Rectangle(float length, float width);

    // 成员函数：计算矩形的面积
    float calculateArea();

    // 成员函数：计算矩形的周长
    float calculatePerimeter();

private:
    float length; // 矩形的长度
    float width;  // 矩形的宽度
};

#endif
```

> **此代码仅作功能演示使用，读者遇到陌生的概念语法等会在后续文章进行讲解。**
>
> **需注意成员函数如果在类中定义，编译器可能会将其当成内 联函数处理。**

#### 3.2.2 分文件编写（常用）

**Rectangle.h（头文件）**

```cpp
#ifndef RECTANGLE_H
#define RECTANGLE_H

// 定义矩形类
class Rectangle {
public:
    // 构造函数：初始化矩形的长度和宽度
    Rectangle(float length, float width);

    // 成员函数：计算矩形的面积
    float calculateArea();

    // 成员函数：计算矩形的周长
    float calculatePerimeter();

private:
    float length; // 矩形的长度
    float width;  // 矩形的宽度
};

#endif
```

**Rectangle.cpp（实现文件）**

```cpp
#include "Rectangle.h"

// 构造函数的实现
Rectangle::Rectangle(float length, float width) {
    this->length = length;
    this->width = width;
}

// 成员函数：计算矩形的面积
float Rectangle::calculateArea() {
    return length * width;
}

// 成员函数：计算矩形的周长
float Rectangle::calculatePerimeter() {
    return 2 * (length + width);
}
```

> **注意：成员函数名前需要加类名::，表明该函数定义属于此类。**

## 四、类的访问限定符及封装

### 4.1 访问限定符

> C++实现封装的方式：用类将对象的属性与方法结合在一块，让对象更加完善，通过访问权限选 择性的将其接口提供给外部的用户使用。
>
> 在C++中，**类的访问限定符用于控制类的成员（成员变量和成员函数）对外部的可见性和访问权限**。类的访问限定符有三种：`public`、`private`和`protected`。这些访问限定符决定了类的成员在哪些地方可以被访问。

**1. public**：

- public是默认的访问限定符，如果不指定访问限定符，默认为public。
- public成员可以在类的外部访问，也可以在类的内部访问。

**2. private：**

- private成员只能在类的内部访问，不能在类的外部直接访问。
- private成员用于隐藏实现细节，将数据隐藏在类的内部，防止外部代码直接访问和修改。

**3. protected**：

- protected成员与private成员类似，也只能在类的内部访问。
- 但protected成员对于**派生类（子类）**来说是可见的，**允许派生类访问基类的protected成员**。

**说明：**

- **访问权限作用域从该访问限定符出现的位置开始直到下一个访问限定符出现时为止，如果后面没有访问限定符，作用域就到 } 即类结束。**
- **访问限定符只在编译时有用，当数据映射到内存后，没有任何访问限定符上的区别。**

### **4.2 封装**

> 封装是面向对象编程的一个重要特性，它允许类将数据和对数据的操作封装在一起，形成一个独立的实体。封装通过将数据成员设置为私有的（private），从而保护了数据的安全性，并通过公共的（public）成员函数来实现对数据的访问和修改。这样外部代码只能通过公共接口来访问和操作数据，无法直接访问私有成员，从而**隐藏了实现细节**，使得类的内部结构和数据对外部代码是**不可见**的。

**封装优点：**

- **数据隐藏和安全性：**类的私有成员对外部代码不可见，避免了直接访问和修改数据，保护了数据的安全性。
- **简化接口：**公共成员函数提供了类与外部交互的接口，隐藏了内部实现细节，使得使用类的代码更简洁、易读。
- **代码重用：**类的接口可以被其他代码重用，提高了代码的可维护性和重用性。

## 五、类的实例化

> 在面向对象编程中，**类的实例化是指通过类创建一个对象的过程**。类定义了一种自定义的数据类型，而类的实例化就是使用该数据类型创建一个具体的实体，也称为**对象**。通过实例化，我们可以使用类的成员函数和成员变量来操作对象的数据和行为。

做个比方。类实例化出对象就像现实中使用建筑设计图建造出房子，类就像设计图，只设计出需要什么东西，但是并没有实体的建筑存在，同样类的定义也只是一个蓝图，**实例化出的对象才能实际存储数据，占用实际物理空间**。

**示例：**

```cpp
class Person{
    //类定义
}

int main(){
    //实例化出对象p
    Person p;
}
```

## 六、 类的对象模型

### 6.1 如何计算类示例的对象大小

```cpp
class Person{
private:
    int _count;

public:
    void print(){
        cout << "Hello World" << endl;
    }
};
```

> **类中既可以有成员变量，又可以有成员函数，那么一个类的对象中包含了什么？如何计算 一个类的大小？值得我们深思！我们应该思考类是被如何存储的！**

### **6.2** 类对象的存储方式猜测

#### 6.3.1 对象中包含类的各个成员（变量＋函数）

> **思考：每个对象内部的成员变量时不相同的，但是函数如同我们平时使用的函数相同，只需要有一份代码即可，如果按照这种方式进行存储，每个对象中都存储一份函数代码，会造成空间浪费，稍加思考也能想到此种方法时行不通的！！**

#### 6.3.2 代码只保存一份，在对象中保存存放代码的地址

>  **此种方法相对第一种方法自然是节省了不少空间，那么让我们在具体环境中简单验证一下！**

> **由此可得，实例化对象中只存储了int变量，并没有所谓的指针。看看第三种！**

#### **6.3.3** 只保存成员变量，成员函数存放在公共的代码段

> 类对象模型将成员变量保存在对象的内存中，而成员函数通常存放在公共的代码段中（也称为代码区、代码段或文本段）。这种设计有助于节省内存空间，因为成员函数在不同的对象实例之间是共享的，而不需要每个对象都保存一份。

**当你调用一个类的成员函数时，实际上是通过对象的指针来访问公共的代码段中的函数代码。具体的调用过程如下：**

1. 创建类的对象：通过类的构造函数创建一个新的对象实例，其中包括成员变量的内存。
2. 使用对象的指针：要调用对象的成员函数，你需要使用指向该对象的指针。
3. 访问成员函数：通过指针访问对象的成员函数。使用箭头运算符（->）来访问通过指针指向的对象的成员函数。

```cpp
#include <iostream>

class MyClass {
public:
    int myVar;

    void nonVirtualFunction() {
        std::cout << "This is a non-virtual function" << std::endl;
    }

    virtual void virtualFunction() {
        std::cout << "This is a virtual function" << std::endl;
    }
};

int main() {
    MyClass obj1;
    MyClass obj2;

    obj1.nonVirtualFunction(); // 调用非虚函数
    obj1.virtualFunction(); // 调用虚函数

    MyClass* ptr = &obj2;
    ptr->nonVirtualFunction(); // 使用指针调用非虚函数
    ptr->virtualFunction(); // 使用指针调用虚函数

    return 0;
}
```

