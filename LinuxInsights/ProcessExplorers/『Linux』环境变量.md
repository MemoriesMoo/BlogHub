# 环境变量

## 概念解析

> 在Linux操作系统中，环境变量是一种关键的配置机制，用于存储和传递有关系统和应用程序的信息。它们是以键值对的形式存在的，其中键是环境变量的名称，值是与之相关联的数据。环境变量可以在各种系统级和用户级任务中使用，其主要目的是自定义和配置系统行为以及影响应用程序和进程的运行方式。

## 使用介绍

当谈到Linux环境变量时，有很多方面需要考虑。以下是更详细的介绍：

### **环境变量的分类**

- **用户级环境变量**：每个用户都可以定义和使用的变量，通常存储在用户的主目录中的`.bashrc`或`.bash_profile`文件中。这些变量仅在用户的Shell会话中可见。

- **系统级环境变量**：对整个系统中的所有用户都可见的变量。通常存储在系统范围的配置文件中，例如`/etc/environment`、`/etc/profile`或`/etc/bash.bashrc`。这些变量可以影响系统的整体行为。

### **常见的环境变量**

- **PATH**：包含一组目录路径，用于告诉Shell在哪里查找可执行文件。当您在终端中输入一个命令时，Shell会在`PATH`中列出的目录中查找该命令的可执行文件。

- **HOME**：表示当前用户的主目录的路径。通常用于指示应用程序将配置文件和数据保存在用户的主目录中。

- **USER**：当前登录用户的用户名。

- **LANG**和`LC_*`：用于设置系统的语言环境和区域设置。这些变量影响文本编码、日期格式、货币符号等。

- **PWD**：表示当前工作目录的路径。这个变量会随着您在文件系统中切换目录而变化。

- **SHELL**：指示当前用户使用的默认Shell的路径。

### **设置和导出环境变量**

- 要设置一个环境变量，可以使用`VARIABLE_NAME=value`的语法。例如：`MY_VARIABLE="some_value"`。

- 要使环境变量在**当前Shell会话中可见**，需要使用`export`命令：`export MY_VARIABLE`。

- 若要永久保存环境变量，通常需要将其添加到Shell的配置文件中。用户级变量添加到`~/.bashrc`或`~/.bash_profile`，系统级变量添加到`/etc/environment`等文件中。

### **查看环境变量**

- 使用`env`命令可以列出当前Shell中定义的所有环境变量，以及它们的值。

- 使用`echo $VARIABLE_NAME`可以查看特定环境变量的值。例如，`echo $PATH`将显示`PATH`环境变量的值。

```bash
[Somn@VM-16-4-centos ~]$ echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/Somn/.local/bin:/home/Somn/bin
[Somn@VM-16-4-centos ~]$ echo $HOME
/home/Somn
[Somn@VM-16-4-centos ~]$ echo $SHELL
/bin/bash
```



### **删除环境变量**

- 要删除一个特定的环境变量，可以使用`unset VARIABLE_NAME`命令。例如：`unset MY_VARIABLE`。

### **环境变量的作用**

- 环境变量在各种情况下非常有用，例如在脚本中存储配置信息、指定默认编辑器、设置语言首选项等。

- 它们还用于控制系统行为，例如**通过`PATH`来指定可执行文件的搜索路径**。

- 环境变量还可用于将信息从一个Shell会话传递到另一个Shell会话。

### **示例**

- 设置`JAVA_HOME`环境变量，以指定Java的安装路径：`export JAVA_HOME=/usr/java/jdk1.8.0_291`

- 将新的目录添加到`PATH`环境变量，以使Shell能够找到新的可执行文件：`export PATH=$PATH:/path/to/new/directory`

- 在Shell脚本中使用环境变量来引用配置信息，例如数据库连接字符串或API密钥。

> 总之，Linux环境变量是配置和自定义Linux系统行为的重要工具。了解如何设置、使用和管理这些变量对于在Linux环境中工作和管理系统非常重要。

## 测试PATH

```c
#include <stdio.h>

int main() 
{
  printf("To test env!\n");
  return 0;
}
```

```bash
[Somn@VM-16-4-centos environment]$ gcc -o testEnv testEnv.c 
[Somn@VM-16-4-centos environment]$ ./testEnv 
To test env!
[Somn@VM-16-4-centos environment]$ testEnv
-bash: testEnv: command not found
```

**为什么有些指令可以直接执行，而我们的二进制程序需要带路径才能执行？**

这涉及到Linux系统的**执行文件搜索路径**。当您在终端中输入一个命令时，系统会在一组**预定义的目录中**搜索该命令的**可执行文件**。这些目录包括`/bin`、`/usr/bin`、`/usr/local/bin`等（PATH）。如果您在一个可执行文件的**绝对路径**中运行它（例如`./hello`），那么系统会直接运行该文件。如果您不带路径运行一个命令，系统会按照`$PATH`环境变量中定义的路径顺序来搜索命令。这就解释了为什么有些指令可以直接执行，因为它们的可执行文件位于系统的默认搜索路径中。

**将程序所在路径加入环境变量PATH中**

将程序所在路径添加到`$PATH`环境变量中是一种常见的方法，可以让您的二进制程序在不带路径的情况下直接执行。通过执行以下命令，您可以将程序所在路径添加到`$PATH`中：

```bash
export PATH=$PATH:/path/to/hello_program
```

在这里，`/path/to/hello_program`应替换为`hello`程序所在的实际路径。一旦将路径添加到`$PATH`中，您就可以在任何位置直接运行`hello`程序，而无需提供完整的路径。

**对比测试**

在将程序所在路径添加到`$PATH`后，您可以进行对比测试。首先，确保环境变量已经更新，可以使用`echo $PATH`来验证。然后，尝试在不同的目录中运行`hello`程序，您会发现它可以直接执行而无需提供路径。

**修改之前提供的实例**

```bash
[Somn@VM-16-4-centos environment]$ export PATH=$PATH:/home/Somn/environment/
[Somn@VM-16-4-centos environment]$ testEnv 
To test env!
[Somn@VM-16-4-centos environment]$ echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/Somn/.local/bin:/home/Somn/bin:/home/Somn/environment/
[Somn@VM-16-4-centos environment]$ cd ..
[Somn@VM-16-4-centos ~]$ testEnv
To test env!
```

可以在**任何位置**直接运行`testEnv`程序，无需提供完整的路径。

**其他方法**

除了将程序路径添加到`$PATH`中之外，还有其他方法可以实现不带路径直接运行程序：

- **使用符号链接**：您可以创建一个符号链接（软链接），将它放在一个在`$PATH`中的目录下，使它指向实际的可执行文件。这样，您可以通过符号链接的名称来运行程序，而不必提供完整路径。

以下是一个创建符号链接的示例：

假设您有一个可执行文件 `my_program`，其完整路径为 `/path/to/my_program`，而且您想要使它可以在任何目录中直接运行。您可以按照以下步骤创建符号链接：

1. 使用 `ln -s` 命令创建符号链接：

   ```bash
   ln -s /path/to/my_program /usr/local/bin/my_program
   ```

   这将创建一个名为 `my_program` 的符号链接文件，它位于 `/usr/local/bin/` 目录中，并指向 `/path/to/my_program`。

2. 确保 `/usr/local/bin/` 在 `$PATH` 中：

   通常情况下，`/usr/local/bin/` 是在 `$PATH` 中的，但您可以通过运行 `echo $PATH` 来验证。如果没有包含 `/usr/local/bin/`，可以通过将它添加到 `$PATH` 来实现：

   ```bash
   export PATH=$PATH:/usr/local/bin
   ```

   **请注意，这只会在当前会话中使其生效。**如果要**永久**更改 `$PATH`，需要将上述命令添加到您的 Shell 配置文件中，如 `~/.bashrc` 或 `~/.bash_profile`。

3. 现在，您可以在任何目录中通过 `my_program` 来运行程序，而不必提供完整路径：

   ```bash
   my_program
   ```

> 这样，您创建了一个**全局可用的符号链接**，使您的程序可以在不同的目录中直接运行，而不需要提供完整路径。这对于将常用工具变成全局可用的命令非常有用，提高了系统的可用性和易用性。

- **将可执行文件移动到`/usr/local/bin`等目录**：如果您有权限，可以将可执行文件移到系统的标准可执行文件目录之一，如`/usr/local/bin`。这样，它就可以直接执行了。

- **使用绝对路径别名**：在某些情况下，您可以通过在Shell配置文件中创建别名来实现直接运行程序。例如，在`~/.bashrc`中添加别名：`alias myapp="/path/to/hello_program"`。这样，您可以运行`myapp`而不需要提供完整路径。

> 总之，Linux中有多种方法可以实现不带路径直接运行程序，其中将程序所在路径添加到`$PATH`是最常见的方法之一，它可以方便地使程序全局可用。其他方法可以根据特定需求来选择。

## 测试HOME

`HOME` 是一个环境变量，用于表示当前用户的主目录路径。在Linux中，不同用户的主目录通常是不同的。下面我们来对比 root 用户和普通用户在使用 `echo $HOME` 和 `cd ~; pwd` 时的差异。

1. **用 root 和普通用户，分别执行 `echo $HOME`，对比差异**：

   - 当 root 用户执行 `echo $HOME` 时，它会显示 root 用户的主目录路径，通常是 `/root`。因为 root 用户是系统管理员，其主目录位于根目录下。
   
     例如：
     ```bash
     [root@VM-16-4-centos ~]# echo $HOME
     /root
     ```

   - 当普通用户执行 `echo $HOME` 时，它会显示该普通用户的主目录路径，通常是 `/home/用户名`。每个普通用户都有自己的主目录。
   
     例如：
     ```bash
     [Somn@VM-16-4-centos ~]$ echo $HOME
     /home/Somn
     ```

2. **执行 `cd ~; pwd`，对应 `~` 和 `HOME` 的关系**：

   - 当用户执行 `cd ~; pwd` 时，`~` 表示当前用户的**主目录**，等效于 `HOME` 环境变量的值。
   
     普通用户执行以下命令：
     
     ```bash
     [Somn@VM-16-4-centos environment]$ cd ~
     [Somn@VM-16-4-centos ~]$ pwd
     /home/Somn
     ```
   
     root用户:
     
     ```bash
     [root@VM-16-4-centos test]# cd ~
     [root@VM-16-4-centos ~]# pwd
     /root
     ```
     
     这里 `cd ~` 让用户进入了其**主目录**，而 `pwd` 命令显示了当前工作目录的路径，即用户的主目录。

> 总结：**`HOME` 环境变量表示用户的主目录路径，不同用户的主目录不同。**`~` 是一个特殊字符，表示当前用户的主目录，它与 `HOME` 的值相同。因此，`cd ~` 可以用来进入当前用户的主目录。根用户和普通用户的主目录通常是不同的，因此 `echo $HOME` 和 `cd ~; pwd` 的结果也会有所不同。

## 相关命令介绍

### **`echo`**

`echo` 命令用于在终端上显示文本或环境变量的值。您可以使用 `echo $VARIABLE_NAME` 来查看特定环境变量的值。例如：`echo $HOME` 会显示主目录的路径。

### **`export`**

`export` 命令用于设置一个新的环境变量，或者将一个已存在的变量升级为环境变量，使其在当前Shell会话以及其后启动的子进程中可见。例如：`export MY_VARIABLE="some_value"`。

假设您希望创建一个名为 `MY_VARIABLE` 的环境变量，其值为 "some_value"。您可以使用以下命令：

```bash
$ export MY_VARIABLE="some_value"
```

现在，`MY_VARIABLE` 已经成为一个环境变量，并且可以在当前Shell会话中访问。您可以使用 `echo` 命令来查看它的值：

```bash
$ echo $MY_VARIABLE
some_value
```

此时，您可以在**当前Shell会话中**使用 `MY_VARIABLE` 这个环境变量，也可以在**当前Shell的任何子进程中**使用它，包括**从当前Shell启动的任何脚本或命令**。

> 总之，`export` 命令允许您创建环境变量，以便它们在当前Shell会话以及从当前Shell启动的子进程中都可见。这对于传递配置信息、设置全局变量和影响子进程的行为非常有用。

> `$ export MY_VARIABLE="some_value"` 中的 "some_value" 不一定是路径，它可以是任何字符串，具体取决于您希望将 `MY_VARIABLE` 设置为的值。通常情况下，环境变量可以存储各种类型的数据，不仅仅是文件路径。

例如，可以将环境变量设置为以下内容：

- 一个文件路径：`export MY_PATH="/path/to/some/file"`

- 一个字符串：`export GREETING="Hello, World!"`

- 一个数字：`export PORT=8080`

- 一个命令或脚本：`export SCRIPT="myscript.sh"`

> 在上述示例中，环境变量的值可以是文件路径、字符串、数字或命令名称，具体取决于您的需求和用途。环境变量的目的是在系统和应用程序中存储和传递配置信息和数据，因此可以用来表示各种不同类型的信息。

### **`env`**

`env` 命令用于显示当前Shell会话中定义的所有环境变量，以及它们的值。这个命令不仅显示用户级环境变量，还显示系统级环境变量。例如：`env` 命令会列出所有环境变量的值。

### **`unset`**

`unset` 命令用于清除或删除一个特定的环境变量。例如：`unset MY_VARIABLE` 会删除名为 `MY_VARIABLE` 的环境变量。

当使用 `unset` 命令来删除一个特定的环境变量时，**它将不再在当前Shell会话中有效，而且在以后的会话中也不会再显示**。以下是一个使用 `unset` 删除环境变量的示例：

假设有一个名为 `MY_VARIABLE` 的环境变量，其存储了一个值：

```bash
$ export MY_VARIABLE="some_value"
```

您可以使用 `echo` 命令来查看它的值：

```bash
$ echo $MY_VARIABLE
some_value
```

然后，如果您想要删除这个环境变量，可以使用 `unset` 命令：

```bash
$ unset MY_VARIABLE
```

现在，再次尝试使用 `echo` 命令来查看 `MY_VARIABLE` 的值：

```bash
$ echo $MY_VARIABLE
```

这时**不会显示任何输出**，因为 `MY_VARIABLE` 已经被成功删除，它不再存在于当前Shell会话中。**如果您在以后的会话中尝试查看它，也会得到相同的结果，因为该变量已经被永久删除。**

> 使用 `unset` 命令是一种有效地删除环境变量的方式，尤其当您不再需要某个变量或需要清除敏感信息时，这个命令非常有用。

### **`set`**

 `set` 命令用于显示当前Shell中定义的所有本地定义的Shell变量和环境变量。这包括了所有的本地变量、环境变量以及一些系统级变量。请注意，`set` 显示的内容可能非常详细，包括了大量信息，包括不仅限于环境变量。这个命令主要用于调试和查看当前Shell的状态。

## 环境变量的组织方式

### 组织特点

> 环境变量通常以键值对的形式组织，其中键表示变量的名称，而值表示变量的内容。这种组织方式使得环境变量易于管理和访问。以下是环境变量的组织方式的一些常见特点：
>

1. **键值对结构**：每个环境变量都有一个唯一的键（变量名）和一个与之关联的值。这种结构允许您使用变量名来引用和访问特定的环境变量。

2. **大小写敏感**：在大多数Unix和Linux系统中，环境变量的名称通常是大小写敏感的，这意味着`MY_VARIABLE`和`my_variable`被视为不同的变量。

3. **通常使用大写字母**：约定俗成的做法是将环境变量的名称表示为大写字母，以便与普通变量区分开来，例如`PATH`、`HOME`等。这不是强制性的，但是有助于提高可读性和可维护性。

4. **特殊字符**：环境变量的名称通常只包含字母、数字和下划线字符（`_`）。变量值可以包含各种字符，包括特殊字符，但可能需要适当地进行转义或引用。

5. **键值对分隔符**：通常，键和值之间用等号（`=`）分隔。例如：`MY_VARIABLE="some_value"`。

6. **多个环境变量**：您可以定义和管理多个环境变量，每个变量都有一个唯一的名称，以存储不同类型的配置信息和数据。

7. **系统级和用户级环境变量**：在Linux系统中，有些环境变量是全局的，系统中的所有用户都可以访问，而有些是用户级的，只能由特定用户访问。系统级环境变量通常位于`/etc/environment`等系统级配置文件中，而用户级环境变量位于`~/.bashrc`、`~/.bash_profile`或类似的用户级配置文件中。

8. **导出和非导出**：只有通过`export`命令设置的环境变量才会被传递给子进程。未使用`export`的变量通常是本地变量，仅在当前Shell会话中可见。

### 环境表

当一个程序在Linux或类Unix系统中启动时，操作系统会自动为该程序创建一个环境表（environment table）。这个环境表是一个字符指针数组，其中每个指针指向一个以null终止字符（'\0'）结尾的环境字符串，这些字符串包含了程序运行时所需的环境变量。

这个环境表通常存储在程序的内存空间中，并且在程序运行期间可供程序访问。环境表中的环境变量以键值对的形式存储，其中键和值都是以字符串的形式表示，并由等号（'='）分隔。

以下是一个示例环境表：

```c
char *envp[] = {
    "PATH=/usr/bin:/bin",  // 环境变量PATH的值
    "HOME=/home/user",     // 环境变量HOME的值
    "LANG=en_US.UTF-8",    // 环境变量LANG的值
    NULL                    // 环境表以NULL指针结束
};
```

在这个示例中，`envp` 是一个**指向环境表的指针**，它包含了三个环境变量。每个环境变量都由一个字符串表示，其中包括了环境变量的名称和值，以等号分隔。**最后，环境表以一个NULL指针结束，以指示表的末尾。**

在程序运行期间，程序可以使用标准的C库函数来访问这些环境变量的值，例如`getenv`函数。例如，要获取`PATH`环境变量的值，程序可以使用以下代码：

```c
char *path = getenv("PATH");
if (path != NULL) {
    printf("PATH: %s\n", path);
} else {
    printf("PATH environment variable not found.\n");
}
```

程序还可以通过修改环境表来改变环境变量的值，但这**通常不是一种推荐的做法**，因为它可能会**导致不可预测的行为**，特别是对于其他正在运行的程序。

> 总之，环境表是一个存储环境变量的数据结构，用于向运行的程序提供配置信息和运行时参数。环境变量以键值对的形式存储在环境表中，并可以通过程序来访问和使用。这是Unix和Linux系统中一个重要的机制，用于自定义和配置程序的行为。