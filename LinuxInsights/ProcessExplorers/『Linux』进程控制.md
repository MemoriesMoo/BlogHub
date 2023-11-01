# 导言

## 回顾`fork`函数

> `fork()` 函数是一个在`Unix-like`操作系统中常见的系统调用，用于创建一个新的进程，新进程是调用进程（父进程）的副本。新进程与父进程在初始时执行相同的程序，并且拥有相同的代码、数据、堆栈等。

## 函数语法

```bash
#include <unistd.h>

pid_t fork(void);
```

- `pid_t` 是一个整数类型，通常是一个有符号整数，用于表示**进程的ID**（Process ID）。
- `fork()` 函数**返回两次**，一次在父进程中返回子进程的ID，一次在子进程中返回0。

## 基本工作流程

1. 在调用进程（父进程）中，`fork()` 被调用，它会创建一个新的进程（子进程）。
2. 子进程是父进程的几乎完全副本，包括程序代码、数据、打开文件描述符、环境变量等。
3. 子进程在创建时处于与父进程相同的执行位置，即从 `fork()` 调用之后的下一条语句开始执行（**`fork`函数之后有多个执行流**）。
4. 父进程和子进程的区别在于它们具有不同的进程ID（PID）。父进程中 `fork()` 返回子进程的PID，而子进程中 `fork()` 返回0。
5. 子进程可以**独立执行**，修改变量或执行其他操作，而这些操作不会影响父进程（写时拷贝）。

```c
// fork.c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>

int main()
{
    pid_t pid;  // 声明个变量 pid 用于存储进程ID

    printf("Before fork: pid is %d.\n", getpid());  // 打印当前进程的PID

    if ((pid = fork()) == -1)  // 调用 fork() 函数，创建新进程
    {
        perror("fork failed.\n");  // 如果 fork() 返回 -1，则表示创建进程失败
        exit(1);  // 退出程序并返回错误状态
    }

    printf("After fork: pid is %d, fork return %d.\n", getpid(), pid);
    // 打印当前进程的PID和 fork() 返回的值，其中 getpid() 返回当前进程的PID

    return 0;
}
```

```bash
[MeMoo@VM-16-4-centos fork]$ gcc -o fork fork.c 
[MeMoo@VM-16-4-centos fork]$ ./fork 
Before fork: pid is 9280.
After fork: pid is 9280, fork return 9281.
After fork: pid is 9281, fork return 0.
```

## 常见用法

### **并行处理**

> `fork()` 可以用于创建多个并行进程，每个进程执行相同的任务，但可以处理不同的数据或工作负载。这对于并行计算和多核处理器上的任务分发非常有用。

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/type.h>

int main() {
    pid_t pid;
    
    for (int i = 0; i < 4; i++) {
        pid = fork();
        if (pid == 0) {
            printf("Child process %d (PID %d)\n", i + 1, getpid());
            // 子进程执行特定任务
            break;  // 子进程可以根据需要执行不同的任务
        }
    }
    
    if (pid > 0) {
        // 父进程可以执行特定任务
        printf("Parent process (PID %d)\n", getpid());
    }
    
    return 0;
}
```

### **进程控制**

> `fork()` 可用于创建一个新的进程，子进程可以用于执行不同的操作，例如执行程序替换（`exec` 系列函数），监控和管理其他进程，以及执行系统任务。

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork();
    
    if (pid == 0) {
        // 子进程执行特定任务
        execl("/bin/ls", "ls", "-l", NULL);  // 例子：用 ls 命令替换子进程
    } else if (pid > 0) {
        // 父进程等待子进程完成
        wait(NULL);
        printf("Parent process: Child process completed.\n");
    } else {
        perror("fork failed.\n");
    }
    
    return 0;
}
```

## 调用失败

### **资源耗尽**

> 系统中可能没有足够的资源来创建新的进程，这包括可用的内存、进程表项（每个进程需要一个表项）、文件描述符表、堆栈空间等。如果资源不足，`fork()` 可能会**失败并返回-1**。

### **进程数量限制**

> 系统通常会限制同时存在的进程数量，这是为了**防止资源耗尽和系统过度负载**。如果已达到系统的进程数量限制，`fork()` 也会失败。

### **操作系统错误**

> 在某些情况下，**系统可能会出现错误**，导致`fork()` 失败。这可能是由于系统配置问题、内核问题或其他系统故障引起的。

# 写时拷贝

## 概念

> **写时拷贝（Copy-on-Write，COW）**是一种内存管理技术，用于在多个进程之间共享内存页，同时确保数据的隔离和一致性。这个技术的核心思想是在必要时才复制内存页，而不是立即复制。

## 工作原理

1. **初始共享**：在写时拷贝开始时，多个进程共享相同的内存页，这些页通常包括代码段、数据段、共享库等。这些页都标记为**只读**，以确保它们不会被修改。
2. **写操作尝试**：当一个进程尝试在共享的内存页上执行写操作（例如修改变量的值），操作系统内核会捕获这个写操作。
3. **触发复制**：写时拷贝的核心概念：**只有在写操作尝试时才复制内存页**。当写操作发生时，内核会触发写时拷贝机制，以创建一个新的副本（拷贝）的内存页，而不是直接在原始页上执行写操作。
4. **更新页表**：内核会更新进程的页表，将其指向新复制的内存页。这样，进程将在新的页上继续执行写操作。
5. **隔离和一致性**：因为每个进程现在都拥有自己的内存页，所以它们可以**独立**地对这些页执行写操作，而不会影响其他进程。原始页保持不变，以供其他进程继续访问。**这确保了数据的隔离和一致性**

## 代码

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>

int main()
{
    pid_t pid;
    int shared_variable = 42; // 共享的变量

    printf("Before fork: shared_variable is %d, address is %p.\n\n", shared_variable, &shared_variable);

    if ((pid = fork()) == -1)
    {
        perror("fork failed.\n");
        exit(1);
    }

    if (pid == 0)
    { // 子进程
        int count = 2;
        while (count--)
        {
            printf("Child process: shared_variable is %d, address is %p.\n", shared_variable, &shared_variable);
            sleep(1);
        }
        printf("Change share_variable!\n");
        shared_variable = 99; // 在子进程中修改共享变量
        printf("Child process: Modified shared_variable to %d, address is %p.\n", shared_variable, &shared_variable);
    }
    else
    { // 父进程
        int count = 3;
        while (count--)
        {
            printf("Parent process: shared_variable is %d, address is %p.\n", shared_variable, &shared_variable);
            sleep(1);
        }
    }

    return 0;
}
```

```bash
Before fork: shared_variable is 42, address is 0x7ffe41612a64.

Parent process: shared_variable is 42, address is 0x7ffe41612a64.
Parent process: shared_variable is 42, address is 0x7ffe41612a64.
Parent process: shared_variable is 42, address is 0x7ffe41612a64.
Child process: shared_variable is 42, address is 0x7ffe41612a64.
Child process: shared_variable is 42, address is 0x7ffe41612a64.
Change share_variable!
Child process: Modified shared_variable to 99, address is 0x7ffe41612a64.
Child process: shared_variable is 99, address is 0x7ffe41612a64.
Parent process: shared_variable is 42, address is 0x7ffe41612a64.
Parent process: shared_variable is 42, address is 0x7ffe41612a64.
```

- 初始时，父进程和子进程都共享相同的内存页，`shared_variable` 的值和地址相同。
- 子进程在循环中打印 `shared_variable` 的值和地址，然后在循环内部修改了 `shared_variable` 的值。**注意，尽管修改了值，但地址仍然相同。**
- 在父进程中，`shared_variable` 的值和地址仍然保持不变，**即使**子进程已经修改了共享变量的值。

这个示例清晰地演示了写**时拷贝的工作方式**，确保了父子进程之间的**数据隔离**。尽管它们共享相同的内存页，但只有在写操作发生时才会触发内核的复制，保持了数据的一致性和隔离。

# 进程终止

## **正常退出**

- **方法**：进程通过从其主函数返回来正常退出，通常使用 `exit()` 函数，它接受一个整数参数作为**退出状态码**。
- **示例**：

```c
#include <stdlib.h>
#include <stdio.h>

int main() {
    printf("Process is doing some work.\n");
    // 进程的主要工作
    printf("Process completed its task.\n");
    // 正常退出，返回状态码 0
    exit(0);
}
```

正常退出通常用于表示进程成功完成了任务。在这个示例中，进程执行一些工作后，通过调用 `exit(0)` 来正常退出，返回状态码 0。

**此外，`return`与`_exit()`函数也可实现进程正常终止。**

> 一般情况下，退出状态码为0表示进程成功完成了任务，而非零状态码通常用于表示某种错误或异常情况。退出状态码的选择取决于程序员，通常会根据**任务的成功与否**以及可能发生的错误情况来设置状态码。**（正常终止不代表结果正确）**

### `exit()`函数

> `exit()` 函数是用于正常终止一个进程的**标准C库函数**。当一个进程调用 `exit()` 时，它会执行一系列**清理操作**，然后退出。

- **函数原型**：

```c
void exit(int status);
```

- **参数**：`status` 是一个整数，通常用于**指定进程的退出状态码**。这个状态码可以由其他进程或父进程通过 `wait` 系列函数来获取，以了解子进程的退出状态。

- **功能**：`exit()` 函数会终止进程的执行，并执行一些清理操作，包括调用已注册的退出处理程序（使用 `atexit` 函数注册的函数），关闭文件，刷新缓冲区等。它会确保进程终止时的资源释放和数据一致性。

- **用途**：`exit()` 通常用于表示进程已成功完成任务或以某种方式退出，它允许进程执行一些必要的清理操作。

示例代码：

```c
#include <stdlib.h>

int main() {
    // 执行一些工作
    // 然后使用 exit() 终止进程
    exit(0);
}
```

示例中，进程执行了一些工作后，使用 `exit(0)` **正常退出**，返回状态码 0，表示成功完成任务。

`exit()` 函数是进程正常终止的推荐方式，因为它确保资源的释放和数据的一致性。当进程成功完成任务时，通常应使用 `exit()` 来终止，而不是 `_exit()` 或 `abort()`，除非出现无法恢复的错误或异常情况。

### `_exit()`函数

> `_exit()` 函数是用于终止进程的**系统调用**，它立即终止当前进程的执行，而**不进行标准的C库终止处理**。这意味着它会立即终止进程，而不会执行退出处理程序、关闭文件、刷新缓冲区等操作。`_exit()` 函数通常用于低级系统编程，而不是通常的应用程序开发。

- **函数原型**：

```c
void _exit(int status);
```

- **参数**：`status` 是一个整数，通常用于指定进程的退出状态码。这个状态码可以由其他进程或父进程通过 `wait` 系列函数来获取，以了解子进程的退出状态。

- **功能**：`_exit()` 函数会立即终止进程的执行，不进行清理操作，包括不执行 `atexit` 注册的退出处理程序。它会导致文件描述符未关闭，缓冲区未刷新，等等。

- **用途**：`_exit()` 通常用于在进程需要快速退出时，例如在出现严重错误时，或在子进程中，当不再需要从父进程继承文件描述符和缓冲区时，或当需要快速退出而不进行标准终止处理时。

示例代码：

```c
#include <unistd.h>

int main() {
    // 执行一些工作
    // 然后使用 _exit() 终止进程
    _exit(1);
}
```

在这个示例中，进程执行了一些工作后，使用 `_exit(1)` 立即终止进程，返回状态码 1。

通常情况下，建议使用 `exit()` 而不是 `_exit()`，因为 `exit()` 会执行标准的C库终止处理，确保资源被释放，文件被关闭，缓冲区被刷新，而 `_exit()` 不会执行这些操作。

## **异常退出**

- **方法**：如果进程遇到无法处理的错误或异常情况，它可以通过调用 `abort()` 函数来异常终止。这将导致进程立即终止，并生成一个核心转储文件以供调试。
- **示例**：

```c
#include <stdlib.h>

int main() {
    // 进程遇到异常情况
    // 异常退出
    abort();
}
```

- **用途**：用于在发生**无法恢复的错误或异常情况**时强制终止进程。

## **信号终止**

- **方法**：进程可以被其他进程发送的信号终止，例如，`SIGTERM` 和 `SIGKILL` 是两个常见的信号，前者允许进程进行清理工作，后者会立即终止进程。
- **示例**：

```bash
# 使用 kill 命令终止进程
kill -TERM <进程ID>

# 使用编程方式发送信号
#include <signal.h>
kill(<进程ID>, SIGTERM);
```

- **用途**：用于控制进程的终止，例如，允许进程进行清理操作或立即终止进程。

**调用 exec 函数**：

- **方法**：当一个进程调用 `exec` 系列函数时，它会被新程序替代。原来的进程将被终止，而新程序将开始执行。
- **示例**：

```c
#include <unistd.h>

int main() {
    // 原进程执行的工作
    // ......
    // 调用 exec 函数，终止当前进程
    execl("/bin/ls", "ls", "-l", NULL);
}
```

- **用途**：用于**启动一个新程序**，替代当前进程的执行。