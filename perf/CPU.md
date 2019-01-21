# CPU的性能优化
## 什么是平均负载

平均负载是指单位时间内，系统处于可运行状态和不可中断状态的平均进程数，也就是平均活跃进程数，跟CPU使用率没有直接关系。可以通过uptime来查看
```
marvin@ubuntu:~$ uptime
 23:49:16 up  1:18,  1 user,  load average: 0.55, 0.52, 0.46
```


这里需要解释可运行状态和不可中断状态，不可中断状态是一种保护机制，保证在根硬件交互的时候，不被打断。常见的linux的进程状态
* RUNNING：正在运行或者在就绪队列中等待运行的进程。也就是上面提到的运行态和就绪态进程的综合。一个进程处于RUNNING状态，并不代表他一定在被执行。由于在多任务系统中，各个就绪进程需要并发执行，所以在某个特定时刻，这些处于RUNNING状态的进程之中，只有一个能得到处理器，而其他进程必须在一个就绪队列中等待。即使是在多处理器的系统中，Linux也只能同时让一个处理器执行任务。
* UNINTERRUPTABLE:不可中断阻塞状态。处于这种状态的进程正在等待队列中，当资源有效时，可由操作系统进行唤醒，否则，将一直处于等待状态。
* INTERRUPTABLE：可中断阻塞状态。与不可中断阻塞状态一样，处于这种状态的进程在等待队列中，当资源有效时，可以有操作系统进行唤醒。与不可中断阻塞状态有所区别的是，处于此状态中的进程亦可被其他进程的信号唤醒。
* STOPPED：挂起状态。进程被暂停，需要通过其它进程的信号才能被唤醒。导致这种状态的原因有两种。其一是受到相关信号(SIGSTOP,SIGSTP,SIGTTIN或SIGTTOU)的反应。其二是受到父进程ptrace调用的控制，而暂时将处理器交给控制进程。
* ZOMBIE：僵尸状态。表示进程结束但尚未消亡的一种状态。此时进程已经结束运行并释放掉大部分资源，但尚未释放进程控制块。


### 平均负载多少合适
**一般平均负载跟系统的CPU核数对应，如果超过的cpu数量，可能代表高负载了**。具体的情况最好长期监控，长期分析。一般**当平均负载高于CPU数量70%的时候**，就需要关注了

### 平均负载和使用率
平均负载，它不仅包括了正在使用 CPU的进程，还包括**等待 CPU**和**等待 I/O**的进程。而 CPU 使用率，是单位时间内 CPU 繁忙情况的统计，跟平均负载并不一定完全对应。
CPU密集型和IO繁忙都可能导致平均负载高


### 常用命令安装和使用
```
grep 'model name' /proc/cpuinfo | wc -l
sudo apt install stress sysstat htop
uptime
ps -aux
top
# CPU密集
stress --cpu 1 --timeout 600

# I/O密集型
stress -i 1 --timeout 600
stress-ng -i 1 --hdd 1 --timeout 600

# 大量进程的场景
stress -c 4 --timeout 600

watch -d uptime
mpstat -P ALL 5
pidstat -u 5 1
pidstat -d
```

## CPU的上下文切换

简单理解，就是把寄存器和程序计数器先保存下来，然后切换其他任务的上下文。常见的上下文切换：
* 进程上下文切换
* 线程上下文切换
* 中断上下文切换

### 进程上下文切换
** 这里一个进程的内核态和用户态的基础知识 **



从用户态切换到内核态（系统调用），就存在上下文切换；同时恢复用户态也会切换。系统调用过程通常称为特权模式切换，而不是上下文切换。但是CPU的上下文切换却不可避免。系统调用属于同进程内的CPU上下文切换；

进程是由内核来管理和调度的，进程的切换只能发生在内核态。所以，进程的上下文不仅包括了用户空间的资源，也包括内核空间资源。

进程上下文切换，是指从一个进程切换到另一个进程运行
进程的上下文切换就比系统调用时多了一步：在保存当前进程的内核状态和 CPU 寄存器之前，需要先把该进程的虚拟内存、栈等保存下来；而加载了下一进程的内核态后，还需要刷新进程的虚拟内存和用户栈。

每次上下文切换都需要几十纳秒到数微秒的 CPU 时间

可能发生进程上下文切换的环境：
1. 时间片耗尽
2. 系统资源不足（比方说，内存不足）
3. sleep自己挂起
4. 有高优先级的进程
5. 硬件中断发生

### 线程上下文切换

线程也有自己的私有数据，比如栈和寄存器等
所以切换就有两种：
* 不同进程的线程切换
* 一个进程内的多个线程切换

### 中断上下文切换
中断处理会打断进程的正常调度和执行

对同一个 CPU 来说，中断处理比进程拥有更高的优先级，所以断上下文切换并不会与进程上下文切换同时发生。

### 有哪些减少上下文切换的技术用例？
数据库连接池（复用连接）、合理设置应用的最大进程，线程数、直接内存访问DMA、零拷贝技术

### 怎样查看上下文切换情况
vmstat:
* cs -> context switch
* in -> interrupt
* r -> Runnable就绪队列长度
* b -> 处于不可中断睡眠状态的进程数

pidstat -w:
* cswch/s, 自愿上下文切换，是指进程无法获取所需资源，导致的上下文切换。比如说， I/O、内存等系统资源不足时
* nvcswch/s, 非自愿上下文切换，则是指进程由于时间片已到等原因，被系统强制调度，进而发生的上下文切换。比如说，大量进程都在争抢 CPU 时

* 自愿上下文切换变多了，说明进程都在等待资源，有可能发生了 I/O 等其他问题； 
* 非自愿上下文切换变多，说明进程都在被强制调度，也就是都在争抢 CPU，说明  CPU 的确成了瓶颈；
* 中断次数变多了，说明 CPU 被中断处理程序占用，还需要通过查看 /proc/interrupts 文件来分析具体的中断类型

### 常用命令
```
sudo su root
apt install sysbench sysstat
vmstat 5 1
pidstat -w 5

sysbench --threads=10 --max-time=300 threads run
vmstat 1
pidstat -w -u 1
# -t才能输出线程的，不然可能看不到
pidstat -wt 1

watch -d cat /proc/interrupts

```

## 找到CPU100%的应用

CPU使用率，可以通过top,ps来查看，但其实基本也都是通过/proc/stat ,/proc/[pid]/stat里计算来的，不过不同工具的间隔时间不一样。

cpu的使用率={1-(idle_time/total_cpu_time)}/sample_time

性能分析工具给出的都是间隔一段时间的平均 CPU 使用率，所以要注意间隔时间的设置。top默认为3s，ps使用的是进程运行时间。

### CPU使用率高怎么查？
top、vmstat、mpstat等命令种关于cpu性能相关指标的含义
* GDB工具，调试方便，但是不推荐
* perf

### 进程不停重启或者很多短时进程
如果碰到不好解释的CPU问题时，比如现象：
通过top观察CPU使用率很高，但是看下面的进程的CPU使用率好像很正常，通过pidstat命令查看cpu也很正常。但通过top查看task数量不正常，处于R状态的进程是可疑点。

首先要想到可能是短时间的应用导致的问题，如下面的两个：
（1）应用里直接调用了其他二进制程序，这些程序通常运行时间比较短，通过top等工具发现不了
（2）应用本身在不停地崩溃重启，而启动过程的资源初始化，很可能会占用很多CPU资源

pstree查查父进程
execsnoop专门查短时进程


### 常用命令
```
# man proc可查看相关说明
cat /proc/stat | grep ^cpu

apt install docker.io sysstat linux-tools-common apache2-utils

perf top -g -p 21515

perf top
perf record
perf report
perf record -ag -- sleep 2;perf report
```