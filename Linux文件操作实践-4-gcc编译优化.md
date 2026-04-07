# GCC 编译优化作业

## 一、作业主题

GCC 编译优化分析与实践

## 二、作业目的

通过本次作业，学生应掌握：

- 理解 GCC 不同优化等级（-O0、-O1、-O2、-O3）的基本区别。
- 学会从 C 代码生成汇编代码，并观察优化前后的差异。
- 能分析循环中的冗余指令、内存访问、条件跳转等性能影响因素。
- 能初步理解寄存器分配、循环优化、常量传播、死代码消除等优化思想。
- 能结合实验结果，对 “为什么优化会提升性能” 给出解释。

## 三、实验环境

### 建议环境

- 操作系统：Linux / WSL /macOS
- 编译器：GCC
- 常用命令：gcc、time、perf（可选）、gdb（可选）

### 查看 GCC 版本

gcc --version

## 四、实验代码

建立文件 calc.c，内容如下：

#include <stdio.h>#include <time.h>int calc(int n) {int fact = 1;for (int i = 1; i < n; i++) {fact *= i;}return fact;}int main() {int n = 1000000000;clock_t start = clock();int ans = calc(n);clock_t end = clock();printf("ans = %d\n", ans);printf("time = %f\n", (double)(end - start) / CLOCKS_PER_SEC);return 0;}

### 说明

本代码中的 calc 函数并非严格数学意义上的阶乘，计算逻辑为：1 * 2 * 3 * ... * (n - 1)。大输入下会发生整数溢出，本实验核心为观察编译优化行为，不关注数值正确性。

## 五、作业内容

### 任务一：生成不同优化等级下的汇编代码

### 生成汇编命令

gcc -O0 -S calc.c -o calc_O0.sgcc -O1 -S calc.c -o calc_O1.sgcc -O2 -S calc.c -o calc_O2.sgcc -O3 -S calc.c -o calc_O3.s

### 要求

1. 对比 calc_O0.s 和 calc_O2.s 中 calc 函数的汇编代码，观察以下现象：
    - 局部变量是否仍保存在栈上
    - 是否大量使用 mov 指令在内存和寄存器之间搬运数据
    - 循环体中的指令条数是否减少
    - 是否还保留标准栈帧
2. 用自己的话说明：为什么 -O0 下会出现大量访存，而 -O2/-O3 下会减少？

### 提交内容

- 截图或贴出 calc 函数在 -O0 和 -O2 下的关键汇编片段
- 300 字左右分析

---

### 任务二：分析循环热路径

### 要求

重点分析 -O0 下循环对应的汇编代码，回答以下问题：

1. 循环体每轮执行哪些指令？
2. 哪些指令属于真正完成计算的指令？
3. 哪些指令主要是为维护局部变量而进行的寄存器 / 内存搬运？
4. 每轮循环大约有几条与内存相关的指令？
5. 条件判断和条件跳转在循环中起什么作用？

### 提示

重点关注指令类型：mov、imul、add、cmp、jl /jg/jne 等跳转指令

### 提交内容

- 给出循环体对应的汇编片段
- 逐行解释其含义
- 说明哪部分是计算，哪部分是管理开销

---

### 任务三：性能测试

### 编译可执行文件

gcc -O0 calc.c -o calc_O0gcc -O1 calc.c -o calc_O1gcc -O2 calc.c -o calc_O2gcc -O3 calc.c -o calc_O3

### 运行并记录时间

./calc_O0./calc_O1./calc_O2./calc_O3

### 要求

1. 记录四种优化等级下的运行时间：

表格

| **优化等级** | **运行时间** |
| --- | --- |
| -O0 | xxx s |
| -O1 | xxx s |
| -O2 | xxx s |
| -O3 | xxx s |
1. 回答问题：
    - O2 比 -O0 快的主要原因是什么？
    - O3 是否一定比 -O2 快？为什么？

---

### 任务四：理解 GCC 常见优化

查阅资料或结合课堂内容，简要解释下列优化概念（50～100 字 / 个），并结合本题代码说明体现情况（可能 / 不明显 / 基本没有体现）：

1. 常量传播（Constant Propagation）
2. 死代码消除（Dead Code Elimination）
3. 公共子表达式消除（Common Subexpression Elimination）
4. 循环不变代码外提（Loop Invariant Code Motion）
5. 强度削弱（Strength Reduction）
6. 寄存器分配（Register Allocation）

## 六、思考题

1. 为什么 -O0 编译出来的代码通常更适合调试，而 -O2/-O3 编译出来的代码更适合运行？
2. 在这段程序中，循环中频繁访问局部变量 fact 和 i。为什么把它们放在寄存器里通常会更快？
3. 循环中每轮都有一次条件跳转。为什么有条件跳转不一定就意味着性能一定很差？
4. 为什么编译优化不能无限制地进行？优化时通常需要权衡哪些因素？