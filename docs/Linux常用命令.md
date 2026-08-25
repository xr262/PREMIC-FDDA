# Linux常用命令

## 一、查看目录 / 查看文件

### `ls`
查看当前目录下有哪些文件和文件夹：
```bash
ls
```

### `ll`
查看当前目录下文件和文件夹的详细信息：
```bash
ll
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

### `cat`

```bash
cat namelist.input
```
表示将 `namelist.input` 的全部内容输出到终端。
> `cat` 比较适合查看内容较短的文件，如果文件很长，容易导致终端刷屏。


### `tail`

查看文件最后几行：
```bash
tail restrts/rsl.error.0000
```
默认一般显示最后 10 行。
可以查看 WRF 当前大致运行到了哪里。


### `tail -f`

持续实时查看文件末尾的新内容：
```bash
tail -f restrts/rsl.error.0000
```
其中：
- `-f` 表示 `follow`，即持续跟踪。
退出实时查看：
```text
Ctrl + C
```

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

### `grep -i`

忽略大小写搜索：

```bash
grep -i mp_lin namelist.output
```
其中：
- `-i` 表示忽略大小写。
  

### `grep` 配合通配符

在当前目录下所有 Python 文件中搜索 `colorbar`：
```bash
grep colorbar *.py
```
```text
*.py
```
表示：
> 当前目录下所有以 `.py` 结尾的文件。
其中：
- `*` 是通配符，表示任意字符。


## 三、切换目录

### `cd ..`

进入当前目录的上一级目录：
```bash
cd ..
```
- `cd` 表示 `change directory`，即切换目录；
- `..` 表示上一级目录。


## 四、复制 / 删除文件和目录

### `cp -r`
复制整个目录：
```bash
cp -r WRF_P SWAN-0.001
```
其中：

- `cp` 表示 `copy`，即复制；
- `-r` 表示 `recursive`，即递归处理。
可以把递归理解为：
> 遇到文件就处理，遇到文件夹就继续进入下一层处理，直到最底层。


### `rm -r`
删除整个目录及其内部内容：
```bash
rm -r SWAN-0.001/
```

## 五、创建链接

### `ln -s`
创建软链接：
```bash
ln -s /data3/XiaAnRen/vscode vscode
```
软链接，并且把这个链接命名为 vscode。


## 六、运行程序 / 后台任务

### WRF 并行后台运行
```bash
nohup mpirun -np 32 ./wrf.mpich &> /dev/null &
```

### `./wrf.mpich`
```bash
./wrf.mpich
```
表示：
> 运行当前目录下的 `wrf.mpich`。
- `.` 表示当前目录。

### `mpirun -np 32`
```bash
mpirun -np 32 ./wrf.mpich
```
表示：
> 使用 MPI 并行运行 `wrf.mpich`。
```text
-np 32
```
表示：
```text
number of processes = 32
```
即使用 32 个 MPI 进程运行程序。

```text
mpirun -np 32 ./wrf.mpich
```
是使用 32 个 MPI 进程并行运行程序。


### `nohup`
```bash
nohup mpirun -np 32 ./wrf.mpich
```
`nohup` 可以理解为：
```text
no hang up
```
> 即使当前终端、SSH 或 VS Code 连接断开，程序仍然可以继续运行。


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
> [!IMPORTANT]
> `nohup` 和 `&` 不是同一个意思。

```text
&       = 程序去后台运行，我继续使用终端
nohup   = 即使终端断开，程序也继续运行
```

### `&> /dev/null`
```bash
&> /dev/null
```
表示：
> 将程序的标准输出和错误输出全部丢弃。
所有写入 `/dev/null` 的内容都会被直接丢弃。

因此完整命令：
```bash
nohup mpirun -np 32 ./wrf.mpich &> /dev/null &
```
可以理解为：
> 使用 32 个 MPI 进程运行当前目录中的 `wrf.mpich`，将任务放到后台，即使 SSH 或 VS Code 断开也继续运行，同时将终端输出全部丢弃。


### `jobs`
查看当前终端中的后台任务：
```bash
jobs
```
> [!NOTE]
> `jobs` 只能查看当前这个终端或 shell 启动的后台任务。
如果退出服务器后重新登录，再执行jobs,通常无法看到之前启动的任务。
因此判断 WRF 是否还在运行，不能只依赖 `jobs`。


## 七、查看 / 管理进程

### `top`
实时查看服务器上的进程和资源使用情况：
```bash
top
```
> Linux 中的“任务管理器”。
退出：
```text
q
```

### `top -u XuRan`
只查看用户 `XuRan` 的进程：
```bash
top -u XuRan
```
- `-u` 可以理解为 `user`。
只显示自己账户下运行的任务，更适合查看自己的 WRF、Python 等程序。


### `pkill node`
结束名称为 `node` 的进程：
```bash
pkill node
```
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


## 十、常见命令词根与参数

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

