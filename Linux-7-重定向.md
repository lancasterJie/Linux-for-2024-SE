安装workbuddy在windows环境中，利用workbuddy布置任务，自动实现下述内容，并提供完整的使用流程截图和效果截图。

### 任务 1：标准输出重定向（覆盖与追加）

1. 使用 `echo` 命令配合 `>` 将文本写入文件（若文件不存在会自动创建，若存在则清空覆盖）：Bash
    
    `echo "Hello, Linux Pipeline" > /tmp/test_redirect.txt
    cat /tmp/test_redirect.txt`
    
2. 使用 `>>` 将新内容追加到文件末尾：

Bash

   `echo "Append this line." >> /tmp/test_redirect.txt
   cat /tmp/test_redirect.txt`

### 任务 2：标准错误重定向与合并

1. 执行一个必定报错的命令（访问不存在的文件），观察错误信息的去向：

Bash

   `ls /not_exist_dir > /tmp/normal.log

2. 将错误信息单独捕获到日志中：

Bash

   `ls /not_exist_dir 2> /tmp/error.log
   cat /tmp/error.log`


### 任务 3：高频进程与内存监控

- **场景需求**：找出当前系统中占用内存最高的前 5 个进程，并将其进程名、PID 以及内存使用率记录下来。
- **命令流水线**：

Bash

  `ps aux --sort=-%mem | head -n 6 | awk '{print $2, $4, $11}'`

---