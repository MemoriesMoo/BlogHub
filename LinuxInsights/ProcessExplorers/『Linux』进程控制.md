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

# 进程等待

> 在Linux操作系统中，进程管理是一个关键的概念，父进程通常需要等待其子进程完成任务。例如，创建子进程执行任务，子进程在父进程前退出，此时会产生**僵尸进程**， 若不对其进行处理会导致资源浪费，同时，我们也可能需要获取子进程的退出结果等。本章节将介绍如何通过引入僵尸进程来理解进程等待，并深入探讨 `wait` 和 `waitpid` 系等统调用（僵尸进程等概念读者可以阅读之前的进程状态篇）。

## 为何需要等待

> 父进程需要等待其子进程结束，以便**正确处理子进程的退出状态和资源回收**。如果父进程不等待子进程，系统无法清理子进程的退出状态信息，这会导致僵尸进程的累积。进程等待是实现进程之间协同工作、通信和资源管理的重要机制。

## 引入僵尸进程

**简单的示例来演示如何引入僵尸进程**

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main()
{
    // 创建子进程
    pid_t child_pid = fork();

    if (child_pid < 0)
    {
        perror("Fork fail!\n");
        exit(1);
    }

    if (child_pid == 0)
    {
        // 子进程
        int cnt = 5;
        while (cnt)
        {
            printf("I'am child process, pid:%d, ppid:%d, cnt:%d\n", getpid(), getppid(), cnt--);
            sleep(1);
        }
        exit(0);
    }
    else if (child_pid > 0)
    {
        // 父进程
        sleep(10); // 父进程休眠10秒，模拟未等待子进程的情况
    }

    return 0;
}
```

### **运行结果**

```bash
[MeMoo@VM-16-4-centos wait]$ gcc -o wait wait.c -std=c99
[MeMoo@VM-16-4-centos wait]$ ./wait 
I'am child process, pid:28671, ppid:28670, cnt:5
I'am child process, pid:28671, ppid:28670, cnt:4
I'am child process, pid:28671, ppid:28670, cnt:3
I'am child process, pid:28671, ppid:28670, cnt:2
I'am child process, pid:28671, ppid:28670, cnt:1
[MeMoo@VM-16-4-centos wait]$ 
```

### **脚本监控**

```bash
[MeMoo@VM-16-4-centos wait]$ while :; do
>   ps axj | head -1
>   ps axj | grep wait | grep -v grep
>   sleep 1
> done
// 前五秒子进程父进程正常运行
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
// 此处开始子进程退出，父进程正常运行，僵尸进程产生
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 Z+    1002   0:00 [wait] <defunct>
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 Z+    1002   0:00 [wait] <defunct>
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 Z+    1002   0:00 [wait] <defunct>
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 Z+    1002   0:00 [wait] <defunct>
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820 28670 28670 23820 pts/4    28670 S+    1002   0:00 ./wait
28670 28671 28670 23820 pts/4    28670 Z+    1002   0:00 [wait] <defunct>
// 此处父进程也退出，但是没有对僵尸进程进行处理，
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
```

> 在这个示例中，父进程创建了一个子进程，但它**没有等待子进程**，而是休眠10秒，这导致子进程运行大概五秒退出后成为僵尸进程。从脚本结果的后半部分可以看出，子进程处于僵尸状态（**Z+**），直至父进程结束任然没有被处理。

## **`wait` 系统调用**

> `wait` 函数是一个系统调用，用于**等待子进程结束并获取其退出状态**。

```c
#include <sys/types.h>
#include <sys/wait.h>

pid_t wait(int *status);
```

- `status` 参数是一个指向整数的指针，用于存储子进程的退出状态信息。
- `wait` 函数的返回值是已经结束执行的子进程的PID，如果没有子进程结束执行或者发生错误，返回值为 `-1`。

**让我们使用`wait`函数对上面的僵尸进程示例进行等待处理：**

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main()
{
    // 创建子进程
    pid_t child_pid = fork();

    if (child_pid < 0)
    {
        perror("Fork fail!\n");  // 如果fork失败，输出错误信息
        exit(1);
    }

    if (child_pid == 0)
    {
        // 子进程
        int cnt = 5;
        while (cnt)
        {
            printf("I'm the child process, pid:%d, parent pid:%d, count:%d\n", getpid(), getppid(), cnt--);
            sleep(1);
        }
        exit(0); // 子进程退出
    }
    else if (child_pid > 0)
    {
        // 父进程
        sleep(10); // 父进程休眠10秒，模拟未等待子进程的情况

        // 获取子进程退出状态
        int status = 0;
        pid_t res = wait(&status); // 等待子进程退出，并获取退出状态
        if (res > 0)
        {
            printf("Wait successfully! Child exit code:%d, signal:%d\n", (status >> 8) & 0xFF, status & 0x7F);
            // 输出等待成功的信息以及子进程的退出状态和信号
        }
    }

    return 0;
}
```

**`ps:` 此处的`status`参数后续进行讲解，此处先使用其来证明子进程被等待。**

### 运行结果

```bash
[MeMoo@VM-16-4-centos wait]$ gcc -o wait wait.c -std=c99
[MeMoo@VM-16-4-centos wait]$ ./wait 
I'm the child process, pid:2057, parent pid:2056, count:5
I'm the child process, pid:2057, parent pid:2056, count:4
I'm the child process, pid:2057, parent pid:2056, count:3
I'm the child process, pid:2057, parent pid:2056, count:2
I'm the child process, pid:2057, parent pid:2056, count:1
Wait successfully! Child exit code:0, signal:0
[MeMoo@VM-16-4-centos wait]$ 
```

### 脚本监控

```bash
[MeMoo@VM-16-4-centos wait]$ while :; do
>   ps axj | head -1
>   ps axj | grep wait | grep -v grep
>   sleep 1
> done
// 前五秒子进程父进程正常运行
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 // 此处开始子进程退出，父进程正常运行，僵尸进程产生
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 Z+    1002   0:00 [wait] <defunct>
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 Z+    1002   0:00 [wait] <defunct>
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 Z+    1002   0:00 [wait] <defunct>
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 Z+    1002   0:00 [wait] <defunct>
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 2056  2057  2056 23820 pts/4     2056 Z+    1002   0:00 [wait] <defunct>
 // 此处父进程对僵尸进程进行等待处理
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
23820  2056  2056 23820 pts/4     2056 S+    1002   0:00 ./wait
// 此处父进程也正常退出
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
```

> 在父进程中，`wait` 调用会等待子进程结束，获取其退出状态，并存储在 `status` 变量中。

## `waitpid` 函数

> `waitpid` 函数也用于等待子进程结束，但它提供了**更多的控制选项。**

```c
#include <sys/types.h>
#include <sys/wait.h>

pid_t waitpid(pid_t pid, int *status, int options);
```

- `pid` 参数用于指定等待的子进程，可以使用不同值，**如 `0` 表示等待任意子进程**，**特定的进程ID表示等待具体的子进程**。
- `status` 参数是一个指向整数的指针，用于存储子进程的退出状态信息（输出型参数）。
- `options` 参数是一个整数，允许设置各种选项，如非阻塞等待等。
- `waitpid` 函数的返回值是已经结束执行的子进程的PID，如果没有子进程结束执行或者发生错误，返回值为 `-1`。

**代码与`wait`大同小异，仅仅变换函数接口。**

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main()
{
    // 创建子进程
    pid_t child_pid = fork();

    if (child_pid < 0)
    {
        perror("Fork fail!\n"); // 如果fork失败，输出错误信息
        exit(1);
    }

    if (child_pid == 0)
    {
        // 子进程
        int cnt = 5;
        while (cnt)
        {
            printf("I'm the child process, pid:%d, parent pid:%d, count:%d\n", getpid(), getppid(), cnt--);
            sleep(1);
        }
        exit(0); // 子进程退出
    }
    else if (child_pid > 0)
    {
        // 父进程
        sleep(10); // 父进程休眠10秒，模拟未等待子进程的情况

        // 获取子进程退出状态
        int status = 0;
        pid_t res = waitpid(child_pid, &status, 0); // 等待子进程退出，并获取退出状态
        if (res > 0)
        {
            printf("Wait successfully! Child exit code:%d, signal:%d\n", (status >> 8) & 0xFF, status & 0x7F);
            // 输出等待成功的信息以及子进程的退出状态和信号
        }

        // 便于脚本查看子进程是否正常被等待成功
        sleep(5);
    }

    return 0;
}
```

## 运行结果

```c
[MeMoo@VM-16-4-centos wait]$ gcc -o wait wait.c -std=c99
[MeMoo@VM-16-4-centos wait]$ ./wait 
I'm the child process, pid:4687, parent pid:4686, count:5
I'm the child process, pid:4687, parent pid:4686, count:4
I'm the child process, pid:4687, parent pid:4686, count:3
I'm the child process, pid:4687, parent pid:4686, count:2
I'm the child process, pid:4687, parent pid:4686, count:1
Wait successfully! Child exit code:0, signal:0
[MeMoo@VM-16-4-centos wait]$ 
```

- 在这个示例中，父进程使用 `waitpid` 函数等待特定的子进程，分别获取它们的退出状态。

**`ps:` 此处不再对脚本结果进行展示，读者可自行验证。**

> 这些函数是在Linux中用于进程等待和处理子进程退出状态的重要工具。使用 `wait` 和 `waitpid` 函数，父进程可以等待子进程的结束，获取其退出状态，从而正确处理进程间的协同工作。

## **`status`**参数位图结构

> **在Linux系统中，`status` 是一个整数，用于存储子进程的退出状态信息。**

- **32位系统上**，`status` 是一个32位整数，其结构如下：

```bash
 31                            8 7 6 5 4 3 2 1 0
+---------------------------------------------+
|            退出状态码         |    退出方式   |
+---------------------------------------------+
```

- **64位系统上**，`status` 是一个64位整数，结构如下：

```bash
 63                            32 31                            8 7 6 5 4 3 2 1 0
+-----------------------------+---------------------------------------------+
|      保留位 (0)            |            退出状态码         |    退出方式   |
+-----------------------------+---------------------------------------------+
```

无论是32位还是64位系统，`退出状态码` 表示子进程的退出状态，通常是子进程的 `main` 函数中返回的整数值。这个状态码**通常在0到255之间**，所以在上面获取子进程退出码时使用`(status >> 8) & 0xFF`，但可以有其他值。

`退出方式` 表示子进程的退出方式，其中不同的位表示不同的信息。具体来说：

- **低7位**用于包含信号编号，如果子进程是因为**信号终止**而退出的。**如果子进程正常退出，这些位将被清零。**
- 最高位（**第8位**）用于表示退出方式，如果子进程正常退出，这一位将被设置为1，否则为0。正常退出表示子进程通过调用 `exit` 函数（`_exit`函数）或 `return` 语句退出。

这些位图结构的设计允许在 `status` 中存储有关子进程退出的多种信息，包括退出状态码和退出方式。父进程可以使用 `wait` 或 `waitpid` 函数来解释这些位，以确定子进程的退出状态和方式。通常，`WIFEXITED(status)` 和 `WEXITSTATUS(status)` 可以用于检查和提取这些信息。如果子进程是由信号终止的，可以使用 `WIFSIGNALED(status)` 和 `WTERMSIG(status)` 来获取更多关于终止子进程的信息。

> 总之，32位和64位系统上的 `status` 结构相似，但64位系统使用更多的位来存储信息，以容纳更多的信息。

**status获取相关信息的方式：**

`(status >> 8) & 0xFF` 和 `status & 0x7F` 是用于提取 `status` 变量中不同部分的位掩码操作。这些操作通常用于获取子进程的退出状态和退出方式的详细信息。

- `(status >> 8) & 0xFF` 的作用是右移 `status` 中的位8位，并使用掩码 `0xFF`（二进制为 11111111）来保留最低8位。这将提取 `status` 中的退出状态码部分。
- `status & 0x7F` 使用掩码 `0x7F`（二进制为 01111111）来保留 `status` 中的最低7位，这些位通常用于存储子进程的终止信号编号。

具体来说，这些操作可以用于以下情况：

- `(status >> 8) & 0xFF` 用于提取子进程的退出状态码。退出状态码通常是子进程 `main` 函数返回的整数值，它描述了子进程的正常终止状态。例如，如果子进程以 `exit(42)` 退出，那么 `(status >> 8) & 0xFF` 将提取出值42。
- `status & 0x7F` 用于提取子进程的退出方式，通常是终止信号编号。如果子进程是由于接收了信号而终止，这个操作将提取出相应的信号编号。例如，如果子进程因为接收到 `SIGTERM` 信号而终止，那么 `status & 0x7F` 将提取出 `SIGTERM` 信号的编号。

> 这两个操作一起允许父进程获取子进程退出的详细信息，包括退出状态码和退出方式（是否是信号终止），以便进一步处理和分析子进程的退出情况。

**相关退出信息获取系统接口：**

### `WIFEXITED(status)`：

```c
int WIFEXITED(int status);
```

- `WIFEXITED` 是一个宏，用于检查 `status` 变量是否表示子进程正常退出。
- `status` 是包含子进程退出信息的整数，通常是从 `wait` 或 `waitpid` 中获取的。
- 返回值是一个非零值（真），表示子进程正常退出；如果返回0，表示子进程不是正常退出。

使用示例：

```c
if (WIFEXITED(status)) {
    // 子进程正常退出
    // 可以使用 WEXITSTATUS 获取子进程的退出状态码
} else {
    // 子进程不是正常退出，可能是因为接收信号而终止
}
```

### `WEXITSTATUS(status)`：

```c
int WEXITSTATUS(int status);
```

- `WEXITSTATUS` 用于获取子进程的退出状态码，这通常是子进程的 `main` 函数的返回值。
- `status` 是包含子进程退出信息的整数，通常是从 `wait` 或 `waitpid` 中获取的。
- 返回值是子进程的退出状态码。

使用示例：

```c
if (WIFEXITED(status)) {
    int exit_status = WEXITSTATUS(status);
    printf("子进程正常退出，退出状态码: %d\n", exit_status);
} else {
    // 处理子进程不是正常退出的情况
}
```

### `WIFSIGNALED(status)`：

```c
int WIFSIGNALED(int status);
```

- `WIFSIGNALED` 是一个宏，用于检查 `status` 变量是否表示子进程因接收信号而终止。
- `status` 是包含子进程退出信息的整数，通常是从 `wait` 或 `waitpid` 中获取的。
- 返回值是一个非零值（真），表示子进程因接收信号而终止；如果返回0，表示子进程不是因信号而终止。

使用示例：

```c
if (WIFSIGNALED(status)) {
    // 子进程因接收信号而终止
    // 可以使用 WTERMSIG 获取信号编号
} else {
    // 子进程正常退出或其他情况
}
```

### `WTERMSIG(status)`：

```c
int WTERMSIG(int status);
```

- `WTERMSIG` 用于获取导致子进程终止的信号的编号。
- `status` 是包含子进程退出信息的整数，通常是从 `wait` 或 `waitpid` 中获取的。
- 返回值是导致子进程终止的信号的编号。

使用示例：

```c
if (WIFSIGNALED(status)) {
    int signal_number = WTERMSIG(status);
    printf("子进程因信号 %d 而终止\n", signal_number);
} else {
    // 子进程正常退出或其他情况
}
```

> 这些函数和宏一起用于检查和提取子进程的退出状态和方式，以便根据不同情况进行相应的处理。

# 进程替换

> Linux中的进程替换是指一个正在运行的进程**被另一个程序替代为新的进程**。进程替换通常用于执行不同的程序，而不是创建一个新的进程来运行它。这可以用于执行新版本的程序、切换到不同的应用程序，或者在不关闭当前进程的情况下切换到不同的任务。**进程替换是在一个现有进程的上下文中进行的**，因此它继承了许多属性，如进程ID、环境变量等。

**在Linux中，可以使用以下方法进行进程替换：**

## `exec`函数族

> exec函数族（如`execv`、`execp`、`execl`等）是用于执行新程序的标准方法。这些函数可以在当前进程中加载一个新的可执行文件，并将控制传递给新程序。在替换进程时，当前进程的内存映像和资源将被替换为新程序的内容。

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    char *args[] = {"ls", "-l", NULL};
    execvp("ls", args);
    return 0; // 这一行不会执行，因为进程已经被替换
}
```

**示例将当前进程替换为运行`ls -l`命令的新进程。**

> `exec`函数族是用于在`Linux`和`UNIX`系统上执行新程序的一组函数。它们允许一个进程在不创建新进程的情况下替换自身的映像，从而执行不同的程序。**`exec`函数族包括多个不同的函数，它们的接口略有不同，以满足不同的需求。**以下是一些常见的`exec`函数：

1. `int execl(const char *path, const char *arg0, ... /* (char *) NULL */);`
   
   - 这个函数接受**可变数量的字符串参数**，最后一个参数**必须**是`NULL`。它用于执行一个指定路径的可执行文件，传递给新程序的参数在参数列表中指定。
   
   ```c
   #include <stdio.h>
   #include <unistd.h>
   
   int main() {
       execl("/bin/ls", "ls", "-l", NULL);
       return 0; // 这一行不会执行，因为进程已被替换（除非参数传递错误）
   }
   ```
   
2. `int execv(const char *path, char *const argv[]);`
   
   - 这个函数接受**可执行文件的路径**和一个**字符串数组**作为参数，数组中的第一个元素通常是可执行文件的名称，后面跟随命令行参数。
   
   ```c
   #include <stdio.h>
   #include <unistd.h>
   
   int main() {
       char *args[] = {"ls", "-l", NULL};
       execv("/bin/ls", args);
       return 0; // 这一行不会执行，因为进程已被替换
   }
   ```
   
3. `int execle(const char *path, const char *arg0, ... /* (char *) NULL, char *const envp[] */);`
   
   - 这个函数与`execl`类似，但它还允许您传递一个**环境变量数组**。
   
   ```c
   #include <stdio.h>
   #include <unistd.h>
   
   int main() {
       char *args[] = {"ls", "-l", NULL};
       char *env[] = {"PATH=/usr/bin", NULL};
       execle("/bin/ls", "ls", "-l", NULL, env);
       return 0; // 这一行不会执行，因为进程已被替换
   }
   ```
   
4. `int execvp(const char *file, char *const argv[]);`
   - 这个函数与`execv`类似，但它会在`PATH`环境变量中查找可执行文件，而**不需要提供完整的文件路径**。

   ```c
   #include <stdio.h>
   #include <unistd.h>
   
   int main() {
       char *args[] = {"ls", "-l", NULL};
       execvp("ls", args);
       return 0; // 这一行不会执行，因为进程已被替换
   }
   ```

5. `int execve(const char *path, char *const argv[], char *const envp[]);`
   - 这个函数接受**可执行文件的路径**、**参数数组**和**环境变量数组**，它提供了**最灵活的选项**。

   ```c
   #include <stdio.h>
   #include <unistd.h>
   
   int main() {
       char *args[] = {"ls", "-l", NULL};
       char *env[] = {"PATH=/usr/bin", NULL};
       execve("/bin/ls", args, env);
       return 0; // 这一行不会执行，因为进程已被替换
   }
   ```

> 这些`exec`函数族的**主要区别在于参数传递方式和是否支持环境变量的传递**。在调用这些函数后，当前进程的映像将被替换为新程序，新程序将从`main`函数或其他入口点开始执行，原进程的上下文将**被丢弃**。这使得进程可以在不创建新进程的情况下执行新的程序，非常有用，**例如在shell中执行命令时，shell会使用`exec`来启动命令。**

**`exec`函数族的不同函数之间主要有以下区别：**

1. **接口参数差异**：
   - `execl`和`execlp`接受可变数量的字符串参数，最后一个参数必须是`NULL`。这使得它们适合于在编译时已知参数的情况。
   - `execv`和`execvp`接受一个**字符串数组**（通常称为`argv`）作为参数，第一个元素通常是可执行文件的名称，后跟命令行参数。这使它们更适合于运行时动态生成参数的情况。
   - `execle`和`execve`与上述函数类似，但还接受一个额外的环境变量数组（通常称为`envp`）作为参数，**以允许在新程序中自定义环境变量**。

2. **文件查找方式**：
   - `execl`和`execle`需要提供完整的文件路径，因为它们不会查找`PATH`环境变量中的可执行文件。
   - `execv`和`execve`需要提供完整的文件路径，因此也不会查找`PATH`环境变量中的可执行文件。
   - `execlp`和`execvp`会查找`PATH`环境变量中的可执行文件，因此**只需要提供可执行文件的名称即可。**

3. **环境变量传递**：
   - `execl`和`execv`不支持传递环境变量数组，因此新程序将继承当前进程的环境变量。
   - `execle`和`execve`允许传递**一个自定义的环境变量数组，以覆盖或添加到新程序的环境变量。**
   - `execlp`和`execvp`不支持传递环境变量数组，新程序将继承当前进程的环境变量。

4. **文件查找方式**：
   - `execl`和`execlp`是以列表方式传递参数的。
   - `execv`和`execvp`是以数组方式传递参数的。

> 总的来说，可以根据具体的需求来选择适合的`exec`函数。**如果需要更多的灵活性和控制，`execve`通常是最合适的选择，因为它允许自定义参数和环境变量，并需要提供完整的文件路径。**如果只需要执行一个外部程序，而不需要自定义环境变量，`execlp`和`execvp`可以更加简便，因为它们会查找`PATH`中的可执行文件。

## `fork`和`exec`

> 另一种进行进程替换的方法是使用`fork`创建一个子进程，然后在**子进程中**使用`exec`来替换新的程序。

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t child_pid = fork();
    if (child_pid == 0) {
        // 这是子进程
        char *args[] = {"ls", "-l", NULL};
        execvp("ls", args);
    } else if (child_pid > 0) {
        // 这是父进程，可以进行其他操作
        // 等待子进程完成
        wait(NULL);
    } else {
        // 处理fork失败的情况
        perror("Fork failed");
    }
    return 0;
}
```

> 至此，我们可以利用现有的知识制作一个简易`shell`，具体代码与解析读者可移步笔者`GitHub`查看详情。
