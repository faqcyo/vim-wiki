[Back to UBA index](index) [Back to index](/index)

# Sistemas Operativos

[Scheduling](#Scheduling)
  [1. Preemptive and non-preemptive scheduling](#Preemptive and non-preemptive scheduling)
  [2. Dispatcher](#Dispatcher)
  [3. Scheduling Criteria](#Scheduling Criteria)
  [4. Scheduling Algorithms](#Scheduling Algorithms)
    [# First-Come, First-Served Scheduling](#First-Come, First-Served Scheduling)
    [# Shortest-Job-First Scheduling](#Shortest-Job-First Scheduling) 
    [# Round-Robin Scheduling](#Round-Robin Scheduling)
    [# Priority Scheduling](#Priority Scheduling)
    [# Multilevel Queue Scheduling](#Multilevel Queue Scheduling)
    [# Multilevel Feedback Queue Scheduling](#Multilevel Feedback Queue Scheduling)
  [5. Thread Scheduling](#Thread Scheduling)
    [# Contention Scope](#Contention Scope)
  [6. Multi-Processor Scheduling](#Multi-Processor Scheduling)
    [# Multicore Processors](#Multicore Processors)
    [# Load Balancing](#Load Balancing)
    [# Processor Affinity](#Processor Affinity)
  [7. Real-Time CPU Scheduling](#Real-Time CPU Scheduling)
    [# Minimizing Latency](#Minimizing Latency)
    [# Priority-Based Scheduling](#Priority-Based Scheduling)
    [# Rate-Monotonic Scheduling](#Rate-Monotonic Scheduling)
    [# Earliest-Deadline-First Scheduling](#Earliest-Deadline-First Scheduling)
    [# Proportional Share Scheduling](#Proportional Share Scheduling)
[Synchronization](#Synchronization)
  [1. The Critical-Section Problem](#The Critical-Section Problem)
  [2. Hardware Support for Sinchronization](#Hardware Support for Sinchronization)
    [# Hardware Instructions](#Hardware Instructions)
    [# Atomic variables](#Atomic variables)
  [3. Mutex Locks](#Mutex Locks)
  [4.Semaphores](#Semaphores)
    [# Semaphore Usage](#Semaphore Usage)
    [# Semaphore Implementation](#Semaphore Implementation)
  [5. Monitors](#Monitors)
  [6. Liveness](#Liveness)
    [# Deadlock](#Deadlock)
[Deadlocks](#Deadlocks)
  [1. Necessary Conditions](#Necessary Conditions)
  [2. Deadlock Prevention](#Deadlock Prevention)
    [# Mutual exclusion](#Mutual exclusion)
    [# Hold and Wait](#Hold and Wait)
    [# No preemption](#No preemption)
    [# Circular Wait](#Circular Wait)
[Synchronization Examples](#Synchronization Examples)
  [1. Classic Problems of Synchronization](#Classic Problems of Synchronization)
  [2. The Bounded Buffer Problem](#The Bounded Buffer Problem)
  [3. The Readers-Writers Problem](#The Readers-Writers Problem)
  [4. The Dining-Philosophers Problem](#The Dining-Philosophers Problem)
  


# Scheduling

(From Operating System Concepts, chapter 5) Processes alternate between
two states: **CPU Burst** and **I/O Burst**. Process execution begins with a CPU
Burst, that is followed by an I/O Burst, which is followed by another
CPU Burst. The cycle finishes with a CPU Burst that indicates a system
request for the termination of the process.

When implementing a **CPU Scheduling algorithm**, its important to take note on **what the program is supposed to do.** If it is a heavily input based program, then the CPU Bursts should have a lot of small CPU Bursts. But with a CPU based program, the CPU Bursts should have less long CPU Bursts.

The CPU Scheduler has the responsibility to select a process from the processes in the **ready queue**, and then allocates the CPU to that process, whenever the CPU becomes idle.

A **PCB** (Process Control Block) is a data structure used by the operating system to store information about a process.

The ready queue is generally composed of PCBs.

*Question:* What does the operating system do in the middle of an I/O bursts? Does it just wait for input? Or it just waits for a defined time frame until the I/O burst finish and changes to CPU Burst?

*Answer:* Given a process, whenever it tries some i/o operation (such as reading from memory), the program goes to a **waiting state** and the I/O Burst of the program begins. The process is in this state until the data is written/read or the space to store it cache runs out (?). When this burst ends, the process goes to the running state again and the cycle continues with a CPU burst.

## Preemptive and non-preemptive scheduling

CPU Scheduling decisions may take place under the following four circumstances:

  1.  When a process switches from the running state to the waiting state (for example, as the result of an I/O request or an invocation of wait() for the termination of a child process);
  2.  When a process switches from the running state to the ready state (for example, when an interrupt occurs);
  3.  When a process switches from the waiting state to the ready state (for example, at completion of I/O);
  4.  When a process terminates;

The situations 1 and 4 are called **nonpreemptive or cooperative**, and the scheduler has no choice other than select a new process (if one exists) from the ready queue. The situtations 2 and 3 are called preemptive

Under nonpreemptive scheduling, once the CPU has been allocated to a process, the process keeps the CPU until it releases it either by terminating or by switching to the waiting state.

When data is shared among many processes, a preemptive scheduling may produce race conditions.

A nonpreemptive kernel will wait for a system call to complete or for a process to block while waiting for I/O to complete to take place before doing a context switch. Thus, its not used in real time computing.

## Dispatcher

The dispatcher is the module that **gives control** of the CPU to the process that is selected by the scheduler.

Its function involves the following:

  - Switching context from one process to another
  - Switching to user mode
  - Jumping to the proper location in the user program to resume that program.

Since the dispatcher is invoked in every context switch, it should be as fast as possible.

The time it takes the dispatcher to stop the execution of one process and start the execution of other process is known as **dispatcher latency**

The amount of context switches per second varies with each system. Use the vmstat command on linux, and look for the cs column. It can also be viewed inside the /proc/{pid}/status file, look for

A **voluntary** context switch occurs when a process has given up control of the CPU because it requires a resource that is currently unavailable (such as blocking for I/O) A **nonvoluntary** context switch occurs when the CPU ahs been taken away from a process.

## Scheduling Criteria

The following are criterias for distinguishing different scheduling
algorithms:

  1. CPU Utilization
  2. Throughput: The number of processes that are completed per time unit.
  3. Turnaround time: How long it takes to execute a particular process.  The interval from the time of submission of a process to the time of completion is the turnaround time.
  4. Waiting time: The sum of the periods spent waiting in the ready queue.
  5. Response time: The time from the submission of a request until the first response is produced. That is the time it takes to start responding, not the time it takes to output the response.

It is desirable to maximize CPU utilization and throughput and to minimize turnaround time, waiting time, and response time.

## Scheduling Algorithms

### First-Come, First-Served Scheduling

It's the simplest one of them. With this scheme, the process that requests the CPU first is allocated the CPU first. The implementation of this scheme is done with a FIFO (First in, First out) queue. On the negative side, the average waiting time under the FCFS policy is often quite long.

### Shortest-Job-First Scheduling

When the CPU is available, the SJF policy selects the process that has the smallest next CPU burst. It cannot be implemented because there is no way of knowing the length of the next CPU burst. Though, we can do some statistical shenanigans to predict its value. This algorithm can be either preemptive or nonpreemptive. Preemptive SJF scheduling is sometimes called shortest-remaining-time-first scheduling.

### Round-Robin Scheduling

Similar to FCFS scheduling, but preemption is added to enable the system to switch between processes. A small unit of time, called a time quantum or time slice, is defined.

A time quantum is generally from 10 to 100 milliseconds in length.

The ready queue is treated as a circular queue.

The CPU scheduler goes around the ready queue allocating the CPU to each process for a time interval of up to 1 time quantum.

To implement RR scheduling, we treat the ready queue as a FIFO queue of processes.

The RR scheduler workflow is as follows:

  1. The CPU scheduler picks the first process from the ready queue.
  2. Then one of two thing will happen:
    1. The proccess actually running will take a CPU burst of less than a quantum, then this process goes to a waiting state, the scheduler will then proceed to the next process in the ready queue.
    2. The proccess actually running will take a CPU burst of more than a quantum, then the timer will go off and will cause an interrupt to the operating system. A context switch wil lbe executed, and the process will be put at the tail of the ready queue. The CPU scheduler will then select the next process in the ready queue.

The average waiting time under the RR policy is often long.

We want the time quantum to be large with respect to the context switch time.

A rule of thumb is that 80 percent of the CPU bursts should be shorter than the time quantum.

### Priority Scheduling

The SJF algorithm is a special case of the general priority-scheduling algorithm. A priority is associated with each process, and the CPU is allocated to the process with the highest priority.

Equal priority processes are scheduled in FCFS order.

The SJF algorithm is a priority algorithm where the priority is equivalent to the inverse of the predicted next CPU burst

Priority Scheduling can be either preemptive or nonpreemptive:

  - A preemptive priority scheduling algorithm will preempt the CPU if the priority of the newly arrived process is higher than the priority of the currently running process.
  - A nonpreemptive priority scheduling algorithm will simply put the new process at the head of the ready queue.

A major proglem with priority scheduling algorithms is **indefinit blocking** or **starvation**.

A process that is ready to run but waiting for the CPU can be considered blocked.

A solution to starvation is **aging**. Aging involves gradually increasing the priority of processes that wait in the system for a long time.

One way of using this algorithm is combining it with Round Robin. The workflow is as follow:

  1. We execute the process with higher priority. Then we encounter two cases:
    1. This process is the only process with that priority. So we just wait until it is finished.
    2. There are more processes with that priority. We follow the Round Robin algorithm to execute every single one of them
  2. After each process from that priority terminated its execution, we move to the next priority and repeat.

### Multilevel Queue Scheduling

Instead of having only one queue for every process with different priorities, what the Multilevel Queue Scheduling algorithm suggests is having **multiple queues** for each priority, and let the Priority Scheduling algorithm schedule the process in the highest-priority queue. This algorithm also benefits from combining the Priority Scheduling algorithm with Round Robin Scheduling algorithm.

A multilevel queue scheduling algorithm can also be used to **partition processes** into several separate queues based on the process type. Each queue might ahve its own scheduling algorithm. In addition, there **must be scheduling among the queues**, which is commonly implemented as fixed-priority preemptive scheduling.

Its important to note that when a process is assigned a queue, that process cannot move to other queue. That is, each process gets permanently assigned to one of the multiple queues defined. This setup has the advantage of low scheduling overhead.

### Multilevel Feedback Queue Scheduling

Allows the process to move between queues. the idea is to separate processes according to the characteristics of their CPU bursts. If a process uses too much CPU time, it will be moved to a lower priority queue. In addition, a process that waits too long in a lower-priority queue may be moved to a higher-priority queue to avoid starvation.

In general, a multilevel feedback queue scheduler is defined by the following parameters:

  - The number of queues
  - The scheduling algorithm for each queue
  - The method used to determine when to upgrade a process to a higher priority queue
  - The method used to determine when to demote a process to a lower priority queue
  - The method used to determine which queue a process will enter when that process needs service.

## Thread Scheduling

On most modern operating systems it is kernel-level threads -not processes- that are being scheduled by the operating system. 
User-level threads are managed by a thread library, and the kernel is unaware of them.

### Contention Scope

One distinction between user-level and kernel-level threads lies in how they are scheduled. 
  * On sysmtes implementing the many-to-one and many-to-many models, the thread library schedules user-level threads to run on an available LWP (lightweight process). This scheme is know as **process-contention scope (PCS)**, since competition for the CPU takes place among threads belonging to the same process. Tipically, PCS is done according to priority.
  * To decide which kernel-level thread to schedule onto a CPU, the kernel uses **system-contention scope (SCS)**. Competition for the CPU with SCS scheduling takes place among all threads in the system.

## Multi-Processor Scheduling

One approach to CPU scheduling in a multiprocessor system has all scheduling decision, I/O processing, and other system activities handled by a single processor - the master server. The other processors execute only user code. This is called **asymmetric multiprocessing.**

The standard approach for supporting multiprocessors is **symmetric multiprocessing (SMP)**, where each processor is self-scheduling. Scheduling proceeds by having the scheduler for each processor examine the ready queue and select a thread to run. This provides two possible strategies for organizing the threads eligible to be scheduled:
  1. All threads may be in a common ready queue.
  2. Each processor may have its own private queue of threads.
If we choose the first strategy, we have a possible race condition on the shared ready queue and therefore must ensure that two separate processors do not choose to schedule the same thread and that threads are not lost from the queue.

The second option permits each processor to schedule threads from its private run queue and therefore does not suffer from the possible performance problems associated with a shared run queue. Thus it is the most common approach on systems supporting SMP.

### Multicore Processors
 
A **memory stall** occurs when a processor spends a significant amount of time waiting for the data to become available. A memory stall can also occur because of a cache miss.

To remedy the bottleneck generated by the memory stalls, many recent hardware designs have implemented multithreaded processing cores in which two (or more) hardware threads are assigned to each core. That way, if one hardware thread stalls while waiting for memory, the core can switch to another thread. For an operating system perspective, each thread appears as a logical CPU that is available to run a software thread. This technique is known as **chip multithreading (CMT)**.

In general, there are two ways to multithread a processing core:
  1. **Coarse-grained multithreading**: a thread executes on a core until a long-latency event such as a memory stall occurs
  2. **Fine-grained multithreading**: switches between threads at a much finer level of granularity -typically at the boundary of an instruction cycle.

It is important to note that the resources of the physical core (such as caches and pipelines) must be shared among its hardware threads, and therefore a processing core can only execute one hardware thread at a time. Consequently, a multithreaded, multicore processor actually requires two different levels of scheduling:
  1. On the first level there are the scheduling decisions that must be made by the operating system as it chooses which software thread to run on each hardware thread (logical CPU). The operating system may choose any scheduling algorithm.
  2. The second level of scheduling specifies how each core decides which hardware thread to run.

### Load Balancing

On SMP systems, it is important to keep the workload balanced among all processors to fully utilize the benefits of having more than one processor. Otherwise, one or more processors may sit idle while other processors have high workloads, along with ready queues of threads awaiting the CPU.

**Load balancing** attempts to keep the workload evenly distributed across all processors in an SMP system.

load balancing is typically necessary only on systems where each processor has its own private ready queue of eligible threads to execute.

There are two general approaches to load balancing:
  1. Push migration: a specific task periodically checks the load on each processor and (if it finds an imbalance) evenly distributes the load by moving (or pushing) threads from overloaded to idle or less-busy processors.
  2. Pull migration: occurs when an idle processor pulls a waiting task from a busy processor. 

Pull and Push migration need not be mututally exclusive.

### Processor Affinity

**Processor affinity** is known as the attempt of the operating system to prevent moving one thread from one core to other in cases where the cache from the first core is warm (that is recently populated).

Then a process has an affinity for the processor on which it is currently running.

We say that a situation where the operating system has a policy of attempting to keep a process on the same processor (but not guaranteeing that it will do so) is **Soft affinit**. Here, it is possible that the process moves to other threads (or processor from the operating system perspective) when doing a load balance.

In contrast some systems provide system calls that support hard affinit, thereby allowing a process to specify a subset of processors on which it can run.

Many systems provide both soft and hard affinity.

## Real-Time CPU Scheduling

In general, we can distinguish between soft real-time systems and hard real-time systems

**Soft real-time systems** provide no guarantee as to when a critical real-time process will be scheduled. They guarantee only that the process will be given preference over noncritical processes.
**Hard real-time systems** have stricter requirements. A task must be serviced by its deadline; service after the deadline has expired is the same as no service at all.

### Minimizing Latency

We refer to **event latency** as the amount of time that elapses from when an event occurs to when it is serviced.

Usually, different events have different latency requirements.

Two types of latencies affect the performance of real-time systems:
  1. **Interrupt latency**: referes to the period of time from the arrival of an interrupt at the CPu to the start of the routine that sevices the interrupt. The total time required to perform the completion of the instruction it is currently executing, the interrupt type acknowledgement from the CPU, the sving of the state of the current process before servicing the interrupt using the ISR (interrupt service routine) is the interrupt latency. 
  2. **Dispatch latency**: The amount of time required for the scheduling dispatcher to stop one process and start another. The most effective technique to minimize this latency is to provide preemptive kernles

Real-time operating systems require that interrupts be disabled for only very short periods of time.

The **conflict phase** of dispatch latency has two components:
  1. Preemption of any process running in the kernel
  2. Release by low-priority processes of resources needed by a high-priority process.

Following the conflic pahse the dispatch phase schedules the high-priority process onto an available CPU.

### Priority-Based Scheduling

The most important feature of a real time operating system is to respond immediately to a real-time process as soon as that process requires the CPU.

The scheduler for a real-time operating system must support a priority-based algorithm with preemption.

note that providing a preemptive, priority-based scheduler only guarantees soft real-time functionality.

### Rate-Monotonic Scheduling

The rate-monotonic scheduling algorithm schedules periodic tasks using a **static priority policy** (That is, priorities are fixed) with preemption. If a lower-priority process is running and a higher-priority process becomes available to run, it will preempt the lower-priority process. Upon entering the system, each periodic task is assigned a priority inversely based on its period.

Rate-monotonic scheduling assumes that the processing time of a periodic process is the same for each CPU burst. That is, every time a process acquires the CPU, the duration of its CPU burst is the same.

Rate-monotonic scheduling is considered optimal in that if a set of processes cannot be scheduled by this algorithm, it cannot be scheduled by any other algorithm that assigns static priorities.

Despite being optimal, then, rate-monotonic scheduling has a limitation: CPU utilization is bounded, and it is not always possible to maximize CPU resource fully. The worst case CPU utilization for scheduling N processes is **N(2^(1/N) - 1)**

### Earliest-Deadline-First Scheduling

EDF assigns priorities dynamically according to deadline. The earlier the deadline, the higher the priority; the later the deadline, the lower the priority.

However, whereas rate-monotonic scheduling allows preemption at the beginning of its next period, EDF scheduling allows processes to continue running. Preemption on EDF scheduling only occurs whenever the process currently running has a deadline larger than other process (it lowers the priority of the process dynamically), giving the process with lower deadline, higher priority.

Unlike the rate-monotonic algorithm, EDF scheduling does not require that processes be periodic, nor must a process require a constan amount of CPU time per burst. The only requirement is that a process **announce its deadline** to the scheduler when it becomes runnable.

### Proportional Share Scheduling

Proportional share schedulers operate by allocating T shares among all applications. An application can receive N shares of time, thus ensuring that the application will have N/T of the total processor time.

The sum of all the processor time given to all processes needs not to be equal to T.

Proportional share schedulers must work in conjunction with an **admission-control policy** to guarantee than an application receives its allocated shares of time.
An admission-control policy will admit a client requesting a particular number of shares only if sufficient shares are available


---

# Synchronization

A **cooperating** process is one that can affect or be affected by other processes executing in the system.

Concurrent access to shared data may result in data inconsistency.

A situation where outcome of the execution depends on the particular order in which the access takes place, is called a **race condition**.

## The Critical-Section Problem

A critical section is a segment of code in which a process may be accessing - and updating - data that is shared with at least one other process.

The important feature of the system is that, when one process is executing in its critical section, no other process is allowed to execute in its critical section. That is, **no two processes are executing in their critical section at the same time**.

The critical-section problem is to design a protocol that the processes can use to synchronize their activity so as to cooperatively share data.

Each process **must request permission** to access it critical section. The section of code implementing this request is the **entry section**. The critical section may be followed by an **exit section**. The remaining code is the **remainder section**.

A solution to the critical-section problem must satisfy the following three requirements:

  1. **Mutual Exclusion**: If process P_i is executing in its critical section, then no other processes can be executing in their critical sections.
  2. **Progress**: if no process is executing in its critical section and some processes wish to enter their critical sections, then only those processes that are not executing in their remainder sections can participate in deciding which will enter its critical section next, and this selection cannot be postponed indefinitely.
  3. **Bounded waiting**: There exists a bound, or limit, on the number of times that other processes are allowed to enter their critical sections after a process has made a request to enter its critical section and before that request is granted.

Two general approaches are used to handle critical sections in operating systems:

  1. Preemptive kernels: allows a process to be preempted while it is running in kernel mode. It is more suitable for real-time programming, as it will allow a real-time process to preempt a process currently running in the kernel.
  2. Nonpreemptive kernels: Doesn't allow a process running in kernel mode t obe preempted; a kernel-mode process will run until it exits kernel mode, blocks or voluntarily yields control of the CPU. It is essentially free from race conditions on kernel data structures.

## Hardware Support for Sinchronization

A memory model is how a computer architecture determines what memory guarantees it will provide to an application program.

A memory model falls int o one of two categories:

  1. Strongly ordered, where a memory modification on one processor is immediately visible to all other processors
  2. Wealy ordered, where modification to memory on one processor may not be immediately visible to other processors.

Memory models vary by processor type, so kernel developers cannot make any assumptions regarding the visibility of modification to memory on a shared memory multiprocessor.

Computer architectures provide instructions that can force any changes in memory to be propagated to all other processors, thereby ensuring that memory modifications are visible to threads running on other processors. Such instructions are known as **memory barriers** or **memory fences**.

When a memory barrier instruction is performed, the system ensures that all loads ands stores are completed before any subsequent load or store operations are performed.

Note that memory barriers are considered very low-level operations and are typically only used by kernel developers when writing specialized code that ensures mutual exclusion.

### Hardware Instructions

There are some instructions provided by computer architectures that allow us either to test and modify the content of a word or to swap the contents of two words **atomically**. That is, as one uninterruptible unit.

The test_and_set() and compare_and_swap() are abstractions of what an instruction of this kind would do:

```
  boolean test_and_set(boolean *target) {
    boolean rv = *target;
    *target = true;
    return rv;
  }
```

Example use of test_and_set()

```
do {
  while(test_and_set(&lock)) 
    ; /* do nothing */
    
    /* critical section */
  lock = false
  
  /* remainder section */
} while (true);
```

The important characteristic of the function test_and_set is that it is executed atomically. Thus, if two test_and_set() instructions are executed simultaneously (each on a different core), they will be executed sequentially in some arbitrary order.


```
int compare_and_swap(int *value, int expected, int new_value) {
  int temp = *value;
  if (*value == expected)
    *value = new_value;
  return temp;
}
```

The compare_and_swap() instruction, operates on two words atomically, but uses a different mechanism that is based on swaapping the content of two words.

Example usage of compare_and_swap():

```
while (true) {
  while (compare_and_swap(&lock, 0, 1) != 0)
    ; /* do nothing */
    /* critical section */
  lock = 0;
  /* remainder section */
}
```

Here lock is a global variable. Although this algorithm satisfies the mutual-exclusion requirement, it does not satisfy the bounded-waiting requirement. The following is another algorithm using the compare_and_swap instruction that satisfies all critical section requirements:


```
while (true) {
  waiting[i] = true;
  key = 1;
  while (waiting[i] && key == 1)
    key = compare_and_swap(&lock, 0, 1);
  waiting[i] = false;
  
  /* critical section */
  
  j = (i + 1) % n;
  while ((j != i) && !waiting[j])
    j = (j + 1) % n;
  
  if (j == i)
    lock = 0;
  else
    waiting[j] = false;
  
  /* remainder section */
}
```

Here the common data structures are:

```
boolean waiting[n];
int lock;
```

The elements in the waiting array are initialized to false, and lock is initialized to 0.

### Atomic variables

The compare_and_swap instruction is used on constructing atomic variables, which provide atomic operations on basic data types (int, bool, etc).

It is important to note that although atomic variables provide atomic updates, they do not entirely solve race conditions in all circumstances.

## Mutex Locks

The mutex lock is a high-level software tool to solve the critical section problem.  

The term mutex is short for **Mut**ual **Ex**clusion.

We use the mutex lock to protect critical sections, and thus, prevent race conditions.

A process must acquire the lock before entering a critical section and it releases the lock when it exits the critical section.

```
while (true) {
  acquire_lock();
  /* Critical Section */
  release_lock();
  /* Remainder Section */
}
```

A mutex lock has a boolean variable available whose value indicates if the lock is available or not. If the lock is available, a call to acquire_lock() succeds, and the lock is then considered unavailable. A process that attempts to acquire an unavailable lock is **blocked** until the lock is released.

The definition of acquire_lock() is as follows:


```
acquire_lock() {
  while (!available)
    ; /* busy wait */
  available = false;
}
```
The disadvantage of this implementation is that any process that tries to enter its critical section must loop continuously in the call to acquire().

The definition of release_lock() is as follows:

```
release_lock() {
  available = true;
}
```

Calls to either acquire_lock() or release_lock() must be performed atomically.

Locks are either contended or uncontended:
  * **Contended**: A lock is considered contended if a thread blocks while trying to acquire the lock. Contended locks can experience either high contention or low contention. High contended locks tend to reduce application performance.
  * **Uncontended**: If a lock is available when a thread attempts to acquire it, the lock is considered uncontended. 

The type of mutex lock we have been describing is also called a **spinlock** because the process "spins" while waiting for the lock to become available. Spinlocks do have an advantage though, in that no context switch is required when a process must wait on a lock, and a context switch may take considerable time.

## Semaphores

A semaphore S is an integer variable that, apart from initialization, is accessed only through two standard atomic operations: wait() and signal().

The definition of wait() is as follows:

```
wait(S) {
  while (S <= 0) {
    ; // Busy waiting 
  }
  S--;
}
```

The definition of signal() is as follows:

```
signal(S) {
  S++;
}
```

All the modifications to the semaphore S in wait and signal must be executed atomically. In addition, the testing of the integer value of S (S <= 0), as well as its possible modification (S--), must be executed without interruption.

### Semaphore Usage

There is a distinction between counting and binary semaphores:
  * **Counting semaphore**: Its value can range over an unrestricted domain.
  * **Binary semaphore**: Its value can range only between 0 and 1.

Thus, binary semaphores behave similarly to mutex locks.

Counting semaphores can be used to **control access** to a given resource consisting of a finite number of instances. The semaphore is **initialized** to the number of **resources available**. Each process that wishes to use a resource performs a wait() operation on the semaphore (thereby decrementing the count). When a process releases a resource, it performs a signal() operation (incrementing the count). When the count for the semaphore goes to 0, all resources are being used. After that, processes that wish to use a resource will block until the count becomes greater than 0.

### Semaphore implementation

To prevent busy waiting, we need to change the state of the process to waiting, and wait for the semaphore to be available, that is, its value should be greater than 0.

To implement semaphores under this definition, we define a semaphore as follows:

```
typedef struct {
  int value;
  struct process *list;
} semaphore;
```

When a process must wait on a semaphore, it is added to the list of processes. A signal() operation removes one process from the list of waiting processes and awakens that process.

Now the wait() semaphore operation can be defined as:

```
wait (semaphore *S) {
  S->value--;
  if (S->value < 0) {
    add this process to S->list;
    sleep();
  }
}
```

and the signal() semaphore operation can be defined as:

```
signal(semaphore *S) {
  S->value++;
  if (S->value <= 0) {
    remove a process P from S->list;
    wakeup(P);
  }
}
```

The sleep() operation suspends the process that invokes it. The wakeup(P) operation resumes the execution of a suspended process P. These two operations are provided by the operating system as system calls.

Note that in this implementation, semaphore values may be negative. If a semaphore value is negative, its magnitude is the number of processes waiting on that semaphore.

The list of waiting processes can be easily implemented by a link field in each process control block (PCB).

The list in the semaphore can be managed with any queuing strategy (FIFO, for example).

It is important to admit that we have not completely eliminated busy waiting with this definition of the wait() and signal() operations. Rather, we have moved busy waiting from the entry section to the critical sections of application programs.

## Monitors

## Liveness

One consequence of using synchronization tools to coordinate access to critical sections is the possibility that a process attempting to enter its critical section will wait indefinitely. The processes with such characteristics violates two of the criteria we consider on the critical-section problem: The progress and bounded-waiting. Also, this process is an exampleof a **liveness failure**

Liveness refers to a set of properties that a system must satisfy to ensure that processes make progress during their execution life cycle.

### Deadlock

The implementation of a semaphore with a waiting queue may result in a situation where two or more processes are waiting indefinitely for the execution of a signal() that can be caused only by one of the waiting processes. When such a state is reached, these processes are said to be deadlocked. 



# Deadlocks

## Necessary Conditions

A deadlock situation can arise if the following four conditions hold simultaneously in a system:
  1. **Mutual exclusion**: At least one resource must be held in a nonsharable mode; that is, only one thread at a time can use the resource. If another thread requests that resource, the requesting thread must be delayed until the resource has been released.
  2. **Hold and wait**: A thread must be holding at least one resource and waiting to acquire additional resources that are currently being held by other threads.
  3. **No preemption**: Resources cannot be preempted; that is, a resource can be released only voluntarily by the thread holding it, after that thread has completed its task.
  4. **Circular wait**: A set {T_0, T_1,...,T_n} of waiting thread must exist such that T_0 is waiting for a resource held by T_1, T_1 is waiting for a resource held by T_2, ..., T_n-1 is waiting for a resource held by T_n, and T_n is waiting for a resource held by T_0.

The four conditions are not completely independent. For example the fourth condition implies the second condition. However, it is useful to consider each condition separately.

## Deadlock Prevention

By ensuring that one of the four previous conditions is not met, we can prevent the occurrence of a deadlock.

### Mutual exclusion

The mutual-exclusion condition must hold. That is, at least one resource must be nonsharable.

### Hold and Wait

To ensure that the hold and wait condition never occurs in the system, we must guarantee thaat, whenever a thread requests a resource, it does not hold any other resources.

One protocol that we can use requires each thread to request and be allocated all its resources before it begins execution. Impractical because of the dynamic nature of requesting resources.

An alternative protocol allows a thread to request resources only when it has none.

Both these protocols have two main disadvantages:
  1. Resource utilization may be low, since resources may be allocated but unused for a long period.
  2. Starvation is possible. A thread that needs several popular resources may have to wait indefinitely.

### No preemption

To ensure that this condition does not hold, we can use the following protocol. If a thread is holding some resources and requests another resource that cannot be immediately allocated to it (that is, the thread must wait), then all resources the thread is currently holding are preempted. In other words, these resources are implicitly released. The preempted resources are added to the list of resources for which the thread is waiting. The thread will be restarted only when it can regain its old resources, as well as the new ones that it is requesting.

Alternatively, if a thread requests some resources, we first check whether they are available. If they are, we allocate them. If they are not, we check whether they are allocated to some other thread that is waiting for additional resources. If so, we preempt the desired resources from the waiting thread and allocate them to the requesting thread. If the resources are neither available nor held by a waiting thread, the requesting thread must wait. While it is waiting, some of its resources may be preempted, but only if another thread requests them. A thread can be restarted only when it is allocated the new resources it is requesting and recovers any resources tha were preempted while it was waiting.

This protocol cannot generally be applied to such resources as mutex locks and semaphores, precisely the type of resources where deadlock occurs most commonly.

### Circular Wait

One way to ensure that this condition never holds is to impose a total ordering of all resource types and to require that each thread requests resources in an increasing order of enumeration.

Alternatively, we can require that a thread requesting an instance of resource R_j must have released any resources R_i such that F(R_i) >= F(R_j), where F(resource) is a one to one function that assigns an integer to each resource.

# Synchronization Examples

## Classic Problems of Synchronization

The following problems are used for testing nearly every newly proposed synchronization scheme.

### The Bounded Buffer Problem

It is commonly used to illustrate the power of synchronization primitives. 

In our problem, the producer and consumer processes share the following data structures:

```
int n;
semaphore mutex = 1;
semaphore empty = n;
semaphore full = 0;
```

We assume that the pool consists on n buffers, each capable of holding one item.

Producer Process:

```
while (true) {
  /* produce an item in next_produced */
  wait(empty);
  wait(mutex);
  
  /* add next_produced to the buffer */
  
  signal(mutex);
  signal(full);
}
```

Consumer Process:

```
while (true) {
  wait(full);
  wait(mutex);
  /* remove an item from buffer to next_consumed */
  signal(mutex);
  signal(empty);
  /* consume the item in next_consumed */
}
```

We can interpret this code as the producer producing full buffers for the consumer or as the consumer producing empty buffers for the producer.

### The Readers-Writers Problem

Suppose that we possess a database and we have two functions. One tries to read the database and the other tries to update (that is, it reads and writes to the database). If a reader and a writer access to the database simultaneously, unexpected behaviour may occur. To ensure that this difficulties do not arise, we require that the writers have exclusive access to the shared database while writing to the database. This is referred to as the readers-writers problem. 

This problem has been used to test nearly every new synchronization primitive.

### The Dining-Philosophers Problem

This problem is considered a classic synchronization problem because it is an example of a large class of concurrency-control problems. It is a simple representation of the need to allocate several resources among several processes in a deadlock-free and starvation-free manner.

A solution using semaphores. The shared data are:

```
semaphore chopstick[5];
```

where all the elements of chopstick are initialized to 1. The structure of philosopher i is given as:

```
while (true) {
  wait(chopstick[i]);
  wait(chopstick[(i + 1) % 5)];
  /* eat for a while */
  signal(chopstick[i]);
  signal(chipstick[(i + 1) % 5]);
  /* think for awhile */
}
```

Although this solution guarantees that no two neighbors are eating simultaneously, it nevertheless must be rejected because it could create a deadlock.

Several possible remedies to the deadlock problem are the following.
  * Allow at most four philosophers to be sitting simultaneously at the table.
  * Allow a philospher to pick up her chopsticks only if both chopsticks are available (to do this, she must pick them up in a critical section)
  * Use an asymmetric solution. That is, an odd-numbered philosopher picks up first her left chopstick and then her right chopstick, whereas an even-numbered philosopher picks up her right chopstick and then her left chopstick.


---

# Main Memory


