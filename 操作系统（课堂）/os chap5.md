# 基本概念

各级调度之间的关系

![](.\chap5picture\QQ截图20211021105138.png)

## CPU-I/O区间周期

进程执行由CPU执行和io等待周期组成，从CPU执行开始

## CPU调度

**turnaround time**周转时间，

### 发生时间

* running to waiting
* running to ready 
* waiting to ready
* terminated

调度发生在2，3情况时，调度方案被称为**preemptive(抢占的)**，1，4被称为非抢占或协作的。

采用非抢占调度时，一旦CPU分配给一个进程，那么它会一直占用直到终止或者切换到waiting

采用抢占调度时，现在运行的进程可以被os中断并转移到ready中

两个进程共享数据时，第一个进程在更新数据时若被抢占，换到第二个进程，此时可能会出现数据不一致的情况

### dispatcher(分派程序)

* 上下文切换
* 切换到用户模式
* 跳转到用户程序的合适位置以重新启动

# 调度准则

* CPU utilization（使用率），对于真实的系统，应使使用率在40%至90%
* throughput：一个时间单元内完成的进程数
* turnaround time：从进程提交到进程完成的时间，为所有时间段之和
* waiting time：在就绪队列中等待所费时间之和
* response time：从提交请求到产生第一次响应的时间

**需要使使用率和吞吐量最大，其余最小化**

# 调度算法

## FCFS scheduling（非抢占）

每个进程加入到FIFO的ready队列

对长进程有利

![](.\chap5picture\QQ截图20211021111631.png)

![](.\chap5picture\QQ截图20211021111656.png)

## SJF（short-job-first)

### 抢占式

又称**shortest-remaining-time-first**，如果cpu区间比当前执行的进程的剩余时间短的新进程到达，抢占。

### 非抢占式

相反

**SJF是最佳的，每个进程平均等待时间最小**

困难是如何知道下一个CPU区间的长度，只能进行预测

## Priority Schedulng优先级调度

每个进程有一个优先级与其关联，相同优先级的进程按FCFS调度，SJF算法属于简单优先级调度

分为：

* 抢占：新来的比原来的高，抢占
* 非抢占

### 问题：

无穷阻塞或饥饿

### solution:

aging,逐渐增加在系统中等待很长时间的进程的优先级

## Round-Robin轮询调度

为交互式系统设计

类似于FCFS，但是增加了抢占

每个进程分配一个较小的CPU时间单元（时间片time slice或time quantum)，时间片过去后，进程被抢占并且加入到ready队列的队尾，**将队列变成环形FIFO队列**

![](.\chap5picture\QQ截图20211025105917.png)

如果就绪队列中有n个进程且时间片为q，每个进程会得到1/n的CPU时间，长度不超过q；必须等待的时间不超过(n-1)q个时间单元

**时间片最好比context switch的时间长，否则会有很多时间浪费在上下文切换上** 

## Multilevel Queue

将就绪队列分成多个独立队列，每个队列有自己的调度算法，如

* 前台（交互式进程）-RR
* 后台（批处理进程）-FCFS

**队列之间必须有调度，否则会出现starvation**

## Multilevel Feedback Queue Scheduling

允许进程在队列间移动

如果一个进程使用太多CPU时间，会被转移到有限度低的队列

如果一个进程在低优先级队列中等待过长时间，会被转移到一个高优先级队列

## Highest Response-Ratio Next Scheduling（高响应比）

非抢占式

![](.\chap5picture\QQ截图20211025111500.png)

高响应比的进程会被优先调度

HRRN综合考虑了等待事件和CPU区间

开销会相对更高

# 多处理器调度

## 类型：

* 松耦合：每个处理器有自己的内存和I/O信道
* 紧耦合：共享主存，由os控制



## 非对称多处理：

有一个boss处理器处理调度决定和其他系统活动

slave处理器执行其他用户代码

slave处理器只向boss发送请求

## Symmetric multiprocessing(SMP)

**peer architechture(symmetric multiprocessing)对等架构（对称多处理）**：

操作系统可以在任何一个处理器上执行

每个处理器自我调度

### ready 队列

* 每个处理器有自己的
* 通用ready 队列：**问题**：要确保不能多个处理器不能同时选中ready队列里的同一个进程，也不能有进程被遗漏

## Processor Affinity

绝大多数SMP系统都努力使一个进程在同一个处理器上运行

*迁移问题*：一个进程迁移到另一个CPU时重新加载cache的开销很大

**类型**：

* 软亲和：不保证
* 硬亲和：允许进程指定它不转移至其他处理器上

## load balancing

负载均衡只在每个处理器有自己的ready队列的系统中是必要的

**两种方法**：

* push migration:一个特殊的任务周期性检查每个处理器的负荷，如果不平衡，把进程从负载过重的处理器push到低的
* pull migration:

## 多处理线程调度



# 线程调度

## 竞争范围（contemtion scope)

* process-contention scope(PCS) 进程内部的线程竞争
* system-contention scope (SCS)：与系统内其他线程竞争

## 线程调度：

* Local Scheduling , process-contention scope (PCS) m:1 and m:n model 
* Global Scheduling, System-contention scope (SCS) 1:1 model uses only SCS

# 算法评估

