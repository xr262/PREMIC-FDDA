# Linux常用命令

## 一、查看目录 / 查看文件

### `ls`

查看当前目录下有哪些文件和文件夹：

```bash
ls
```

可以简单理解为：

> `ls` = list = 查看当前目录中有什么。



### `ll`

查看当前目录下文件和文件夹的详细信息：

```bash
ll
```

通常 `ll` 相当于：

```bash
ls -l
```

相比 `ls`，它还会显示：

- 文件权限；
- 文件所有者；
- 文件大小；
- 修改时间；
- 文件名。

例如：

```text
-rw-r--r-- 1 XuRan group 3521 Aug 25 14:20 namelist.input
drwxr-xr-x 3 XuRan group 4096 Aug 25 13:00 restrts
```

其中：

- `-` 开头表示普通文件；
- `d` 开头表示目录。

可以简单记为：

> `ls`：看有哪些东西。  
> `ll`：更详细地看有哪些东西。

---

### `cat`

查看整个文件的内容：

```bash
cat viztools.py
```

例如：

```bash
cat namelist.input
```

表示将 `namelist.input` 的全部内容输出到终端。

> `cat` 比较适合查看内容较短的文件，如果文件很长，容易导致终端刷屏。

---

### `tail`

查看文件最后几行：

```bash
tail restrts/rsl.error.0000
```

默认一般显示最后 10 行。

在运行 WRF 时，可以通过：

```bash
tail restrts/rsl.error.0000
```

查看 WRF 当前大致运行到了哪里。

例如日志中出现：

```text
Timing for main: time 2025-06-14_08:00:00
Timing for main: time 2025-06-14_08:05:00
Timing for main: time 2025-06-14_08:10:00
```

说明模式已经积分到了 `08:10:00`。

可以简单记为：

> `tail` = 看文件的“尾巴”。

---

### `tail -f`

持续实时查看文件末尾的新内容：

```bash
tail -f restrts/rsl.error.0000
```

其中：

- `-f` 表示 `follow`，即持续跟踪。

因此：

```bash
tail restrts/rsl.error.0000
```

表示：

> 查看一次文件末尾，然后结束。

而：

```bash
tail -f restrts/rsl.error.0000
```

表示：

> 一直盯着日志文件，只要出现新内容就实时显示。

在运行 WRF、FDDA、SWAN 等程序时，这条命令很常用。

退出实时查看：

```text
Ctrl + C
```

> [!IMPORTANT]
> 此时 `Ctrl + C` 停止的是 `tail -f`，不会停止后台运行的 WRF。

可以简单记为：

> `tail`：看尾巴。  
> `tail -f`：一直跟着尾巴走。

---

## 二、搜索文件内容

### `grep`

在文件中搜索指定关键词：

```bash
grep gqr namelist.input
```

表示：

> 在 `namelist.input` 中查找包含 `gqr` 的行。

可以拆解为：

```text
grep       gqr          namelist.input
搜索       关键词       文件
```

可以把 `grep` 理解成：

> Linux 命令行中的 `Ctrl + F`。

---

### `grep -i`

忽略大小写搜索：

```bash
grep -i mp_lin namelist.output
```

其中：

- `-i` 表示忽略大小写。

因此下面这些内容都可以被匹配：

```text
mp_lin
MP_LIN
Mp_Lin
```

可以简单记为：

> `grep` = 搜索。  
> `-i` = ignore case = 忽略大小写。

---

### `grep` 配合通配符

在当前目录下所有 Python 文件中搜索 `colorbar`：

```bash
grep colorbar *.py
```

其中：

```text
*.py
```

表示：

> 当前目录下所有以 `.py` 结尾的文件。

例如当前目录中有：

```text
viztools.py
plot.py
radar.py
test.txt
```

执行：

```bash
grep colorbar *.py
```

会搜索：

```text
viztools.py
plot.py
radar.py
```

但不会搜索：

```text
test.txt
```

其中：

- `*` 是通配符，表示任意字符。

例如还可以使用：

```bash
grep contourf *.py
```

查找哪些 Python 文件中使用了 `contourf`。

或者：

```bash
grep wrfout *.py
```

查找哪些 Python 文件中包含 `wrfout`。

---

## 三、切换目录

### `cd ..`

进入当前目录的上一级目录：

```bash
cd ..
```

其中：

- `cd` 表示 `change directory`，即切换目录；
- `..` 表示上一级目录。

例如当前路径为：

```text
/data3/XuRan/WRF/run
```

执行：

```bash
cd ..
```

会进入：

```text
/data3/XuRan/WRF
```

再执行一次：

```bash
cd ..
```

会进入：

```text
/data3/XuRan
```

常见目录符号：

```text
.     当前目录
..    上一级目录
~     当前用户的 home 目录
/     Linux 根目录
```

例如：

```bash
cd ~
```

表示回到当前用户的 home 目录。

---

## 四、复制 / 删除文件和目录

### `cp -r`

复制整个目录：

```bash
cp -r WRF_P SWAN-0.001
```

或者：

```bash
cp -r SWAN SWAN-0.001
```

其中：

- `cp` 表示 `copy`，即复制；
- `-r` 表示 `recursive`，即递归处理。

例如：

```bash
cp -r SWAN SWAN-0.001
```

表示：

> 将整个 `SWAN` 目录复制一份，新目录名称为 `SWAN-0.001`。

假设原目录结构为：

```text
SWAN/
├── namelist.input
├── wrf.mpich
└── restrts/
    ├── rsl.error.0000
    └── old/
        └── test.txt
```

加上 `-r` 后，Linux 会一层一层进入子目录，把其中所有文件和子目录全部复制。

可以把递归理解为：

> 遇到文件就处理，遇到文件夹就继续进入下一层处理，直到最底层。

可以简单记为：

> `cp` = copy = 复制。  
> `-r` = recursive = 递归。

---

### `rm -r`

删除整个目录及其内部内容：

```bash
rm -r SWAN-0.001/
```

其中：

- `rm` 表示 `remove`，即删除；
- `-r` 表示递归处理。

因此：

```bash
rm -r SWAN-0.001/
```

表示：

> 一层一层删除 `SWAN-0.001` 目录中的文件和子目录，最后删除整个目录。

> [!WARNING]
> `rm -r` 使用时一定要谨慎。Linux 服务器上的文件通常不会进入类似 Windows 的“回收站”。

删除前建议先执行：

```bash
pwd
ls
```

确认：

- 当前路径是否正确；
- 要删除的目录名称是否正确。

---

### 删除 VS Code Server

```bash
rm -r ~/.vscode-server/
```

其中：

- `~` 表示当前用户的 home 目录；
- `.vscode-server` 是 VS Code Remote Server 在服务器上的相关目录。

例如：

```text
/home/XuRan/.vscode-server/
```

这条命令常用于处理：

- VS Code Remote SSH 无法连接；
- VS Code Server 安装异常；
- Remote Server 卡死；
- VS Code 远程扩展异常。

---

## 五、创建链接

### `ln -s`

创建软链接：

```bash
ln -s /data3/XiaAnRen/vscode/
```

其中：

- `ln` 表示 `link`；
- `-s` 表示 `symbolic`；
- `ln -s` 表示创建符号链接，也叫软链接。

软链接可以简单理解为：

> Linux 中的“快捷方式”。

更完整的形式通常为：

```bash
ln -s 原路径 链接名
```

例如：

```bash
ln -s /data3/XiaAnRen/vscode vscode
```

表示创建一个名为：

```text
vscode
```

的软链接，实际指向：

```text
/data3/XiaAnRen/vscode
```

之后执行：

```bash
cd vscode
```

实际进入的是：

```text
/data3/XiaAnRen/vscode
```

可以简单记为：

> `ln` = link。  
> `-s` = symbolic。  
> `ln -s` = 创建软链接。

---

## 六、运行程序 / 后台任务

### WRF 并行后台运行

```bash
nohup mpirun -np 32 ./wrf.mpich &> /dev/null &
```

这条命令可以拆成以下几个部分。

---

### `./wrf.mpich`

```bash
./wrf.mpich
```

表示：

> 运行当前目录下的 `wrf.mpich`。

其中：

- `.` 表示当前目录。

因此：

```text
./wrf.mpich
```

可以理解为：

> 当前目录中的 `wrf.mpich`。

---

### `mpirun -np 32`

```bash
mpirun -np 32 ./wrf.mpich
```

表示：

> 使用 MPI 并行运行 `wrf.mpich`。

其中：

```text
-np 32
```

表示：

```text
number of processes = 32
```

即使用 32 个 MPI 进程运行程序。

可以简单理解为：

```text
./wrf.mpich
```

是直接运行程序。

而：

```text
mpirun -np 32 ./wrf.mpich
```

是使用 32 个 MPI 进程并行运行程序。

---

### `nohup`

```bash
nohup mpirun -np 32 ./wrf.mpich
```

`nohup` 可以理解为：

```text
no hang up
```

作用是：

> 即使当前终端、SSH 或 VS Code 连接断开，程序仍然可以继续运行。

对于需要长时间运行的 WRF 模式，这个命令非常常用。

---

### 最后的 `&`

```bash
nohup mpirun -np 32 ./wrf.mpich &
```

最后一个：

```text
&
```

表示：

> 将程序放到后台运行。

程序进入后台之后，当前终端可以继续输入其他命令。

> [!IMPORTANT]
> `nohup` 和 `&` 不是同一个意思。

可以简单记为：

```text
&       = 程序去后台运行，我继续使用终端
nohup   = 即使终端断开，程序也继续运行
```

---

### `&> /dev/null`

```bash
&> /dev/null
```

表示：

> 将程序的标准输出和错误输出全部丢弃。

其中：

```text
/dev/null
```

可以理解为 Linux 中的“黑洞”。

所有写入 `/dev/null` 的内容都会被直接丢弃。

因此完整命令：

```bash
nohup mpirun -np 32 ./wrf.mpich &> /dev/null &
```

可以理解为：

> 使用 32 个 MPI 进程运行当前目录中的 `wrf.mpich`，将任务放到后台，即使 SSH 或 VS Code 断开也继续运行，同时将终端输出全部丢弃。

WRF 自身仍然可以通过：

```text
rsl.error.0000
rsl.out.0000
```

等日志文件查看运行情况。

---

### `jobs`

查看当前终端中的后台任务：

```bash
jobs
```

例如某个程序通过：

```bash
some_program &
```

放到后台运行后，可以执行：

```bash
jobs
```

查看当前 shell 中的后台任务。

> [!NOTE]
> `jobs` 只能查看当前这个终端或 shell 启动的后台任务。

如果退出服务器后重新登录，再执行：

```bash
jobs
```

通常无法看到之前启动的任务。

因此判断 WRF 是否还在运行，不能只依赖 `jobs`。

---

## 七、查看 / 管理进程

### `top`

实时查看服务器上的进程和资源使用情况：

```bash
top
```

可以把 `top` 理解为：

> Linux 中的“任务管理器”。

通常可以查看：

- CPU 使用情况；
- 内存使用情况；
- 系统负载；
- PID；
- USER；
- `%CPU`；
- `%MEM`；
- COMMAND。

运行 WRF 时，可以通过 `top` 判断：

- WRF 进程是否还存在；
- CPU 是否还在工作；
- 服务器当前负载是否过高。

退出：

```text
q
```

---

### `top -u XuRan`

只查看用户 `XuRan` 的进程：

```bash
top -u XuRan
```

其中：

- `-u` 可以理解为 `user`。

相比：

```bash
top
```

显示整台服务器上的进程，

```bash
top -u XuRan
```

只显示自己账户下运行的任务，更适合查看自己的 WRF、Python 等程序。

可以简单记为：

> `top`：看整个服务器。  
> `top -u XuRan`：只看自己的进程。

---

### `pkill node`

结束名称为 `node` 的进程：

```bash
pkill node
```

其中：

- `pkill` 用于按照进程名称结束进程；
- `node` 是 Node.js 进程。

VS Code Remote Server 会启动一些 Node.js 进程，因此当 VS Code Remote 出现异常时，有时会使用：

```bash
pkill node
```

先结束相关进程，然后执行：

```bash
rm -r ~/.vscode-server/
```

删除旧的 VS Code Server。

之后重新连接 VS Code，系统会重新建立相关远程环境。

> [!WARNING]
> 如果当前账户下还有其他 Node.js 程序正在运行，`pkill node` 也可能将它们一起结束。

---

## 八、常用命令分类总结

### 1. 查看目录 / 文件

```bash
ls
ll
cat
tail
tail -f
```

### 2. 搜索文件内容

```bash
grep
```

### 3. 切换目录

```bash
cd
```

### 4. 复制 / 删除

```bash
cp -r
rm -r
```

### 5. 创建链接

```bash
ln -s
```

### 6. 运行程序 / 后台任务

```bash
nohup
mpirun
&
jobs
```

### 7. 查看 / 管理进程

```bash
top
top -u
pkill
```

---

## 九、Linux 常见符号

| 符号 | 含义 |
|---|---|
| `.` | 当前目录 |
| `..` | 上一级目录 |
| `~` | 当前用户的 home 目录 |
| `/` | Linux 根目录 |
| `*` | 通配符，表示任意字符 |
| `&` | 将程序放到后台运行 |
| `>` | 输出重定向 |
| `/dev/null` | 丢弃输出 |

---

## 十、WRF / FDDA 常见操作流程

假设需要建立一个新的实验目录 `SWAN-0.001`。

查看当前目录：

```bash
ls
```

复制实验目录：

```bash
cp -r SWAN SWAN-0.001
```

进入实验目录：

```bash
cd SWAN-0.001
```

查看目录详细信息：

```bash
ll
```

编辑参数：

```bash
vi namelist.input
```

检查参数：

```bash
grep gqr namelist.input
```

或者：

```bash
grep -i mp_lin namelist.output
```

启动 WRF：

```bash
nohup mpirun -np 32 ./wrf.mpich &> /dev/null &
```

查看自己的计算进程：

```bash
top -u XuRan
```

临时查看 WRF 日志：

```bash
tail restrts/rsl.error.0000
```

持续查看 WRF 日志：

```bash
tail -f restrts/rsl.error.0000
```

整个过程可以概括为：

```text
建立实验
    ↓
cp -r

进入目录
    ↓
cd

修改参数
    ↓
vi

检查参数
    ↓
grep

运行程序
    ↓
nohup + mpirun + &

查看进程
    ↓
top

查看运行日志
    ↓
tail / tail -f
```

---

## 十一、重点记忆

Linux 命令一般可以按照：

```text
命令 + 参数 + 操作对象
```

来理解。

例如：

```bash
cp -r SWAN SWAN-0.001
```

可以拆解为：

```text
cp        -r          SWAN       SWAN-0.001
复制      递归        原目录      目标目录
```

常见命令词根：

```text
ls      → list        → 查看
cd      → directory   → 切换目录
cp      → copy        → 复制
rm      → remove      → 删除
cat     → 查看整个文件
tail    → 查看文件尾部
grep    → 搜索
ln      → link        → 链接
top     → 查看进程和资源
kill    → 结束进程
mpirun  → MPI 并行运行
nohup   → 终端断开后继续运行
```

常见参数：

```text
-r    recursive              递归处理
-i    ignore case            忽略大小写
-u    user                   指定用户
-f    follow                 持续跟踪
-s    symbolic               符号链接
-np   number of processes    进程数量
```


> 命令是“做什么”，参数是“怎么做”，后面的内容是“对谁做”。

