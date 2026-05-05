## 一、作业目标

通过本次作业，需要掌握 Linux 环境下静态库和动态库的基本构建、链接和运行方法。完成作业后，学生应能够：

1. 理解静态库和动态库的区别
2. 使用 gcc 编译多个 C 源文件
3. 使用 ar 工具生成静态库
4. 使用 gcc 生成动态库
5. 理解链接路径和运行时库搜索路径

## 二、作业背景

在 Linux 系统中，程序通常会将通用功能封装为库文件。库文件可以被多个程序复用，有助于提高代码复用性、降低维护成本，并形成更加清晰的工程结构。

Linux 中常见的库文件主要包括两类：

### 1. 静态库

静态库在程序链接阶段被合并到最终可执行文件中。使用静态库生成的可执行文件体积通常较大，但运行时不依赖外部库文件。

静态库文件后缀通常为：

```
.a
```

### 2. 动态库

动态库在程序运行时被加载。使用动态库生成的可执行文件体积通常较小，但运行时系统必须能够找到对应的动态库文件。

动态库文件后缀通常为：

```
.so
```

本次作业要求 实现一个简单的数学工具库，并分别将其构建为静态库和动态库，然后编写测试程序调用库中的函数。

## 三、项目结构要求

请按照如下目录结构组织代码：

```
libmathlab
├── include
│   └── mathlab.h
├── src
│   ├── add.c
│   ├── sub.c
│   ├── mul.c
│   └── div.c
├── test
│   └── main.c
└── README.md
```

## 四、功能要求

在 mathlab 库中实现以下函数：

```c
int ml_add(int a, int b);
int ml_sub(int a, int b);
int ml_mul(int a, int b);
int ml_div(int a, int b);
```

函数功能如下：

| 函数名 | 功能 |
| --- | --- |
| ml_add | 返回两个整数之和 |
| ml_sub | 返回两个整数之差 |
| ml_mul | 返回两个整数之积 |
| ml_div | 返回两个整数相除的结果 |

ml_div 函数需要处理除数为 0 的情况。当除数为 0 时，函数返回 0，并在终端输出错误提示信息。

## 五、代码实现要求

### 1. 头文件 mathlab.h

在 include 目录下创建 mathlab.h，用于声明库对外提供的函数。

参考内容如下：

```c
#ifndef MATHLAB_H
#define MATHLAB_H

int ml_add(int a, int b);
int ml_sub(int a, int b);
int ml_mul(int a, int b);
int ml_div(int a, int b);

#endif
```

### 2. 源文件实现

在 src 目录下分别创建四个源文件：

```
add.c
sub.c
mul.c
div.c
```

每个源文件分别实现一个函数：

| 源文件 | 实现函数 |
| --- | --- |
| add.c | ml_add |
| sub.c | ml_sub |
| mul.c | ml_mul |
| div.c | ml_div |

其中，div.c 需要包含 stdio.h，用于输出错误提示信息。

### 3. 测试程序 main.c

在 test 目录下编写 main.c，调用 mathlab 库中的四个函数，并输出计算结果。

测试程序至少需要包含以下测试内容：

```
10 + 5
10 - 5
10 * 5
10 / 5
10 / 0
```

程序运行后，应能清晰显示每个测试的执行结果。

## 六、编译任务要求

### 1. 生成目标文件

将 src 目录下的每个 C 源文件编译为目标文件。

目标文件建议放入 build 目录。

示例目标文件如下：

```
build/add.o
build/sub.o
build/mul.o
build/div.o
```

### 2. 生成静态库

使用 ar 工具将目标文件打包为静态库。

静态库文件名要求为：

```
libmathlab.a
```

静态库建议放入 lib 目录。

### 3. 使用静态库生成可执行程序

使用 libmathlab.a 链接生成测试程序。

生成的可执行程序命名为：

```
main_static
```

运行该程序，检查输出结果是否正确。

### 4. 生成动态库

使用 gcc 生成动态库。

动态库文件名要求为：

```
libmathlab.so
```

动态库建议放入 lib 目录。

生成动态库时，目标文件需要使用位置无关代码选项：

```
-fPIC
```

### 5. 使用动态库生成可执行程序

使用 [libmathlab.so](http://libmathlab.so/) 链接生成测试程序。

生成的可执行程序命名为：

```
main_dynamic
```

运行该程序，检查输出结果是否正确。

## 七、分析问题

完成实验后，请在 [README.md](http://readme.md/) 中回答以下问题：

1. 静态库和动态库的文件后缀分别是什么？
2. 静态库使用什么工具生成？
3. 动态库生成时为什么需要使用 -fPIC？
4. main_static 和 main_dynamic 的文件大小是否相同？为什么？
5. 如果直接运行 main_dynamic 出现找不到动态库的问题，原因是什么？
6. 使用 ldd 查看 main_dynamic 的依赖库，能看到什么信息？
7. 使用 nm 查看 libmathlab.a 和 [libmathlab.so](http://libmathlab.so/) 时，能看到哪些函数符号？
8. 修改 mathlab 库中的函数实现后，静态链接程序和动态链接程序分别需要如何重新编译？
9. 静态库和动态库分别适合哪些应用场景？