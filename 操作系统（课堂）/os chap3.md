# 进程概念

## 进程，作业，程序

通俗来讲，进程是执行中的程序，但进程不只是程序代码，进程通常还包含

* 堆（heap）包含运行期间动态申请的内存
* 进程栈 包含临时数据，如函数参数，返回地址和局部变量
* 数据段（data section）全局变量

<img src=".\picture\QQ截图20211017120737.png" style="zoom:50%;" />

### 程序和进程

程序是被动实体，如可执行文件，进程是活动实体，它有一个程序计数器来表示下一步操作。当一个可执行文件被装入内存时，一个程序才能成为进程

### 进程和作业

作业是用户需要计算机完成的某项任务，是要求计算机所做工作的集合。一个作业的完成要经过作业提交、作业收容、作业执行和作业完成4个阶段。而进程是对已提交完毕的程序所执行过程的描述，是资源分配的基本单位。

一个作业可由多个进程组成，且必须至少由一个进程组成，反过来则不成立。

作业的概念主要用在批处理系统中，像UNIX这样的分时系统中就没有作业的概念。而进程的概念则用在几乎所有的多道程序系统中。

作业与进程最主要的区别是：前者是由用户提交，后者是由系统自动生成；前者以用户任务为单位，后者是操作系统控制的单位。

## 进程状态

进程在执行时会改变状态，可能处于下列状态之一

* new 新进程正在被创建
* running 指令正在被执行
* waiting 等待某个事件发生，如I/O完成
* ready 等待分配处理器
* terminated 进程完成执行

一次只有一个进程可以在一个处理器上run,多个进程可以处于ready和waiting

![](.\picture\QQ截图20211017122057.png)

## 进程控制块PCB（process control block）

每个进程在操作系统中用pcb来表示，包含与一个特定进程相关的信息

<img src=".\picture\QQ截图20211017122431.png" style="zoom: 50%;" />

* process state
* program counter
* CPU registers
* CPU scheduling information(cpu调度信息)：包括进程优先级，调度队列的指针和其他调度参数
* 内存管理信息
* accounting information：cpu使用时间，实际使用时间，时间界限等
* I/O状态信息：包括分配给进程的I/O设备列表，打开的文件列表
* process number：标识符

CPU在进程间的切换

idle:空闲

![](.\picture\QQ截图20211017123152.png)

### Linux下的PCB：由结构体task_struct表示

![](.\picture\QQ截图20211017170113.png)

## 线程（threads）

说是第四章会讲

# 进程调度

## scheduling queues（调度队列）

进程进入系统时，会被加入到job queue中，该队列包括系统中所有进程

驻留在内存的处于ready,waiting的进程保存在**ready queue**中，该队列通常用链表实现，每个pcb包含一个指向就绪队列的下一个pcb的指针域

![ready queue](.\picture\QQ截图20211017132013.png)

## device queues(设备队列)

等待特定I/O设备的进程列表，每个设备都有自己的设备队列

![](.\picture\QQ截图20211017132935.png)

### 队列图

![](.\picture\QQ截图20211017133126.png)

## Schedulers（调度程序）

### long-term scheduler(or job scheduler)

选择应该进入ready队列的进程

### short-term scheduler(or CPU scheduler)

从准备执行的进程中选择进程并分配CPU

**二者区别**：执行频率，一般只有当进程离开系统后才可能调用长期调度程序

进程可分为**I/O-bound process**和**CPU-bound process**，I/O为主的会在执行I/O花费更多时间。而cpu为主的进程会花更多时间在执行计算，为了不浪费资源，提高性能，系统需要合理的以io为主和以CPU为主的组合进程

### medium-term scheduling

有些操作系统比如分时系统，可能会引入这种调度程序，核心思想是能将进程从内存（或CPU）中移出，之后能被重新调入内存，并从中断处执行，这种方式被称为**swapping**

![](.\picture\QQ截图20211017161603.png)

会增加两个新状态 swapped waiting(suspend waiting/blocked)和swapped ready(suspend ready)

详细调用：

![](.\picture\QQ截图20211017162023.png)

## context switch（上下文切换）

### what

将CPU切换到另一个进程需要保存当前进程的状态并恢复另一个进程的状态，这一任务为context switch

### context

用PCB表示，包括CPU寄存器的值，进程状态和内存信息等

### how

发生时，内核将旧进程的状态保存在其PCB中，并装入经过调度要执行的已保存的新进程的context。

### speed和开销

上下文切换的时间是额外开销

所需时间与硬件支持相关

# 进程操作

## Process Creation进程创建

### reason

* 提交批处理作业
* 用户登录
* 创建子进程
* 用来使用设备

**pid**：进程标识符，用来识别进程，通常是一个整数

### 过程

* 申请一个独有的pid
* 为进程申请空间
* 初始化PCB
* 设置合适的连接，如添加新进程到调度队列的链表中
* 创建其他数据结构
* **resource sharing**
  * 父子进程共享所有资源
  * 子进程共享父进程部分资源
  * 无共享资源
* 初始化数据由父进程传给子进程

### 执行

* 并发执行（concurrently）
* 父进程等待，直到某个或全部子进程执行完

### 地址空间

* 子进程是父进程的复制品，拥有相同的程序和数据
* 或者装入另一个新程序

### UNIX下的例子

**fork()**：创建新进程，创建好的子进程和父进程并发执行接下来的代码（可以看作接下来同一段程序独立运行了两遍），对子进程返回0，对父进程返回子进程的pid

**exec()**:调用fork后使用，用来用一个新程序代替子进程的内存空间，也就说这个进程替换成了其他程序，但是pid不变，所以系统还是把它识别为原来的进程

**int execlp(const char *file,const char *arg,...)**:exec()的变形，可以用参数指定用什么来覆盖原进程的空间。参数1是环境变量，如果没有找到该环境变量返回-1，参数2是命令行参数，可以有多个命令行参数，最后用NULL结尾

**wait()**：令进程等待直到子进程运行结束再继续

![](.\picture\QQ截图20211017175102.png)

### win32下的例子

不想记了告辞

## process termination

当进程完成执行最后的语句并使用系统调用exit()请求os删除自身时，进程终止。此时，进程可以返回状态值到父进程（通过wait()），所有进程资源会被os释放

被终止进程的父进程可以通过sys-call终结进程

### reason

* 子进程使用了超过它所分配到的资源
* 分配给子进程的任务不再需要
* 父进程退出，如果父进程终止，os不允许子进程继续

# Interprocess Communication(进程间通信)

* independent process：不能被其他进程影响或影响其他进程
* cooperating process：能

advantages of co-process

* information sharing
* computation speedup
* modularity
* convenience

## 通信模型

### shared memory

允许以最快的速度进行方便的通信，在计算机中可以达到内存的速度

### message passing

通常用系统调用实现，需要更多时间来让内核介入

![](.\picture\QQ截图20211017190651.png)

## shared-memory systems    and   producer-consumer problem

生产者消费者问题时协作进程的通用范例，生产者进程产生信息供消费者进程消费

为了允许生产者进程和消费者进程能并发执行，必须要有一个缓冲区填充在两个进程的共享内存区域

* unbounded-buffer 无限缓冲
* bounde-buffer 有限缓冲

#### 共享缓存

由循环数组和两个逻辑指针in，out来实现

![](.\picture\QQ截图20211017191843.png)

in指向缓冲下一个空位，out指向缓冲区第一个满位

当in==out时，缓冲为空

当(in+1)%BUFFER_SIZE=out时，缓冲为满，允许最大项数为BUFFER_SIZE-1

<img src=".\picture\QQ截图20211017192743.png" style="zoom:33%;" />

## message-passing systems and interprocess communication(IPC)

消息传递提供一种机制来允许进程不必通过共享地址空间来实现通信和同步

ipc提供至少两种操作

* send
* receive

逻辑实现的方法

* 直接或间接通信
* 同步或异步通信
* 自动或显示缓冲

#### 相关问题

##### 命名

对于**直接通信**，需要通信的每个进程必须明确通信的接收者或发送者。方案如下

![](.\picture\QQ截图20211017193902.png)

具有如下属性

![](.\picture\QQ截图20211017193944.png)

缺点：限制了进程定义的模块化。改变进程的名称可能必须检查所有其他进程定义

对于**间接通信**，通过邮箱或者端口发送信息，邮箱可以抽象为对象，每个邮箱有唯一的标识符，方案如下

![](.\picture\QQ截图20211017194308.png)

具有如下属性：

![](.\picture\QQ截图20211017194341.png)

如果邮箱为进程所有，则需要区分拥有者（只能通过邮箱接收消息）和使用者（只能发送），每个邮箱有唯一的标识符

如果为os所有，则必须提供机制来允许进程创建删除邮箱，发送接收消息

##### 同步

**阻塞与否也被称为同步和异步**

* 阻塞send：发送进程阻塞，直到消息被接受
* 非阻塞
* 阻塞receive：接收阻塞，直到有消息可用
* 非阻塞：接受有效消息或空消息

##### 缓冲

![](.\picture\QQ截图20211017195746.png)

# IPC实例

## POSIX 共享内存

主要调用以下api

### shm_open

```C
int shm_open(const char *name,int oflag,mode_t mode)
```

需要以下头文件

```C
#include <sys/mman.h>
#include <sys/stat.h>        /* 对于模式常量 */
#include <fcntl.h>           /* 对于 oflag 常量 */
```

该函数会打开或创建一个共享内存对象，通常保存在/dev/shm/路径下，实质上就是一个文件，linux认为所有的对象都是文件，可以使用文件操作函数，

返回值为文件描述符fd，成功时为正数，失败时为负数

各参数含义：

* name：文件名，不能填写路径
* oflag：位掩码，表示文件操作属性
  * **O_RDONLY** 只读
  * **O_RDWR** 读写
  * **O_CREAT** 若文件不存在，则创建
  * **O_EXCL** 在指定O_CREAT的情况下若文件已存在，则返回错误
* **mode：** 表示文件的共享模式，应在指定O_CREAT的情况下使用，即创建文件后的用户权限

### ftruncate

```C
int ftruncate(int fd, off_t length)
```

位于头文件 *unistd.h* 中
ftruncate()会将参数 fd 指定的文件大小改为参数 length 指定的大小。如果原来的文件大小比参数length大，则超过的部分会被删去。
off_t 是 long 的宏
使用该函数时，打开的文件必须具有写入权限
该函数的功能是重置文件的大小，任何open函数打开的文件都可以用该函数，包括但不限于shm_open打开的文件
执行成功则返回0，失败返回-1，错误原因存于errno（errno.h中定义的一个int型变量）。

### mmap

```C
void* mmap(void* start,size_t length,int prot,int flags,int fd,off_t offset);
```

位于头文件 *sys/mman.h* 中
**该函数的功能是将文件映射到内存中，使得我们用操作内存指针的方式来操作文件数据。**
文件被映射到多个页上，如果文件的大小不是所有页的大小之和，最后一个页不被使用的空间将会清零。 即映射的内存空间最小单位为页
其中各参数：

- **start：** 将文件映射到的内存地址，一般应该传递NULL来由Linux内核指定。

- **length：** 映射区的长度。长度单位是以字节为单位，不足一内存页按一内存页处理

- **prot:** 是一个位掩码，映射的内存区域的操作权限（保护属性），包括PROT_READ、PROT_WRITE、PROT_EXEC、PROT_NONE

- flags: 

  位掩码，指定映射对象的类型，包括

  - MAP_SHARED： 与其它所有映射这个对象的进程共享映射空间。对共享区的写入，相当于输出到文件。直到msync()或者munmap()被调用，文件实际上不会被更新。
  - MAP_PRIVATE： 建立一个写入时拷贝的私有映射。内存区域的写入不会影响到原文件。这个标志和以上标志是互斥的，只能使用其中一个。
  - MAP_ANON: 进行匿名映射，此时fd参数要填-1
  - 等

- **fd：** 用来建立映射区的文件描述符，用 shm_open打开或者open打开的文件。

- **offset：** 映射文件相对于文件头的偏移位置，应该按4096字节对齐。

成功执行时，mmap()返回被映射区的指针；失败时，mmap()返回MAP_FAILED。
要对该映射的内存写入内容，可以使用sprintf()或write()函数，在此之前先将mmap的返回值类型由 void* 转换成 char*

### munmap

```C
int munmap(void *start,size_t length)
```

使用头文件 *unistd.h* 和 *sys/mman.h*
**函数功能是取消参数start所指的映射内存起始地址，参数length则是欲取消的内存大小。**
当进程结束或利用exec相关函数来执行其他程序时，映射内存会自动解除，但关闭对应的文件描述符时不会解除映射。
返回值: 如果解除映射成功则返回0，否则返回－1，错误原因存于errno中

### shn_unlink

```C
int shm_unlink(const char *name):
```

使用头文件 *sys/stat.h* 和 *fcntl.h*
该函数用于删除指定的共享内存对象

# Communication in Client-Server systems

## Socket

emmm

## Remote Procedure Calls(RPC)

有机会再说吧

## Remote Method Invocation(RMI,java)

不会java啊

## Pipes管道通信

### ordinary pipe in UNIX

```C
int pipe(int filedes[2])
```

使用头文件 *unistd.h*
该函数会在两个进程中创建一个**匿名(普通)管道**， 参数数组即为要通信的两个文件的文件描述符（不需要我们赋值，在调用pipe函数后直接将[0]和[1]当做文件操作对象）
**其中[0]为管道读端，只允许从该端读取管道中的信息；[1]为管道写端，只允许从该段向管道中写信息**

管道可以看作是一个特殊共享文件，实质是在内存区当中开辟一个固定大小的缓冲区
从管道中读取走的那部分信息会从缓冲区中清楚
需要注意的是**管道是半双工通信的，如果需要双方通信时，需要建立起两个管道**
**管道只能用于父子进程(fork())或者兄弟进程之间，即具有亲缘关系的进程**
**返回值：成功 0 失败 -1**，错误信息保存在errno中

通常情况下，一个进程先用pipe()创建一个管道后，再用fork()创建子进程，实现两个父子进程间的通信 

对管道的读写，可以使用文件相关的函数read()、write()、close()，(这些函数的参数都是文件描述符)
如read(filedes[0], buf, BUF_LEN)可将管道中指定字节数读到buf中

**对于不再使用的管道端，一定要记得用close()关闭它**

**若有多个进程同时对写端进行写入，根据测试，可以都写入，而且不会乱序**

### named pipes

