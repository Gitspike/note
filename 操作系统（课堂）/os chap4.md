# Overview

**thread** ：被称为轻量级进程（LWP），cpu使用的最小单元

traditional(or **heaveyweight**)process 只有一个线程

多线程进程的各线程共享进程的资源

![](.\chap4pitcture\QQ截图20211018101518.png)

## 线程与进程

* 进程可以单独存在
* 线程不能
* 进程被交换出时，所有的线程也被换出，反正线程是进程的一部分

## 动机

* 大多数现代软件都是多线程的
* 创建进程开销很大，而创建线程不需要申请新内存，工作量少（light-weight）
* 简化编码
* os kernal 基本是多线程的

## 多线程优点

* Responsiveness(响应度) 
  * 线程操作比进程操作更快
  * 多线程间通信更快，采用共享内存方式

* 资源共享
* 经济
* 多处理器体系结构的利用

## 线程状态

running ,ready , waiting

改变线程状态的操作

* spawn
* block
* unblock
* finish  :释放寄存器上下文和栈

![](.\chap4pitcture\QQ截图20211018103357.png)

## 多核编程

![](.\chap4pitcture\QQ截图20211018104738.png)

**concurrency**: 并发，同一时刻

**parallelism**：并行，同一时刻多重任务都在推进

也就是说并行才是真正同时进行，并发是频繁交替工作

**数据并行**：

将处理的数据分开，每个核处理的任务是一样的

**任务并行**：

处理不同的任务

# 多线程模型

两种提供线程支持的方法

* 用户线程
  * 用户线程必须映射到一个内核线程上才能运行
  * 通过应用程序调用api自己来管理，内核并不知道线程存在
* 内核线程
  * 由os直接管理

当代操作系统几乎都支持内核线程

## 多对一模型

多个用户线程（ULT）映射到一个内核线程（KLT）

线程管理在用户空间，通过用户自己来控制，内核不知道有多线程存在

如果一个线程执行了阻塞sys-call，整个进程都被阻塞

多线程不能并行运行在多处理器上

对内核来说，各线程与内核的通信都看作是进程在向内核发送请求

## 一对一模型

每个用户线程映射到一个内核线程

唯一缺点是创建内核线程的开销会影响应用程序性能

## 多对多模型

![](.\chap4pitcture\QQ截图20211018110048.png)

### 两级模型

多对多基础上允许某个线程绑定到某个内核线程

![](.\chap4pitcture\QQ截图20211018110302.png)

# 线程库

## 同步线程

父线程必须等到子线程结束才能恢复执行，也被称为fork-join strategy

## 异步线程

创建好子线程后父线程就继续执行，父子线程是并发执行

## Pthread

```C
int  pthread_create(pthread_t *tidp, const  pthread_attr_t *attr,( void *)(*start_rtn)( void *), void  *arg);
```

编译链接参数：-lpthread

第一个参数为指向线程 标识符的 指针。
第二个参数用来设置线程属性。
第三个参数是线程运行函数的起始地址。
最后一个参数是运行函数的参数。

```C
pthread_t tid  //无符号长整型，线程标识符
pthread_attr_t attr; //线程属性的集合，是一个结构体，详细定义如下：
typedef struct
             {
                  int  detachstate;    //线程的分离状态
                  int  schedpolicy;   //线程调度策略
                  structsched_param     schedparam;   //线程的调度参数
                  int  inheritsched;   //线程的继承性
                  int  scope;         //线程的作用域
                  size_t guardsize;    //线程栈末尾的警戒缓冲区大小
                  int stackaddr_set;   //线程栈的设置
                   void* stackaddr;     //线程栈的位置
                  size_t  stacksize;    //  线程栈的大小
            }pthread_attr_t;
```

**线程属性函数**

```C
int pthread_attr_init(pthread_attr_t *attr);
```

初始化线程属性对象，为其赋默认值，成功返回0，失败返回非零

```C
int pthread_attr_destroy(pthread_attr_t *attr);
```

销毁线程对象，返回值同上

```C
int pthread_join(pthread_t tid, void **retval);
```

以阻塞的方式等待thread指定的线程结束。当函数返回时，被等待线程的资源被收回。如果线程已经结束，那么该函数会立即返回。并且thread指定的线程必须是joinable的。
参数 ：thread: 线程标识符，即线程ID，标识唯一线程。

​			retval: 用户定义的指针，用来存储被等待线程的返回值，一般写为NULL即可。
返回值 ： 0代表成功。 失败，返回的则是错误号。

一个线程不能被多个线程等待

```C
void pthread_exit( void * value_ptr );
```

终止线程，参数：若pthread_join里为NULL，这里填0，这是因为，pthread_exit的参数会被传入join中作为第二个参数

**线程分离状态**

线程的分离状态决定一个线程以什么样的方式来终止自己。在默认情况下线程是非分离状态的，这种情况下，原有的线程等待创建的线程结束。只有当pthread_join()函数返回时，创建的线程才算终止，才能释放自己占用的系统资源。
而分离线程不是这样子的，它没有被其他的线程所等待，自己运行结束了，线程也就终止了，马上释放系统资源。程序员应该根据自己的需要，选择适当的分离状态。所以如果我们在创建线程时就知道不需要了解线程的终止状态，则可以pthread_attr_t结构中的detachstate线程属性，让线程以分离状态启动。
可以使用pthread_attr_setdetachstate函数把线程属性detachstate设置为下面的两个合法值之一：设置为PTHREAD_CREATE_DETACHED,以分离状态启动线程；或者设置为PTHREAD_CREATE_JOINABLE,正常启动线程。可以使用pthread_attr_getdetachstate函数获取当前的datachstate线程属性。



# 多线程问题

## fork()和exec()

如果程序中一个线程调用fork，如果之后立即调用exec,你不会复制所有的线程，反之子进程会复制所有的线程

## thread cancellation

**异步取消**：一个线程立即终止目标线程

**延迟取消**：目标线程不断检查她是否应该终止。

*取消点*（cancellation point)：线程检查是否被取消并按照请求进行动作的一个位置

pthread_cancel调用并不等于线程终止，它只提出请求。线程在取消请求发出会继续运行，直到到达某个取消点

### 相关函数

```C
int pthread_cancel(pthread_t tid)
```

发送终止信号，运行至取消点时会结束线程

```C
void pthread_testcancel(void);
```

线程取消功能处于启用状态且类型设置为延迟模式（maybe 分离状态）时有效，在当前位置创建取消点

## Signal handling

用来通知进程某个特定的事件发生了

信号可以是同步的也可以是异步的，但是所有信号遵循同样的模式

* 信号是由特定的事件的发生所产生的
* 产生的信号要传送到进程中
* 一旦发送，信号就必须被处理

**同步信号**：

eg.非法内存访问内存或除0，同步信号发送到执行该操作的同一进程

**异步信号**：

当一个信号由运行进程之外的事件产生，那么进程就异步接收这一信号。eg.特殊按键（Ctrl+C）或定时器到时。通常异步信号被发送到另一个进程

每个信号都有一个**default signal handler**，当信号处理是在内核中运行时使用；可以使用**user-defined signal handler**来改写。

### 对于多线程信号发送

通常有如下选择：

* 发送至信号应用的线程
* 发送到进程内每个线程
* 发送到固定线程
* 规定一个特殊线程来接受所有信号

**对同步信号来说**，应当发送至1.

**对异步信号来说**，有些信号应发送给所有线程（如Ctrl+C），还有可能只发送给不拒绝它的第一个线程

## Thread pools

主要思想：在进程开始时创建一定数量的线程，并放入池中等待工作，当服务器收到i请求时，他会唤醒池中的一个线程，并将要处理的请求传递给它，线程完成任务后会返回到线程池中

优点：

* 比临时创建新线程快
* 限制可用线程数量，对于不能支持大量并发线程的系统非常重要

## Thread-specific data

允许线程有自己的数据副本

## Scheduler Activations调度程序激活

多对多模型和二级模型都需要在内核与线程库之间建立通信来动态调整内核中线程的数量以保证最好的性能

**方法**：在用户和内核线程之间设立一个中间数据结构，通常是lightweight process(LWP)

![](.\chap4pitcture\QQ截图20211021165635.png)

每个LWP连接一个内核线程，表现为一个可以调度用户线程运行的虚拟CPU

如果内核线程被阻塞，那么对应的LWP也被阻塞，LWP相连的用户线程也被阻塞

### Scheduler Activations

一种线程库与内核通信的方法：

内核提供一组LWP给应用程序，应用程序可调度用户线程到一个可用的LWP上，即内核必须告知与应用程序有关的特定事件，这个过程被称为**upcalls**

**upcalls**由具有处理upcall处理句柄的线程库处理,并且必须运行在LWP上