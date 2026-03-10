# Linux 文件操作实践课程


---

## 实验目标

1. 掌握 Linux 文件系统的基本操作（增删改查）
2. 理解并实践文件权限管理机制
3. 掌握常用文件处理工具（压缩、下载、网络诊断等）
4. 提交实验报告到bb平台，包含实验内容，运行截图（包含自己用户名）效果
---

## 实验环境

- Linux 系统（Ubuntu / CentOS / WSL 均可）
- 普通用户权限（非 root）

---

## 第一部分：文件基础操作（20 分钟）

### 1.1 创建与查看

```bash
# 创建实验目录并进入
mkdir -p ~/linux-lab/files
cd ~/linux-lab/files

# 创建不同类型的文件
touch readme.txt                    # 空文件
echo "Hello Linux" > hello.txt      # 写入内容
date > created.log                  # 记录创建时间

# 查看文件内容
cat hello.txt                       # 显示全部内容
head -5 /etc/passwd                 # 查看前5行
tail -5 /etc/passwd                 # 查看后5行
less /etc/passwd                    # 分页浏览（按 q 退出）

# 查看文件属性
ls -l hello.txt                     # 详细信息
file hello.txt                      # 文件类型识别
stat hello.txt                      # 完整元数据
```

### 1.2 复制、移动与删除

```bash
# 复制操作
cp hello.txt hello-copy.txt
cp -r /etc/skel .                   # 复制目录

# 移动/重命名
mv hello-copy.txt backup/
mv readme.txt README.md             # 重命名

# 删除操作（谨慎！）
rm hello.txt                        # 删除文件
rm -i *.log                         # 交互式删除
rm -r backup/                       # 删除目录
```

> ⚠️ **安全提示**：`rm -rf /` 会删除整个系统，永远不要执行！

### 1.3 查找与搜索

```bash
# 查找文件
find ~ -name "*.txt"                # 按名称查找
find /usr/share -type f -size +1M   # 查找大于1MB的文件
find . -mtime -7                    # 最近7天修改的文件

# 内容搜索
grep "root" /etc/passwd             # 文本匹配
grep -r "TODO" .                    # 递归搜索
grep -n "error" /var/log/syslog     # 显示行号

# 组合使用
find . -name "*.py" -exec grep -l "main" {} \;  # 查找包含main的Python文件
```

---

## 第二部分：权限管理（25 分钟）

### 2.1 理解权限模型

```bash
# 查看当前权限
ls -l
# 输出示例：-rw-r--r-- 1 user group 1234 Mar 10 10:00 file.txt
#         [文件类型][所有者][组][其他]
```

权限位含义：
- `r` (4)：读权限
- `w` (2)：写权限  
- `x` (1)：执行权限

### 2.2 修改权限

```bash
# 创建测试脚本
echo '#!/bin/bash
echo "Current user: $(whoami)"
echo "Current directory: $(pwd)"' > show-info.sh

# 数字模式设置权限
chmod 755 show-info.sh              # rwxr-xr-x（所有者可读写执行，其他人可读执行）
chmod 644 readme.txt                # rw-r--r--（标准文件权限）
chmod 700 private.key               # rwx------（仅所有者可访问）

# 符号模式
chmod u+x script.sh                 # 给所有者添加执行权限
chmod go-w file.txt                 # 移除组和其他人的写权限
chmod a+r document.pdf              # 给所有人添加读权限

# 执行脚本
./show-info.sh
```

### 2.3 所有权管理

```bash
# 查看当前用户
whoami
id

# 创建共享目录实验
mkdir shared
touch shared/{a,b,c}.txt

# 修改所有权（需要 sudo 权限）
sudo chown root:root shared/a.txt   # 修改所有者和组
sudo chgrp students shared/b.txt    # 仅修改组

# 设置 SGID 位（共享目录常用）
chmod 2775 shared/                  # 新文件继承目录的组
```

### 2.4 特殊权限（了解）

```bash
# SUID - 以文件所有者权限执行
ls -l /usr/bin/passwd               # 观察 s 权限位

# Sticky Bit - 仅删除自己的文件
ls -ld /tmp                         # 观察 t 权限位
```

---

## 第三部分：文件压缩与归档（15 分钟）

### 3.1 常用压缩格式

```bash
# 创建测试数据
mkdir archive-demo
cd archive-demo
dd if=/dev/urandom of=data1.bin bs=1M count=5
dd if=/dev/urandom of=data2.bin bs=1M count=3

# tar 归档（不压缩）
tar cvf backup.tar *.bin            # 创建归档
tar tvf backup.tar                  # 查看内容
tar xvf backup.tar -C /tmp/         # 解压到指定目录

# gzip 压缩
gzip backup.tar                     # 生成 backup.tar.gz
gunzip backup.tar.gz                # 解压

# 一步完成归档+压缩
tar czvf backup.tar.gz *.bin        # .tar.gz / .tgz
tar cjvf backup.tar.bz2 *.bin       # .tar.bz2（更高压缩率）
tar cJvf backup.tar.xz *.bin        # .tar.xz（最高压缩率）

# 解压
tar xzvf backup.tar.gz              # 解压 .tar.gz
tar xjvf backup.tar.bz2             # 解压 .tar.bz2
```

### 3.2 zip 格式（与 Windows 兼容）

```bash
# 创建 zip
zip -r project.zip archive-demo/

# 查看内容
unzip -l project.zip

# 解压
unzip project.zip -d ~/extracted/

# 加密压缩
zip -e secret.zip sensitive.txt     # 会提示输入密码
```

### 3.3 压缩效率对比

```bash
# 实验：对比不同压缩算法的效率
ls -lh *.tar.*                      # 对比文件大小
time tar czf test.gz largefile      # 记录压缩时间
time tar cJf test.xz largefile      # 对比压缩时间
```

> 💡 **提示**：gzip 速度快但压缩率一般，xz 压缩率高但速度慢，根据场景选择。

---

## 第四部分：网络工具实践（20 分钟）

### 4.1 文件下载

```bash
# curl - 强大的数据传输工具
curl https://www.example.com        # 获取网页内容
curl -o download.html https://www.example.com  # 保存到文件
curl -O https://example.com/file.zip           # 保留远程文件名

# 断点续传
curl -C - -O https://example.com/large-file.iso

# 查看响应头
curl -I https://www.example.com

# POST 请求（API 测试）
curl -X POST -d "name=test&value=123" https://httpbin.org/post

# 下载并解压一步到位
curl -sL https://github.com/user/repo/archive/main.tar.gz | tar xz
```

### 4.2 wget 下载工具

```bash
# 基础下载
wget https://www.example.com/file.zip

# 后台下载
wget -b https://example.com/large-file.iso

# 递归下载网站（谨慎使用！）
wget --mirror --convert-links --wait=2 -P ./local-dir https://example.com

# 限制速度
wget --limit-rate=200k https://example.com/file.zip
```

### 4.3 网络诊断

```bash
# ping - 测试连通性
ping -c 4 www.baidu.com             # 发送4个包
ping -i 0.5 192.168.1.1             # 间隔0.5秒

# 查看网络配置
ip addr                             # 现代方式（替代 ifconfig）
ip route                            # 查看路由表

# 端口测试
curl -v telnet://example.com:80     # 测试端口连通性

# DNS 查询
nslookup www.baidu.com
dig www.baidu.com                   # 更详细的信息

# 网络连接查看
ss -tuln                            # 查看监听端口
netstat -tuln                       # 传统方式（如已安装）
```

---
