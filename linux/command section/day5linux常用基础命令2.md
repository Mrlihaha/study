### 1.top命令

`top`可以实时动态地查看系统的整体运行情况，是一个综合了多方信息监测系统性能和运行信息的实用工具。通过top命令所提供的互动式界面，用热键可以管理。用法直接输入 top回车

第1行：`top - 19:31:16 up 21:10,  2 users,  load average: 0.00, 0.00, 0.00`

| 内容                           | 含义                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| 19:31:16                       | 表示当前时间                                                 |
| up  21:10                      | 系统运行时间 格式为时：分                                    |
| 2 users                        | 当前登录用户数                                               |
| load average: 0.00, 0.00, 0.00 | 系统负载，即任务队列的平均长度。 三个数值分别为 1分钟、5分钟、15分钟前到现在的平均值。 |

第2行：`Tasks: 109 total,   1 running, 108 sleeping,   0 stopped,   0 zombie`

第3行：`%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.3 hi,  0.0 si,  0.0 st`

第2、3行为进程和CPU的信息  当有多个CPU时，这些内容可能会超过两行，其参数如下：

| 内容         | 含义                                          |
| ------------ | --------------------------------------------- |
| 109 total    | 进程总数                                      |
| 1 running    | 正在运行的进程数                              |
| 108 sleeping | 睡眠的进程数                                  |
| 0 stopped    | 停止的进程数                                  |
| 0 zombie     | 僵尸进程数                                    |
| 0.0 us       | 用户空间占用CPU百分比                         |
| 0.0 sy       | 内核空间占用CPU百分比                         |
| 0.0 ni       | 用户进程空间内改变过优先级的进程占用CPU百分比 |
| 99.7 id      | 空闲CPU百分比                                 |
| 0.0 wa       | 等待输入输出的CPU时间百分比                   |
| 0.3 hi       | 硬中断（Hardware IRQ）占用CPU的百分比         |
| 0.0 si       | 软中断（Software Interrupts）占用CPU的百分比  |

​     

第4行：`MiB Mem :   1775.3 total,   1369.3 free,    162.4 used,    243.6 buff/cache` 第5行：`MiB Swap:   1640.0 total,   1640.0 free,      0.0 used.   1466.1 avail Mem`

为内存信息

| 内容                  | 含义                                   |
| --------------------- | -------------------------------------- |
| KiB Mem:1775.3 tota   | 物理内存总量                           |
| 1369.3 free           | 空闲内存总量                           |
| 162.4 used            | 使用的物理内存总量                     |
| 243.6 buff/cache      | 用作内核缓存的内存量                   |
| MiB Swap:1640.0 total | 交换区总量                             |
| 1640.0 free           | 空闲交换区总量                         |
| 0.0 used              | 使用的交换区总量                       |
| 1466.1 avail Mem      | 代表可用于进程下一次分配的物理内存数量 |

第6行：    `PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND`            

为进程信息

| 列名    | 含义                                                         |
| ------- | ------------------------------------------------------------ |
| PID     | 进程id                                                       |
| USER    | 进程所有者的用户名                                           |
| PR      | 优先级                                                       |
| NI      | nice值。负值表示高优先级，正值表示低优先级                   |
| VIRT    | 进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES                |
| RES     | 进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA    |
| SHR     | 共享内存大小，单位kb                                         |
| S       | 进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程 |
| %CPU    | 上次更新到现在的CPU时间占用百分比                            |
| %MEM    | 进程使用的物理内存百分比                                     |
| TIME+   | 进程使用的CPU时间总计，单位1/100秒                           |
| COMMAND | 命令名/命令行                                                |

### 2.w命令

输入w可以查看当前系统登录的用户

### 3.last命令

显示用户最近登录的信息单独执行last命令，它会读取/var/log/wtmp的文件，并把该给文件的内容记录的登入系统的用户名单全部显示出来。`/var/log/wtmp`文件  last -5 只显示5行

`wtmp`文件是二进制文件,该日志文件永久记录每个用户登录、注销及系统的启动、停机的事件。因此随着系统正常运行时间的增加，该文件的大小也会越来越大，增加的速度取决于系统用户登录的次数。该日志文件可以用来查看用户的登录记录，last命令就通过访问这个文件获得这些信息，并以反序从后向前。

### 4.ps命令

`ps`命令是最基本同时也是非常强大的进程查看命令，使用该命令可以确定有哪些进程正在运行和运行的状态、进程是否结束。

用法：`ps [选项]`

参数：

-A 显示所有进程（同-e）

-a 显示当前终端的所有进程

-u 显示进程的用户信息

-o 以用户自定义形式显示进程信息

-f 显示程序间的关系

-x 显示所有程序，不以终端机来区分

-aux 显示所有包含其他使用者的行程

案例：

```bash
ps -aux|more
ps -aux|grep root|more
```

| USER       | PID    | %CPU       | %MEM       | VSZ                               | RSS                                               | TTY        | STAT     | START        | TIME       | COMMAND      |
| ---------- | ------ | ---------- | ---------- | --------------------------------- | ------------------------------------------------- | ---------- | -------- | ------------ | ---------- | ------------ |
| 行程拥有者 | 进程ID | CPU 使用率 | 内存使用率 | 进程使用的虚拟内存大小，以K为单位 | 驻留空间的大小。显示当前常驻内存的程序的K字节数。 | 终端的号码 | 进程状态 | 进程开始时间 | 执行的时间 | 所执行的指令 |

STAT进程状态包括下面的状态： 

D 不可中断 Uninterruptible sleep (usually IO) 

R 正在运行，或在队列中的进程 

S 处于休眠状态 T 停止或被追踪 

Z 僵尸进程 

W 进入内存交换（从内核2.6开始无效） 

X 死掉的进程 

< 高优先级 

N 低优先级 L 有些页被锁进内存 

s 包含子进程 

\+ 位于后台的进程组

l 多线程，克隆线程

### 5.netstat命令

`netstat`命令用来打印Linux中网络系统的状态信息，可让你得知整个Linux系统的网络情况。

用法：`netstat [选项]`

参数：

- -a或--all：显示所有连线中的Socket；
- -l或--listening：显示监控中的服务器的Socket；
- -n或--numeric：直接使用ip地址，而不通过域名服务器；
- -t或--tcp：显示TCP传输协议的连线状况；
- -p或--programs：显示正在使用Socket的程序识别码和程序名称；

案例：

```
netstat -an
netstat -anltp
```

| Proto | Recv-Q       | Send-Q       | Local Address | Foreign Address | State    | PID/Program name |
| ----- | ------------ | ------------ | ------------- | --------------- | -------- | ---------------- |
| 协议  | 网络接收队列 | 网路发送队列 | 本地地址      | 外部地址        | 端口状态 | 进程ID/程序名    |

### 6.lsof命令

#### 1.命令使用1

lsof`（list open files）是一个常用的系统管理工具，主要用于列出当前系统中打开的文件和网络连接等相关信息。下面详细介绍一下 `lsof` 命令的用法和参数含义：

- 基本语法

```
lsof [options] [file|directories]
```

- 常用选项

以下是 `lsof` 常用的几个选项：

```
-a：与 OR 连接多个选项条件
-c <进程名称>：指定进程名称
-d <文件描述符>：指定文件描述符
-i：显示网络连接信息
-n：禁止解析主机名和端口号
-p <进程号>：指定进程号
-u <用户名称>：指定用户名称
```

- 参数说明

`lsof` 命令的参数可以是文件或者目录名，也可以是在命令行上指定的进程、用户、网络端口等信息。

- 示例

以下是 `lsof` 命令的几个常见示例：

```
1. 列出所有打开的文件：`lsof`
2. 列出某个进程打开的所有文件：`lsof -c nginx`
3. 列出某个用户打开的所有文件：`lsof -u user`
4. 列出某个目录中被打开的文件：`lsof /path/to/dir`
5. 列出某个网络端口的相关信息：`lsof -i :80`
```

#### 2.利用lsof恢复已删除的文件

原理是进内存里拿文件,被恢复的文件需要被打开过,内存使用过高时会把文件覆盖掉则无法恢复

`lsof |grep /var/log/messages`这里pid对应的是699，所以我们需要查看699进程打开的文件描述

```
cd /proc/699/fd
```

再使用`ll`查看就可以看到被删除对应的文件了，文件7对应的就是被删除的文件，将7里面的内容输入到被删除的目录文件就恢复了

`cp 7 /var/log`拷贝这个文件。

### 7.kill命令

## 

`kill`命令用于结束进程。

用法：`kill [选项] pid` 案例：

```bash
kill -9 123456          #彻底杀死进程号为123456的进程
```

### 8. which命令

which用于查找并显示给定命令的绝对路径，可以看到某个系统命令是否存在 用法：which [命令] 案例：

```
which cd
```

### 9.rpm -Va 检查文件完整性

直接在centos终端输入rpm -Va 可以检查被修改过或缺失的系统文件

