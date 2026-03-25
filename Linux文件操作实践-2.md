# Linux 软链接与硬链接上机实验

## 一、实验名称

Linux 文件链接实验：硬链接与软链接

---

## 二、实验目的

1. 理解 Linux 文件系统中 inode 的基本概念。
2. 掌握普通文件、硬链接和软链接的创建方法。
3. 观察硬链接与软链接在 inode、文件内容、删除源文件后的表现差异。
4. 理解硬链接与软链接的适用场景及区别。

---

## 三、实验环境

- 操作系统：Linux（Ubuntu/CentOS 均可）
- 工具：终端 Shell
- 实验目录：建议在用户主目录下新建实验文件夹进行操作

---

## 四、实验原理

在 Linux 文件系统中，文件名和文件内容并不是直接绑定的。  
系统通过 **inode** 来保存文件的元数据，而文件名本质上是目录项中指向 inode 的一个“入口”。

### 1. inode 是什么

inode 中通常保存以下信息：

- 文件类型
- 文件权限
- 文件大小
- 所有者
- 时间戳
- 数据块位置
- 链接计数（link count）

### 2. 硬链接（Hard Link）

硬链接本质上是给同一个 inode 增加一个新的文件名。  
因此，多个硬链接文件其实对应的是同一个文件实体。

特点：

- 硬链接和原文件的 inode 相同
- 修改其中任意一个，内容都会同步变化
- 删除原文件后，只要还有硬链接存在，文件内容仍然存在
- 一般不能对目录创建硬链接
- 不能跨文件系统创建硬链接

### 3. 软链接（Symbolic Link）

软链接也叫符号链接，类似于 Windows 中的“快捷方式”。  
它本身是一个独立文件，保存的是目标文件的路径信息。

特点：

- 软链接和原文件的 inode 不同
- 软链接指向的是原文件路径
- 删除原文件后，软链接会失效
- 可以跨文件系统创建
- 可以对目录创建软链接

---

## 五、实验内容

本实验分为四个部分：

1. 创建普通文件并观察 inode
2. 创建硬链接并观察变化
3. 创建软链接并观察变化
4. 删除原文件后比较硬链接和软链接表现

---

## 六、实验步骤

## 实验任务一：创建普通文件并观察 inode

### 1. 新建实验目录


mkdir -p ~/link_lab
cd ~/link_lab

### 2. 创建测试文件

echo "This is the original file." > file1.txt

### 3. 查看文件详细信息

ls -l file1.txt
ls -i file1.txt
stat file1.txt
cat file1.txt

### 4. 观察内容

重点观察以下信息：

- 文件权限
- 文件大小
- 修改时间
- inode 编号
- 链接计数（Links）

### 5. 记录结果

请记录 file1.txt 的：

- 文件名
- inode 编号
- 链接计数
- 文件内容
## 实验任务二：创建硬链接并观察变化

### 1. 创建硬链接

`ln file1.txt file1_hard.txt`

### 2. 查看原文件和硬链接文件信息

`ls -l file1.txt file1_hard.txt`  
`ls -i file1.txt file1_hard.txt`  
`stat file1.txt`  
`stat file1_hard.txt`

### 3. 修改硬链接文件内容

`echo "Add one line." >> file1_hard.txt`

### 4. 分别查看两个文件内容

`cat file1.txt`  
`cat file1_hard.txt`

### 5. 观察内容

重点观察：

- 两个文件的 inode 是否相同
- 链接计数是否变化
- 修改 `file1_hard.txt` 后，`file1.txt` 内容是否同步变化

### 6. 记录结果

请记录：

- `file1.txt` 和 `file1_hard.txt` 的 inode 编号
- 两者链接计数
- 修改后两个文件的内容

## 实验任务三：创建软链接并观察变化

### 1. 创建软链接

`ln -s file1.txt file1_soft.txt`

### 2. 查看文件信息

`ls -l file1.txt file1_soft.txt`  
`ls -i file1.txt file1_soft.txt`  
`stat file1.txt`  
`stat file1_soft.txt`

### 3. 查看软链接内容

`cat file1_soft.txt`

### 4. 修改原文件内容

`echo "Soft link test." >> file1.txt`

### 5. 再次查看原文件和软链接内容

`cat file1.txt`  
`cat file1_soft.txt`

### 6. 观察内容

重点观察：

- 软链接和原文件的 inode 是否相同
- `ls -l` 中软链接是否显示指向关系
- 修改原文件后，软链接访问到的内容是否同步变化

### 7. 记录结果

请记录：

- `file1.txt` 和 `file1_soft.txt` 的 inode 编号
- `file1_soft.txt` 指向的目标
- 修改后两个文件的显示内容

## 实验任务四：删除原文件后比较硬链接和软链接

### 1. 删除原文件

`rm file1.txt`

### 2. 查看当前目录文件情况

`ls -l`  
`ls -i`

### 3. 分别访问硬链接和软链接

`cat file1_hard.txt`  
`cat file1_soft.txt`

### 4. 观察内容

重点观察：

- 删除原文件后，硬链接是否还能访问内容
- 删除原文件后，软链接是否失效
- 为什么会出现这样的现象

### 5. 记录结果

请记录：

- `file1_hard.txt` 是否可正常读取
- `file1_soft.txt` 是否可正常读取
- 系统提示信息是什么

## 七、实验结果分析

请根据实验现象，完成以下分析。

### 1. 硬链接的特点

请总结：

- 为什么硬链接与原文件 inode 相同
- 为什么删除原文件后硬链接仍可访问
- 为什么修改一个文件另一个也会变化

### 2. 软链接的特点

请总结：

- 为什么软链接 inode 与原文件不同
- 为什么软链接像“快捷方式”
- 为什么删除原文件后软链接失效