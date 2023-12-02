---
title: 类与对象（下）
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-19.webp
description: "\U0001F389面向过程与面向对象，类与对象深入。"
mathjax: true
tags:
  - 类与对象
categories:
  - CPP
abbrlink: 746ee5e2
date: 2023-11-15 00:00:00
updated: 2023-11-15 00:00:00
swiper_index:
---
## 一、 this指针

### 1.1 引入

首先看一段代码

```cpp
#include <iostream>

class Person
{
public:
    void Init(const std::string& name, int age)
    {
        _name = name;
        _age = age;
    }

    void Print()
    {
        std::cout << "Name: " << _name << ", Age: " << _age << std::endl;
    }

private:
    std::string _name;
    int _age;
};

int main()
{
    Person p1, p2;
    p1.Init("John", 30);
    p2.Init("Alice", 25);
    p1.Print(); // Output: Name: John, Age: 30
    p2.Print(); // Output: Name: Alice, Age: 25
    return 0;
}
```

### **1.2 问题**

> **Person类**中有 **Init** 与 **Print** 两个成员函数，函数体中没有关于不同对象的区分，那么是如何区分是哪一个对象进行的调用呢？

C++设计者们提出使用**this指针**解决该问题，当我们调用成员函数时，C++编译器会在内部为每个**非静态成员函数**增加一个隐藏的指针参数，即**`this`指针**。这个`this`指针是一个**指向当前对象的地址的常量指针**，它指向调用该成员函数的对象。在函数体中，所有对成员变量的操作都是通过`this`指针来访问的。只不过所有的操作对用户是透明的，即用户不需要来传递，编译器自动完成。

> **图片还未上传**

 **如图所示，我们定义类成员函数时，应当像上面的第一种格式定义，第二种是编译器对自动在参数列表传递指向调用该非静态成员函数的成员的常量指针，上图只是为了让读者体会到该过程，在函数内部，可以直接使用成员属性，当函数形参名称与成员属性发生冲突时，使用this->成员属性的方式可进行区分!!!**

> 在成员函数中，我们可以使用`this`指针来访问当前对象的成员变量和成员函数。例如，如果类中存在一个成员变量和函数都叫做`value`，我们可以使用`this->value`来明确表示访问的是成员变量而不是函数。此外，通过在成员函数中返回`*this`，我们可以实现链式调用，提高代码的可读性和简洁性。

### 1.3 特性

- `this`指针的类型为`类类型* const`，即成员函数中不能给`this`指针赋值，因为它指向当前对象的地址，不允许指向其他对象。
- `this`指针只能在成员函数的内部使用，不能在类的非成员函数或全局函数中使用。
- `this`指针本质上是成员函数的一个隐含形参，在对象调用成员函数时，编译器会将对象的地址作为实参传递给`this`指针。因此，对象本身不存储`this`指针。

## 二、 构造函数

> **空类**：类中无任何成员属性和成员函数。
>
> 在C++中，如果你未在类中定义，类会自动为你生成一些默认的成员函数，如果你没有显式地定义它们。这些默认成员函数包括：
>
> - **默认构造函数 (Default Constructor)**
> - **默认析构函数 (Default Destructor)**
> - **默认拷贝构造函数(Default Copy Constructor)**
> - **默认赋值运算符 (Default Copy Assignment Operator)**
> - **默认移动构造函数 (Default Move Constructor)**
> - **默认移动赋值运算符 (Default Move Assignment Operator)**
>
> **本文我们主要介绍前面四种函数，后续介绍其他函数。**

### 2.1 概念

我们可以引用上面的代码来展开介绍。

```cpp
class Person
{
public:
    void Init(const std::string& name, int age)
    {
        _name = name;
        _age = age;
    }

    void Print()
    {
        std::cout << "Name: " << _name << ", Age: " << _age << std::endl;
    }

private:
    std::string _name;
    int _age;
};
```

**在我们创建对象时，每次都要显示调用Init函数去给对象属性赋值，但如果每次创建对象时都调用该方法设赋值，显得略为繁琐，那能否在对象创建时，就将信息设置进去呢？便产生了我们的构造函数。**

> - **作用：**用于创建对象时初始化成员变量的默认值。
> - **使用情况：**当你创建一个类对象时，如果没有显式地提供构造函数，编译器会为你自动生成一个默认构造函数。默认构造函数没有参数，它将成员变量初始化为其对应类型的默认值（例如，数值类型为0，指针类型为nullptr，类对象的成员会再调用它们自己的构造函数来初始化）。**在不同的编译器中可能实现不同，有些编译器对内置类型并不处理，是随机值，类对象的成员会调用他们的构造函数。**
> - 构造函数是一个特殊的成员函数，名字与类名相同,创建类类型对象时由编译器自动调用，以保证每个数据成员都有一个合适的初始值，并且在对象整个生命周期内**只调用一次。**

### 2.2 特性

> 构造函数是特殊的成员函数，需要注意的是，构造函数虽然名称叫构造，但是构造函数的主要任务并不是开空间创建对象，而是初始化对象，对对象内的成员属性进行初始化。
>
> **特征：**
>
> - 1. 函数名与类名相同。
> - 2. 无返回值。
> - 3. 对象实例化时编译器自动调用对应的构造函数。
> - 4. 构造函数可以**重载**。

### 2.3 语法

```cpp
#include <iostream>
#include <string>

class Person
{
public:
    // 1.无参构造函数
    Person()
    {}

    // 2.带参构造函数
    Person(const std::string& name, int age)
    {
        _name = name;
        _age = age;
    }

    // 3.打印个人信息
    void PrintInfo()
    {
        std::cout << "Name: " << _name << ", Age: " << _age << std::endl;
    }

private:
    std::string _name;
    int _age;
};
```

由上述代码可知，我们暂时可以将构造函数可以分为无参构造函数和有参构造函数，我们来看一看如何使用这两个参数对对象进行初始化。

```cpp
int main()
{
    // 调用无参构造函数创建对象
    Person p1;
    p1.PrintInfo();

    // 调用带参构造函数创建对象
    Person p2("John", 30);
    p2.PrintInfo(); // Output: Name: John, Age: 30

    return 0;
}
```

> **图片未上传**

使用无参构造初始化对象时，直接使用**类名+对象名**即可，使用带参构造时要传入对应的参数用来初始化成员属性。

**提醒**

```cpp
Person person();
```

**使用无参构造实例化对象是，不要在对象后加上（），这样会导致编译器无法认定这是使用无参构造实例化对象还是声明返回值是Person类型的函数，最好不要这样使用！！！**

> **如果我们在类定义时不主动写构造函数，无论无参还是有参，系统会自动生成默认构造函数，即无参构造函数。一旦用户显式定义编译器将不再生成，即用户自己实现有参或无参，编译器不再实现默认（无参）构造函数。**

如果将上述代码的无参构造函数注释，只留下有参构造函数，那么只能通过调用有参构造函数实例化对象，不能够使用无参构造函数。

### 2.4 注意点

> 无参的构造函数和全缺省的构造函数都称为默认构造函数，并且默认构造函数只能有一个。 注意：无参构造函数、全缺省构造函数、我们没写编译器默认生成的构造函数，都可以认为 是**默认构造函数**。

```cpp
class Date {
public:
    Date() {
        _year = 1900;
        _month = 1;
        _day = 1;
    }

    Date(int year = 1900, int month = 1, int day = 1) {
        _year = year;
        _month = month;
        _day = day;
    }

private:
    int _year;
    int _month;
    int _day;
};

// 以下测试函数能通过编译吗？
void Test() {
    Date d1;
}
```

**答案是不行的。**

**因为此时无参构造函数和全缺省构造函数均可为此行代码实例化对象，造成二义性，无法编译通过！！！**

## **三、 析构函数**

### **3.1 概念**

> 析构函数是C++中一个特殊的成员函数，用于在对象销毁时进行清理工作和释放资源。它的名称是在类名前加上波浪线(~)，例如，如果类名是`ClassName`，那么析构函数的名称就是`~ClassName`。
>
> 析构函数的作用是进行对象的善后处理工作，当对象的生命周期结束时（比如对象超出作用域、被显式删除或者程序退出），析构函数会自动被调用。

### 3.2 特性

析构函数有以下特点：

1. 析构函数没有返回值，包括`void`，也没有参数。
2. 一个类可以有且只有一个析构函数，而且不能被重载。
3. 如果你没有显式地定义析构函数，编译器会为你自动生成一个默认析构函数。
4. 如果类中有动态分配的资源（如堆上的内存、文件句柄等），在析构函数中应该释放这些资源，避免内存泄漏和资源泄漏。

### 3.3 示例

```cpp
#include <iostream>

class MyClass {
public:
    // 构造函数
    MyClass() {
        std::cout << "Constructor called." << std::endl;
    }

    // 析构函数
    ~MyClass() {
        std::cout << "Destructor called." << std::endl;
    }
};

int main() {
    std::cout << "Creating object..." << std::endl;
    MyClass obj; // 创建对象，调用构造函数

    std::cout << "Object will be destroyed..." << std::endl;
    // 在这里，obj超出了作用域，对象的生命周期结束，析构函数被自动调用

    return 0;
}
```

```
Creating object...
Constructor called.
Object will be destroyed...
Destructor called.
```

> 这证明了对象的构造函数和析构函数分别在对象的创建和销毁时被调用。析构函数的调用可以确保对象在销毁时完成必要的清理工作，释放资源，避免资源泄漏。

**提醒：**如果不手动编写析构函数，系统也会自动生成析构函数，但是系统自带的析构函数时空实现，不做任何事。当然，如果类对象内部不存在堆区开辟的空间，使用系统生成的即可。但如果有堆区开辟的空间，需要在析构函数内部手动释放，否则容易造成内存泄漏！！！其次，类对象中如果有其他类成员属性，会在该对象销毁时，自动调用类成员属性的析构函数，不需要在该类对象的析构函数中管理！！！

## **四、拷贝构造函数**

### 4.1 概念

> 拷贝构造函数是C++中的一种特殊构造函数，用于在对象进行复制时创建一个新对象，并将原对象的值拷贝给新对象。它的作用是生成一个新的对象，这个新对象与原对象的内容相同，但是它们是独立的，修改一个对象的内容不会影响另一个对象。

> 如果你没有显式地定义拷贝构造函数，编译器会为你生成一个默认的拷贝构造函数。默认拷贝构造函数会逐个拷贝成员变量的值，**并**为类中的指针成员进行**浅拷贝**（即复制指针的值而不是复制指针指向的对象）。如果类中有资源需要**深拷贝**（如动态分配的内存），则需要自己定义拷贝构造函数来完成深拷贝，否则在析构函数中会对一块空间重复释放导致错误。

### 4.2 特性ClassName(const ClassName& other);**拷贝构造函数是构造函数的一个重载形式。**

- **拷贝构造函数的参数只有一个且必须是类类型对象的引用，使用传值方式编译器直接报错， 因为会引发无穷递归调用。**

```cpp
ClassName(const ClassName& other);
```

### 4.3 示例

假设有一个类`MyClass`，并且我们试图定义一个错误的拷贝构造函数，使用传值方式来接收参数：

> 在上面的例子中，我们定义了一个名为`MyClass`的类，并试图使用传值方式来定义拷贝构造函数。当我们尝试使用拷贝构造函数创建`obj2`对象时，会导致无限递归调用，从而导致栈溢出。
>
> 这种情况发生的原因是：传值方式会调用拷贝构造函数本身，以创建传递的参数的副本，然后在调用拷贝构造函数的过程中又会再次创建参数的副本，导致无限递归。
>
> 为了避免无限递归调用，拷贝构造函数的参数必须使用引用方式接收，这样在拷贝构造函数调用时只会传递对象的引用，而不会创建新的副本。

以下是修正后的示例代码，使用引用方式定义正确的拷贝构造函数：

```cpp
#include <iostream>

class MyClass {
public:
    // 正确的拷贝构造函数
    MyClass(const MyClass& obj) {
        std::cout << "Copy constructor called." << std::endl;
    }
};

int main() {
    MyClass obj1;
    MyClass obj2 = obj1; // 正确，使用引用方式传递参数
    MyClass obj3(obj2);    //此种方式也可以

    return 0;
}
```

### 4.4 深浅拷贝

> **浅拷贝（Shallow Copy）**： 浅拷贝是指在拷贝对象时，仅仅是复制对象中的成员变量的值，包括指针成员变量的值。这意味着新对象和原对象会共享相同的资源，而不是为新对象创建独立的资源副本。如果原对象中包含指向堆内存的指针成员，浅拷贝后新对象和原对象的指针成员指向同一块堆内存，造成了两个对象对同一资源的管理，可能会导致资源释放问题和潜在的错误。

> **深拷贝（Deep Copy）**： 深拷贝是指在拷贝对象时，会为新对象创建一个独立的资源副本，而不是共享资源。如果原对象中有指向堆内存的指针成员，深拷贝会为新对象的指针成员单独分配内存，将原对象指针所指向的内容复制到新的内存中。这样两个对象就拥有各自独立的资源，修改一个对象的资源不会影响另一个对象。

```cpp
#include <iostream>
#include <cstring>
#include <cstdlib>

class Person {
public:
    // 构造函数
    Person(const char* name, int age) {
        _name = (char*)malloc(strlen(name) + 1);
        strcpy(_name, name);
        _age = age;
    }

    // 拷贝构造函数（浅拷贝）
    Person(const Person& other) {
        _name = other._name; // 浅拷贝，共享资源
        _age = other._age;
    }

    // 深拷贝构造函数（深拷贝）
    Person(const Person& other) {
        _name = (char*)malloc(strlen(other._name) + 1); // 深拷贝，为新对象分配独立资源
        strcpy(_name, other._name);
        _age = other._age;
    }

    // 析构函数
    ~Person() {
        free(_name);
    }

    // 打印个人信息
    void PrintInfo() {
        std::cout << "Name: " << _name << ", Age: " << _age << std::endl;
    }

private:
    char* _name;
    int _age;
};

int main() {
    // 创建一个Person对象
    Person p1("John", 30);

    // 浅拷贝
    Person p2(p1);
    p1.PrintInfo(); // Output: Name: John, Age: 30
    p2.PrintInfo(); // Output: Name: John, Age: 30

    // 修改p1的值
    p1 = Person("Alice", 25);
    p1.PrintInfo(); // Output: Name: Alice, Age: 25
    p2.PrintInfo(); // Output: Name: Alice, Age: 30（由于浅拷贝，p2共享p1的资源，也被修改为Alice）

    // 深拷贝
    Person p3(p1);
    p1.PrintInfo(); // Output: Name: Alice, Age: 25
    p3.PrintInfo(); // Output: Name: Alice, Age: 25（由于深拷贝，p3拥有独立的资源，不受p1的修改影响）

    return 0;
}
```

## 五、 赋值运算符重载

### 5.1 概念

> 赋值运算符重载是在C++中允许自定义类的成员之间赋值操作的一种特殊函数。通过重载赋值运算符，我们可以实现类对象之间的自定义赋值行为，确保对象的正确复制和资源管理。

### 5.2 语法

```cpp
返回类型 operator=(const 类名& 另一个对象) {
    // 赋值操作的实现
    // 返回对象本身的引用
}
```

**其中，返回类型通常是一个引用类型，这样可以支持连续赋值操作。参数是一个`const`引用，表示传入的赋值运算符右侧的对象。**

### 5.3 示例

```cpp
#include <iostream>
#include <cstring>

class Person {
public:
    Person(const char* name, int age) {
        _name = new char[strlen(name) + 1];
        strcpy(_name, name);
        _age = age;
    }

    // 拷贝构造函数
    Person(const Person& other) {
        _name = new char[strlen(other._name) + 1];
        strcpy(_name, other._name);
        _age = other._age;
    }

    // 赋值运算符重载
    Person& operator=(const Person& other) {
        if (this == &other) { // 自我赋值检测
            return *this;
        }
        delete[] _name; // 释放旧资源

        _name = new char[strlen(other._name) + 1];
        strcpy(_name, other._name);
        _age = other._age;

        return *this; // 返回对象本身的引用
    }

    ~Person() {
        delete[] _name;
    }

    void PrintInfo() {
        std::cout << "Name: " << _name << ", Age: " << _age << std::endl;
    }

private:
    char* _name;
    int _age;
};

int main() {
    Person p1("John", 30);
    Person p2("Alice", 25);

    p1.PrintInfo(); // Output: Name: John, Age: 30
    p2.PrintInfo(); // Output: Name: Alice, Age: 25

    p2 = p1; // 赋值操作

    p1.PrintInfo(); // Output: Name: John, Age: 30
    p2.PrintInfo(); // Output: Name: John, Age: 30（p2被赋值为p1的内容）

    return 0;
}
```

在这个示例中，我们在`Person`类中重载了赋值运算符。在重载函数中，我们首先检查是否发生了自我赋值（对象本身赋值给自己），如果是，则直接返回对象的引用。然后释放旧资源（删除旧的`_name`内存），然后重新分配内存并复制新的内容。