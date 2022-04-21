# 背景

consistency:一致性

要维护数据一致性，对共享数据的访问做限制

## race condition

多个进程并发访问和操作数据且执行结果与访问的特定顺序有关

为了避免这种情况，要求并发进程必须要synchronized

# The Critial-Section Problem

**critical section**（临界区）：是一个代码段，不允许两个及以上进程同时访问

临界区问题就是设计一个协议来让每个进程必须请求允许进入临界区,实现这一请求的代码段被称为**entry section**，临界区后可以有**exit section**剩余代码段被称为**remainder section**

## 要求：

* mutual exclusion(互斥)
* progress(前进)：如果没有进程在临界区执行且有进程需要进入临界区，那么只有那些不在剩余区的进程可以参加选择，且这种选择不能无限推迟
* bounded wating(有限等待)：从一个进程提出请求，到该进程被允许进入临界区，其他进程进入临界区的上限有次数（也就是说不会请求了一直进入不了）

## os 内的临界区问题处理：

* preemptive kernel:允许处于内核模式的进程被抢占
* 非抢占式

# petersonn算法

基于软件的临界区问题解决方法，适用于两个进程在临界区和剩余区交替进行

## 算法1

![](.\chap6picture\QQ截图20211028110918.png)

progress不能保证，如：p0每分钟执行一次，p1每天执行一次

有限等待也不能保证，若一个进程执行后退出，则另一个进程的turn不能改变，会持续阻塞

## 算法2

![](.\chap6picture\QQ截图20211028111627.png)

有可能出现死锁，两个进程都无法进入

![](.\chap6picture\QQ截图20211028111246.png)

## peterson算法

![](.\chap6picture\QQ截图20211031133637.png)

## 面包房算法

针对多个进程并发

进入临界区之前每个进程获得一个数字，最小数字的进程先进入临界区

若有两个进程的数字相同，比较pid

**要定义的共享数据**：

boolean choosing[n]:表示进程状态，为true时该进程要取得编号，取完之后设为false

int number[n]：编号

初始为false和0

# Synchronization Hardware

## disable interrupts

* 单处理器：当前运行的代码不会被抢占
* 多处理器：不可行，过于费时

**多处理器采用锁的方式**

**现代机器提供了特殊的atomic(不可被中断)硬件指令**

* TestAndSet 测试内存字并赋值
* Swap 交换两个锁的值

![](.\chap6picture\QQ截图20211101100501.png)

**只使用TestAndSet无法保证有限等待**，无序竞争（来的早不如来得巧）

Swap同理

### 机器指令的优缺点

* 优点：
  * 简单

* 缺点：
  * Busy-waiting，一直在while循环占用CPU资源
  * Starvation
  * deadlock: 如果一个低优先级进程获得临界区进入权，然后切换到ready状态，此时一个高优先级进程被调度，高优先级会等低优先级执行完临界代码，但是低优先级在ready状态中无法跨过高优先级被调度

### 改进

![](.\chap6picture\QQ截图20211101101955.png)

# Semaphores(信号量)

一种提供更复杂的同步进程活动的同步工具

**不需要busy-waiting**

**是一个特殊变量，具有整数值，只能做两种操作，wait和signal**

![](.\chap6picture\QQ截图20211101103059.png)

wait和signal不可被打断，即原子性的(atomic)

## 信号量使用

* 二值量：只能是0和1
* counting senaphore: 可以控制访问具有若干个实例的某种资源
  * 初始化为可用的资源数
  * 当有进程要使用资源，执行一次wait()
  * 释放资源时，执行一次signal()
  * 当count变成0，说明所有资源都在被使用，这时如果有新进程要使用该资源，会等待

**依然会有busy-waiting**

## 没有busy waiting的信号量

将信号量定义为一个结构体

![](.\chap6picture\QQ截图20211101104939.png)

其中链表是指向PCB的指针

**系统调用：**

* block()：将进程调用到合适的waiting队列

* wakeup(P)：把一个进程从waiting移到ready队列

* wait():

  ![](.\chap6picture\QQ截图20211101105209.png)

* signal()

![](.\chap6picture\QQ截图20211101105242.png)

### 问题

* 必须确保没有两个进程能同时对同一个信号量进行操作，**这本身也是临界区问题**
* 死锁：两个或多个进程无限等待一个事件，而该事件只能由这些等待进程产生
* starvation

### 优先级倒置

inherit:继承

当优先级低的进程在使用资源时，若有高优先级进程被调度，临时将低优先级的优先级增加到与高优先级相同，使用完资源后再回调，避免starvation

## bounded-buffer problem

定义信号量并初始化为

* full=0
* empty=n
* mutex=1

![](.\chap6picture\QQ截图20211101111231.png)

互斥信号量又称为公有信号量（mutex)，同步信号量又称为私有信号量（full,empty）

## readers-writers problem

写者和写者之间互斥，写者和读者也有互斥，读者和读者共享

### 读者优先

读者读时写者不能写

写者可能会出现starvation

### 写者优先

写者再等待申请共享空间时，不允许新读者来读

读者可能会starvation

![](.\chap6picture\QQ截图20211114214604.png)

## 哲学家进餐问题

* 允许最多四个哲学家同时进餐

  ![](.\chap6picture\QQ截图20211104101803.png)

* 只有两支筷子都空闲时才允许拿起筷子
* ![](.\chap6picture\QQ截图20211104101906.png)

* 奇数位先左后右，偶数位先右后左
* ![](.\chap6picture\QQ截图20211104102359.png)

**会出现死锁**

# 管程





