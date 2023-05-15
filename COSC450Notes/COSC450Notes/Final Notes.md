### Lecture Note One: What is an Operating System

#### Operating System
- **Modern complex computer system:** processor, memory, I/O devices 
- **Operating system:** protected software that provides interface between hardware and software.
- **Hardware:** 
	- Physical devices: wires, power supply, IC chips (CPU, memory, I/O)
	- Micro-architecture: physical devices that are grouped together to form functional units
	- Machine language: executing some set of instructions 
- **Von Neumann** 
	- Von Neumann bottleneck: limitation on throughput caused by the standard personal computer architecture
		- **Throughput:** measure off how many units of information a system can process in a given amount of time
		- Since processor calculation speeds are much faster than data movement between memory and CPU -- causes a bottleneck
	- Programs and data are held in memory; the processor and memory are separate and data moves between the two
- Ways to consider the OS:
	1. **Extended Machine**: provides an interface between user and hahrdware, computer users can use the hardware (CPU, memory, I/O) without knowing the messy details 
	2. **Resource Managers**: computer consists of CPU, RAM, and I/O devices
		1. Process management, memory management, file management, I/O management, deadlock management 

#### A Computer System
- **Instruction cycle:** the CPU's main task is to execute instructions
- **Fetch Cycle:** 
	1. Reading the address of the instruction in PC to be executed from the memory and 
	2. Loading it into the IR
	3. PC is modified to point to the next valid instruction
![[Screenshot 2023-03-31 at 17.44.46.png]]
- **Execute Cycle**:
	1. Contents of the IR are decoded and executed 
	2. The execution may result in a variety of actions (may be self contained, or involve iteraction with the memory and the ALU)

#### History of Operating Systems

1. First generation (1945-1955): vacuum tubes and plugboards 
	- Use vacuum tubes, all programming done in machine language
2. Second generation (1945-1965): transistors and batch system
	- Computers became more reliable
	- Batch system: programmer writes a program on paper, punch program onto cards, bring cards to input room, wait for output
		- Two IBM machines (1401 to read cards onto tape, put tape onto 7094 to do computing)
3. Third generation (1965-1980): IC and multiprogramming
	- Maintaining two computer products was expensive
		- IBM System/360 was made for scientific and commericial purposes 
			- All software, including the OS, had to work on all models 
		- First major computer to use IC 
		- Improve CPU utilization: multiprogramming, spooling, time sharing 
	- **Multiprogramming:** multiple jobs are loaded into RAM and run concurrently 
		- Once CPU became availabale, one job from the ready queue is selected by the short term scheduler
		- OS needs to keep each job's current status in process table
	- **Spooling:** kind of buffering mechanism for a process in which data is temporarily held as a file to be used and executed by a device, program, or system 
	- **Time sharing:** multiple terminals are sharing CPU time 
4. Fourth generation (1980 - present): PC build with LSI, VLSI, and ULSI 
	- Contains thousands of transistors 
	- User types commands from the keyboard
	- Has a GUI
5. Fifth generation (1990 - present): mobile computers 

### Lecture Note Two: Computer System Organization 

#### Computer System Architecture 
- **Modern general-purpose computer system:** one or more CPUs and a number of device controllers connected through a common bus that provides access between I/O devices and shared memory 
		![[Pasted image 20230401155508.png]]
#### General Architecture 
- **Device controller**: Each **device controller** is in charge of a specific type of I/O device 
	- Maintains some local buffer storage and a set of special purpose registers 
	- Device controller is responsible for moving data between the peripheral devices that it controls and its local buffer storage 
	-  Each I/O device has a **device driver** for each device controller 
- **CPU:** the brain of the computer 
	- Each type of CPU has a specific instruction set that can be used for executing each instruction
	- All CPUs contain sets of registers and cache to hold instructions, key variables, and temporary results
		- PC, IR, data registers, stack pointer
	- Fecthes instructions from memory and executes them
	- **Basic instruction cycle executed by CPU:**
		1. Fetch data from memory to register
		2. Decode the instruction
		3. Execute the instruction
	- **Components:**
		- ALU, control unit, cache, registers
		- Registers:
			- General registers (instruction, data)
			- Program counter (address of next instruction - virtual address)
			- Stack pointer (address of top of stack for currently running process)
			- Program status word (saves control information for each process)
	- The OS must know the content of each register for a process
		- When a process stops running by changing states (from running to ready, running to block), the OS saves content for each register for the process in process table which needs to be used to finish its job
	- CPUs might have multiple cores
					![[Pasted image 20230401160801.png]]
- **Interrupts:** when an I/O device is ready to receive or send data through a bus, it interrupts the OS by sending a signal 
	- For I/O, the device driver loads an instruction (read/write) to the device controller's IR
	- The controller starts the transfer of data from the device to its local bugger
	- Once the data transfer is compelte, check any error then the device controller informs the device driver that it is ready to transfer
	- The device driver gives control to the other parts of the OS 
	- Hardware may trigger an interrupt at any time by sending a signal
	- Interrupts are a key part of how the OS and hardware interact
	- When the CPU is interrupted, it stops what it is doing and immediately transfers execution to a fixed location where the service routine for the interrupt is located 
	- The interrupt service routine executes; on completion, it resumes the interrupted computation
	- The OS holds a table of pointers in low memory for holding addresses of interrupt service routines 
	- **Interrupt Implementation:**
		- Hardware: interrupt-request line that senses after every CPU instruction
			- Types: nonmaskable (unrecoverable hardware error) and recoverable (used by device controllers to request service)
		- CPU reads interrupt number and jumps to the routine using the number as an index into the interrupt vector
- **Memory:** the CPU can load instructions only from RAM
	- **Storage Hierarchy:**
		1. Registers in CPU (volatile)
		2. Cache memory
		3. Main memory - RAM
			1. DRAM (dynamic RAM; capacitors)
			2. SRAM (static RAM; logic gates)
		4. Secondary memory (non-volatile)
			1. HDD, SSD, magnetic tape, USB
- **Input/Output Devices**
	- A large portion of OS code is dedicated to managing I/O
	- Form of interrupt-drive I/O is fine for moving small amounts of data, but has high overhead
		- Instead, use DMA (direct memory access) because it can handle I/O independent from the CPU
		- The CPU is freed from invovlement with data transfer, so it speeds up the overall computer operation
	- OS controls all I/O devices by:
			1. Issue commands (read/write) to devices 
			2. Catch interrupts from devices (when devices are ready to send or receive data)
			3. Handle errors
	- OS provides interface between the devices and the rest of the system 
	- Parts of I/O devices: 
		1. Mechanical components (device itself)
		2. Electrical components (device controller)
		3. Device driver (software)
- Buses: common pathway between the CPU and peripheral devices 
	- **Parallel buses:** use slots on the motherboard and provide multiple lines for data between the CPU and the peripheral card
		- Advantage: fast data communication
		- Disadvantage: short distance communication due to crosstalk between the parallel line
		- PCI and AGP 
	- **Serial buses:** have external ports and a cable that plugs into them to connect to multiple devices
		- Disadvantage: slower data communication
		- Advantage: long distance communication
		- USB and FireWire
- **Single Processor System:** one CPU with a single processor core
	- **Core:** component that executes instructions and registers for storing data locally; typically capabale of executing a general instruction set
	- May have other special purpose processors, which run on a limited instruction set 
	- The OS manages these special purpose processors by sending information about their next task and monitors their status
- **Multiprocessor Systems:**
	- Primary advantage: increased throughput. The speed up ratio with N processors is less than N, because of overhead due to communicatining with the CPU
	- Definition of multiprocessor also includes multicore systems
		- More effecient than multiple chips because on chip communication is faster than between chip 
	- **System Types:**
		- **Symmetric Multiprocessing**: each CPU has its own set of registers and possibly cache memory; each CPU performs all tasks including OS functions and user processes.
			- All processors share a common physical memory 
			- Additional CPUs will increase computing power but the system bus can act as a bottleneck
		- **Non-uniform memory access**: each CPU has a local memory that is accessed via a small, fast local bus
			- CPUs are connected and share one physical address space
			- Advtange: when a CPU accesses its local memory, its fast with no contention over system interconnect
			- Potential drawback: increased lentecy when a CPU must access remote memory, possible performance penalty
		- **Clustered systems**: two or more individual systems are connected locally and run as one system
			- High availablility service; service will continue if one or more systems fail
			- **Asymmetric clustering**: one machine is on hot stanby - if one node fails, the hot standby host becomes the active server
			- **Symmetric clustering**: two or more hosts are running applications and monitoring eachother; requires more than one application be available to run
- **Multiprogramming**: OS keeps several processes in memory simulatenously
	- The OS chooses one of the processes and lets it use CPU to execute its job
	- Eventualy, the process may have to wait for some task, such as I/O to complete
	- Multiprogramming increases CPU utilization by organizing programs so the CPU always has one to execute

### Lecture Three: OS as a Resource Manager

- **Process Managment**: The OS is responsible for the following activities for process managment: 
	1. Creating and deleting both user and system processes
	2. Scheduling processes and threads on the CPUs
	3. Suspending and resuming processes and threads
	4. Providing mechanisms for process synchronization (semaphore, mutex, conditional variable)
	5. Providing mechanisms for process communication (PIPE, message queue, shared memory, FIFO, socket)
- **Memory Management:** for a program to be executed, it must be mapped to absolute addresses and loaded into memory
	- As the program executes, it accesses program instructions and data from memory by generating them
	- CPU reads instructions from main memory during the instruction-fetch cycle and both reads/writres data from main memory during the data-fetch cycle
	- The OS is responsible for the following activities for memory managment: 
		1. Keeping trak of what parts of memory are currently being used and which process is using them 
		2. Allocating and deallocating memory space as needed
		3. Deciding which processes (or parts of processes) and data to move in/out of memory
- **FIle Mangement**: OS provides a logical, uniform view of information storage for users to save a file
	- The OS implements the abstract concept of a file by managing mass storage media and the devices that control them 
	- Files are normally organized into directories to make them easier to use
	- The OS is responsible for the following activities for file management: 
		1. Creating and deleting files 
		2. Creating and deleting directories to organize files
		3. Supporting primitives for manipulating files and directories 
		4. Mapping files onto mass sotrage
		5. Backing up files on stable (nonvolatile) storage media 
- **Mass Storage Management**: modern computer system use multiple types of secondary storages 
	- The OS is responsible for the following activities for secondary storage management
		1. Mounting and unmounted
		2. Free space managment 
		3. Storage allocation 
		4. Disk scheduling 
		5. Partitioning 
		6. Protection 
- **Cache Management**
	- Hardware or software component that stores data which might be used against soon
	- Cache hit: the requested data can be found in a cache 
	- Cache management is an important design problem
	- The movement of information between levels of a storage hierachy may be either implicit or explicit; depends on hardware diesgn 
		- Data transfer from cache to CPU and registers is usually a hardware fucntion 
		- Data transfer from disk to memory is usually controlled by the OS
- **Deadlock Management** 
	- Deadlocks between processes happen since limited number of resources must be shared
	- Processes are sharing resources for finishing their job
	- Four strategies for dealing with deadlock
		1. Ignore
		2. Detection and recover 
		3. Dynamic avoidance by careful allocation
		4. Prevention 
	- Four necessary conditions to avoid deadlock:
		1. Mutual exclusion
		2. Circular wait
		3. Hold and wait
		4. No preemptve
- **Input/Output**:
	- OS manages all kinds of I/O devices such as keyboards, monitors, printers
	- I/O subsystems: 
		- A memory mangement components (buffering, spooling, caching)
		- General device drivers
		- Drivers for specific hardware
		- Processor for specific hardware
	- OS controls I/O devices by:
		1. Issue commands to devices 
		2. Catch interrupts from devices 
		3. Handle errors

#### Operating System Structures 
- **Monolithic**: written as a collection of procedures, each of which can call any of the others whenever it needs to
	- Each procedure  in the system as a well-defined interface in tersm of parameters and results, each one is free to call any other one
	- Possible to have some structure for a monolihtic system 
		- Main program, service functions, utility functions
- **Layered Systems:** OS is divided into several layers and each later works on different rules: 
	1. Layer 0: process management 
	2. Layer 1: memory management
	3. Layer 2: inter-process managment
	4. Layer 3: input/output managment 
	5. Layer 4: user program 
	6. Layer 5: systerm operator process
- **Microkernels**: with the layered approacj, the designers have a choice where to draw the kernel-user boundary 
	- Achieve high reliability by splitting the OS up into small well-defined module 
	- Only one module runs in kernel mode and the rest run as users mode
- **Virtual Machine**: runs on bare hardware and does multiprogramming by providing several virtual machines 
	- Each virtual machine copies the bare heardware, including kernel/user mode, I/)
	- Different VMs can run on different OS
![[Screenshot 2023-04-01 at 23.53.41.png]]

### Lecture Note Four: Processes 

- **Process**: program in execution
	- **Memory layout of a process**: 
		1. Text section (executable code)
		2. Data section (global variables)
		3. Heap (used for dynamic allocation)
		4. Stack section (temporary data storage for local variables - function parameters, return addresses, local variables)
- **The process:** assuming that we have one CPU (single core) in the system 
	- **Multiprogramming**: several jobs are loaded into memory, OS simulates pseudo parallelism through virtual memory
	- The OS schedules CPU time for processes by switching from one process to another based on the *scheduling algorithm* and *process state*
					![[Pasted image 20230402135227.png]]
- **The Process Model**: 
	- **Real model**: multiprogramming 
	- **Conceptual (virtual) model**: each process has its own virtual CPU (ALU, PC, registers, stack pointer) and RAM
		- Virtual Machine: each OS runs its own machine
		- Virtual Memory: each process has  its own memory
		- Virtual process model: each process runs on its own machine
	- A process can hold CPU during its time term (amount of time allocated to process, not uniform)
		- Time term might be calculated based on the type of jobs (I/O bounded, CPU bounded, or priority)
- **Process Creation**:
	1. System initialization: when an OS is booted
	2. Created by a running process using a system call
	3. Created by system user by executing a program
	4. Initiation of a batch job
	- Child processes: once a child process is created, both parent and child have their own distinct address, requiring inter-process communication for sharing resources. 
	- systemd pid = 1
	- When a process creates a new process, two possibilities for execution exist: 
		- The parent continues to execute concurrently with its children by using waitpid()
		- The parent waits for some or all of its children to finish executing by wait() 
		- Two address-space possibilities: 
			- The child is a duplicate of the parent process
			- Child process has a new program loaded using exec into it and run 
- **Process Termination**: process may terminate due to the following conditions:
	- **Voluntary**:
		- Normal exit (process finishes its job)
		- Error exit (process itself discoveres a fatal error)
	- **Involuntary**:
		- Fatal error (error cauzed by the process)
		- Killed by another process (when a deadlock is discovered)
	- **Zombie process**: process that has terminated, but whose parent has not yet called wait()
- **Process States**: 
	- **Running state:** process is currently being executed by using the CPU
	- **Block state**: process is waiting for some event to occur (I/O, signal)
	- **Ready state**: process is ready to use CPU but it is not currently available 
			![[Pasted image 20230402140059.png]]
- **Process Table**: when a process is created, the OS stores its run time information in a process table
	- Contains process state, program counter, contents of CPU registers, CPU scheduling information, memory management information, accounting information, and I/O status 
- **Multiple Threads**: most OS have processes that have multiple threads of execution. which allows them to perform more than one task at a time; beneficial on multicore systems, since multiple threads can run in parallel.
- **Process Scheduling** 
	- **Scheduling Queues**: when the CPU becomes available, the short term scheduler seelcts a process from the ready queue
		- Objective of multiprogramming is to maximize CPU utilitzation 
		- Two types of queues to hold pointers to process tables for processes in the block and ready state: 
			- **Ready queue** and **wait queue** 
			- Generally stored as a linked list
		- Events that can occur once a CPU is allocated and executing: 
			1. Process could request I/O and be placed in an I/O wait queue
			2. Process could create a new child process then be placed into wait queue 
			3. Process could be forcibly removed as the result of an interrupt
			4. Process could try and a down semaphore whose value is 0, be placed into semaphore wait queue
		- Scheduling algorithms: shortest job first, shortest remaining time, round robin, priority queue, guaranteed scheduling, lottery scheduling 
	- **Context Switch**: interrupts cause the OS to change a CPU from the current task to run a kernel routine through a context switch, which is performing a state save of the current process and a state load of a process from its process table
		- Pure overhead (CPU must be idle)

### Lecture Five: Interprocess Communication

- Reasons for allowing process cooperation:
	1. Information sharing
	2. Computational speed up
	3. Modularity 
- Fundamental models of interprocess communication:
	- Shared memory: region of memory is shared, OS is only involved in creation but not in synchronization/mutual exclusion
		- Faster than message passing, since shared memory is in user's space after the initial set-up system calls
		- All memory accesses are treated as routine memory accesses, without kernel's assistance; allows processes to access each other's memory without the OS getting involved
		- Other processes that wish to use the shared-memory segment must attach it to their address spaces by key values 
		- **Producer-Consumer Problem with Shared Memory**: 
			- Has a buffer of items that can be refilled by producer and emptied by consumer
			- Buffer resides in shared memory
			- Producer/consumer must be synchronized so the producer doesn't try and overfill the buffer and the consumer doesn't try to consumer from an empty buffer
	- **Message passing**: communication takes place by means of messages exchanged, easy to implement in large systems, OS involved with creation and synchronization 
		- Message queue, FIFO, socket 
		- Allows processes to communicate and synchronize their actions without sharing the same address space
		- A communication link must exist between the processes
			- Direct communication requires each process to know the end point address for sending/receiving messages, while with indirect communication, messages are sent/received from mailboxes
				- Direct communication can be full duplex (both processes have endpoint addresses) or half duplex (send to specific address, receive from any)
				- Indirect communication allows a process to communicate with another process via a bunch of different mailboxes, but two processes can only communicate if they have a shared mailbox
		- **Synchronization**: can be blocking (synchronous - sender/receiver are blocked until message is received/sent) or nonblocking (asynchronous - sender sends message then continues and receiver either receives a valid or null message)
			- Rendezvous: both sender and receiver are blocking
		- Buffering: 
			- Zero capacity (no messages are queued on a link)
			- Bounded capacity (finite length of n messages)
			- Unbounded capacity (infinite length)
- **Overview of Threads**
	- Each thread runs on a different part of a process, with a thread ID, program counter, register set, and a stack.
		- Shares with other threads belonging to the same process its code section, data section, and OS resources, such as open files and signals 
			![[Pasted image 20230402143307.png]]
	- Most OS kernels are multithreaded
	- **Benefits with Threads**: 
		- Resource sharing (share memory and resources of the process that they belong too; allows an application to have several different threads of activity within the same address space)
		- Economy (more economical to create and context-switch threads)
		- Responsiveness (program may continue to run even if part of a program which is run by a thread is blocked)
		- Scalability (benefits are even greater in a multiprocessor architecture)
	- **Multicore Programming with Threads**: provides improved concurrency 
		- Challenges for programming in multicore systems: 
			1. Identifiying tasks
			2. Balance (balanced worked load)
			3. Data splitting (data is accessed and manipulated into separate tasks)
			4. Data dependency 
			5. Testing and debugging 
		-  Types of parallelism 
			1. Data parallelism (distributing subsets of the same data across multiple computing cores)
			2. Task parallelism (distributing tasks/threads across multiple computing cores)

### Lecture Six: Thread Implementation

- **User level threads:** kernel is not aware of threads 
	- Thread library controls creations, destruction and scheduling 
	- Application starts with single thread
	- **Advantage**:
		1. Thread switching does not requre kernel mode privileges 
		2. User level threads can run on any OS
		3. Scheduling can be application specific in user level threads
		4. User level threads are fast to create and manage
	- **Disadvantage**:
		1. Most system calls are blocked 
		2. Multithreaded applications cannot take advantage of multiprocessing
- Kernel level threads: thread managment is done by OS
	- Kernel maintains context information for the process as a whole
	- Scheduling by the kernel is done on a thread basis 
	- Kernel performs thread creation, scheduling, and management in kernel space
	- Advantages: 
		1. Kernel can schedule multiple threads from the same process on multiple processes
		2. If one thread on a process is blocked, the kernel can schedule another thread on the same process
		3. Kernel routines themselves can be multithreaded
	- Disadvantages
		1. Kernel threads are general slower to create and manage than the user threads
		2. Transfer of control from one thread to another within the same process requires a mode switch 
- **Multithreading models**
	- **Many to one**: maps many user-level threads to one kernel threads
		- Thread managment is done by the thread library is user space, efficient 
		- When a thread makes a blocking system call, the entire process will be blocked 
		- Only one thread can access the kernel at a time, multiple threads are unable to run in parallel on multiprocessors 
	- **One to one**: maps each user thread to a kernel thread 
		- More concurrency than many-to-one model by allowing another thread to run when a thread makes a blocking system call 
		- Allows multiple threads to run in parallel
		- A large number of kernel threads may burden the performance of a system 
	- **Many to many**: multiplexes any number of user threads onto an equal or small number of kernel threads 
		- Allows developers to create as many user threads as needed
		- Provides best accuracy on concurrency and when a thread performs a blocking call, the kernel can schedule another thread for execution 

### Lecture Seven: CPU Scheduling 

- **Three Level Scheduler**:
	- **Long Term Scheduler**: selects a process from the job queue and loads it into memory for execution 
	- **Short Term Scheduler**: selects a process from the ready queue and allocates it to the CPU
	- **Memory Scheduler**: schedules which process is in memory and in disk
				![[Pasted image 20230402151942.png]]
- Effective CPU scheduler is essential: process switching is expensive: 
	1. All information for the blocked (became ready state or block state) must be saved (state, register, stack pointer, PC)
	2. The memory map must be saved
	3. New process is selected by the scheduler
	4. Information for the new process must be loaded
	5. Start run new process
- When to make a scheduling decisions: 
	- Short term scheduler:
		- When a process switches from running state to blocked state (nonpreemptive)
		- When a process switches from running state to ready state (preemptive)
		- When a process switches from blocked state to ready state (preemptive)
	- Long term scheduler: 
		- When a process terminates its job, memory space becomes available (preemptive)
	- Under nonpreemptive scheduling, once the CPU has been allocated to a process, the process keeps the CPU until it releases it by terminating or by switching to the block state
- **Scheduling Criteria**
	- CPU utilization: want to keep the CPU as busy as possible 
	- Throughput: number of processes completed per time unit
	- Turnaround time: interval from the time of submission of a process to the time of completion 
	- Waiting time: the sum of the periods spent waiting in the ready queue
	- Response time: the time from submission of a request until the first response is produced
- **First Come, First Serve** 
	- Simplest, nonpreemptive 
	- Managed by FIFO queue; when a process enters the ready queue, its process table is linked to the tail of the queue
	- When the CPU is free, it is allocated to the process at the head of the queue
	- Drawback: average waiting time is long
			![[Pasted image 20230402154450.png]]
- **Shortest Job First**
	- Associates each process with the length of the process's next CPU burst
		- When the CPU is available, it is assigned to the process that has the smallest next CPU burst
		- FCFS is used to break ties 
	- Provably optimal, gives minimal average waiting time
	- Cannot be implemented at the level of CPU scheduling since there is no way to know the length of the next CPU burst. 
		- Approach: try to approximate based off of previous CPU burst time
		$T_{n+1}$ = $a*t_n + (1 - a)*T_n$ 
	![[Pasted image 20230402155436.png]]
		![[Pasted image 20230402154622.png]]
- **Shortest Time Remaining**: preemptive version of the SJF
	- When a new process arrives at the ready queue while the previous process is executing, the CPU is preempted from the process and changes to the ready state 
	- Short term scheduler selects a process from the ready queue; newly arrived process might be shorter than what is left of the current executing process
			![[Pasted image 20230402155745.png]]
- **Round Robin**: similar to FCFS scheduling, but preemption is added to enable to system to switch between processes
	- A small unit of time (time quantum) is defined 
	- No process is allocated the CPU for more than 1 time quantum in a row
	- Implemented in a circular queue
	- Average waiting time is long
			![[Pasted image 20230402155956.png]]
- **Priority Scheduling**: a priority is assigned with each process, the CPU is allocated to the process with the highest priority; each priority processes are done in FCFS basis 
	- Variations: non preemptive priority scheduling, preemptive priority scheduling, priority scheduling with round robin between processes with the same priority
	- Problem: starvation of lower priority processes, who never get CPU time; technique called aging, which gradually increases the priority of processes that have waited in the system for a long time
					![[Pasted image 20230402160256.png]]
					![[Pasted image 20230402160309.png]]
					![[Pasted image 20230402160319.png]]
					![[Pasted image 20230402160329.png]]
- **Priority Scheduling with Multilevel Queue**: 
	- Priority scheduling with a singlue queue has O(n) search time; with multilevel queue, each level maintains a pointer to process tables with the same priority 
	- Works well when priority scheduling is combined with round robin 
	- Processes are permanently assigned to a queue when they enter the system, but with a **multilevel feedback queue**, processes can move in between queues
- **Guaranteed Scheduling**: 
	- Guarantees fairness by monitoring the amount of CPU time spent by each user and allocating resources accordingly 
	- System must keep track of how much CPU time each process has had since its creation 
	- For $n$ processes, each process will receive $1/n$ of the CPU 
- **Lottery Scheduling**
	- When a program starts, a lottery ticket is given to each process; whenever a scheduling decision has to be made, a lottery ticket is selected at random and the process holding that ticket gets the resources 

### Lecture Ten: Interprocess Communication 

- **Three Issues with Interprocess Communication**: 
	1. How can one process pass information to another? (Communication between processes)
		-  Shared memory, message queue, FIFO, PIPE, socket
	1. How to make sure two or more processes do not get into the critical section (mutual exclusion)
		- Mutex, semaphore
	1. Proper sequencing when dependencies are present (synchronization)
- **Race condition**: where two or more processes are reading/writing to some shared data and the final result depends on who runs precisely when
	- How to avoid the race condition? A solution should have the following four conditions: 
		1. No two processes may be simulatenously inside their critical sections (mutual exclusion)
		2. No process running outside its critical section may block other processes
		3. No process should have to wait forever to enter the critical region
		4. No assumptions may be made about speed or the number of CPUS 
- **Critical section**: part of the program where the shared memory is accessed
- **Mutual exclusion in a critical section an avoid race conditions**. 
	- Two approaches for mutual exclusion solutions: 
		1. Busy wait (a process will wait until the resource becomes available or CPU time term expired)
		2. Sleep and wake up (a process checks a resource, if it is not available, go to sleep. When the resource becomes available, the process will be woken up by the system)
- **Mutual Exclusion with Busy Waiting**
	- Each process has a time term; a process keeps checking the possibility to get into the critical section
	- Types: disabling interrupts (non preemptive kernel), lock variables, strict alternation, Peterson's solution, test and set lock (hardware)
	- **Disabling interrupt**: 
		- Once the process gets into the critical section, interrupts set to disable
		- Other processes cannot get CPU time until the process finishes its job in the critical section
		- Since each user process has power to control the interrupt, it might cause the end og the system 
		- EXAMPLE: 
			1. A process gets into the critical section
			2. Disables all interrupts (all other processes are sleeping)
			3. The process is blocked outside the critical section just before it can enable all interrupts again, causing end of system 
	 - **Lock variable**: 
		 - A process can enter in its critical section when lock = 0
		 - Lock = 0 means that no process is currently running in the critical section, set lock = 1 and enter the critical section
		 - Once a process finishes its job in critical section, set lock = 0 and let other processes in the critical section
		 - Lock = 1 means that there is a process running in the critical section, a process does busy waiting until lock = 0
						![[Pasted image 20230402163247.png]]
	- **Strict alternation**: 
		-  Variables can be i or j. 
		- If turn = i, process $P_i$ can go into the critical section
		- Once $P_i$ finishes its job in the critical section, $P_i$ sets turn = j and lets process $P_j$ into the critical section
						![[Pasted image 20230402163523.png]]
	- **Peterson's Solution** 
		- Provides a good algorithmic description of solving the critical-section problem 
		- Peterson's solution is restricted to two or more processes that alternate execution between their critical sections and remainder section
		- The processes are numbered $P_0$ and $P_1$ 
			- When presenting $P_i$ and $P_j$ , and $j = 1-i$ 
						![[Pasted image 20230402163802.png]]
						![[Pasted image 20230402163827.png]]
	- **Test and Set Lock**: 
		- The operations of reading the lock and storing into registers are guaranteed to be individuable 
		- Read the content at the memory address of lock into register RX; store a non zero value at the memory address of lock 
		- When lock = 0, any process may set lock = 1 by using the TSL instruction and go to its critical section; when the process finishes its critical section, set lock = 0 by using the original move instruction
							![[Pasted image 20230402164110.png]]
	- **Memory Barriers**: hardware solution 
		- Two general memory models: 
			1. Strongly ordered memory: a memory modification on one processor is immediately visible to all other processors 
				- Computer architectures provide instructions that can force any changes in memory to be propagated to all other processors 
			2. Weakly ordered memory: a memory modification on one processor may not be immediately visible to other processors 
		- A memory barrier causes the CPU to enforce an ordering contract on memory operations issued before and after the barrier instruction 
						![[Pasted image 20230402164738.png]]
	- **Atomic Variables** 
		- When a thread/process performs an atomic operation, the oother threads see it as its happening instantaneously 
		- Relatively quick compared to locks
		- Only do a limited set of operations; cannot synthesize more complicated operations efficiently 
- **Priority Inversion Problem**:
	- A computer with two processes, $P_h$ with high priorities and $P_l$ with lower priority. The scheduling rules state that $P_h$ gets CPU time whenever it is in the ready state 
		0. At time $T_0$, $P_l$ is in the critical section and $P_h$ is in the block state
		1. At time $T_1$, $P_h$ change state from blocked to ready and try to enter the critical section. $P_l$ is in the critical section
		2. Based on the scheduling rule, the short term scheduler selects $P_h$, $P_h$ holds the CPU and tries to enter the critical section
		3. Since $P_l$ is in critical section, $P_h$ runs busy waiting outside the critical section forever since $P_l$ does not have a chance to get CPU time to finish its critical section 
						![[Pasted image 20230402165317.png]] 

### Lecture Eleven: Mutual Exclusion with Sleep and Wakeup 

- **Sleep and Wakeup**: a process checks a resource (critical section), if not available, goes to sleep. When the resource is available, the process will be woken up by the system or the process releasing the resources
- **Producer-Consumer Problem**: 
	- Description: two processes share a common, fixed-size buffer that the producer puts information into and the consumer takes information out of 
	- Trouble arises: when the producer wants to put a new item in but its full; when the consumer wants to remove an item from the buffer but it is empty
		- Solution: Producer goes to sleep and is awakened by the consumer when it has removed items; consumer goes to sleep and is woken up by the produce when it places items into the buffer
	- Race condition for the producer-consumer problem 
		1. Initially the buffer is empty (count = 0)
		2. The consumer reads count = 0, but since consumer's time slice is over, CPU assigns time to producer 
		3. The producer produces item and checks count = 0, so it places item in buffer and increases count. It then wakes up the consumer, but since it is not sleeping yet, it misses the call
		4. Producer's CPU time is over, so the scheduler assigns CPU time to the consumer
		5. The consumer already read  count = 0, so it goes to sleep 
		6. The producer keeps producing items until the buffer is full, producer goes to sleep 
		- The same can be reproduced for the producer if the buffer is initially full 
					![[Pasted image 20230402170126.png]]
- **Semaphores**: an integer variable which could have the value 0 (no wakeups saved) or +i (wake ups are pending)
	- Can only be accessed through two standard atomic operations (down or up)
	- Modification to the integer value of the semaphore in the down and up operations are indivisibly, which means that when a process is modifying the semaphore value, no process can simultaneously modify that same semaphore value 
	- The OS briefly disables all interrupts while it is testing the semapgore, updating it, and putting the process to sleep 
					![[Pasted image 20230402171023.png]]
	- **Proper usage of semaphores** 
					![[Pasted image 20230402171150.png]]
	- **Careless usage of semaphores** 
					![[Pasted image 20230402171216.png]]
		- In producer, the mutex is downed before the empty semaphore. If empty = 0, meaning the buffer is full, and mutex = 1, then the mutex is downed, then empty is downed. When empty is downed, it will put the producer to sleep before it can up the mutex. When the consumer has CPU time, it will down the full, then try and down the mutex, but since it is already 0, it will be put to sleep. Both processes will be forever sleeping. 
					![[Pasted image 20230402171236.png]]
		- In consumer, the mutex is downed before the full semaphore. If full = 0 and mutex = 1, then the mutex is downed. Following that, the full is downed, which puts the consumer to sleep. The producer's turn is next, but when it does to down the mutex after downing the full, it cannot since the mutex is still 0. So, it goes to sleep and both processes will be sleeping. 
- **Dining Philosophers Problem:** 5 philosophers are sitting at the table, where there are 5 chopsticks. They alternate between thinking and eating, but need two chopsticks to eat. When they are done eating, they put down their chopsticks. They will never give up a chopstick until they hold two and eat.
- **Readers-Writers Problem**: process reader R and writers W are sharing resources at one time; only one process can access the shared resources at any time. 
	- Multiple readers can read at the same time
	- Readers cannot jump ahead of writers 
- **Mutexes**: 
	- When a semaphore's ability to count is not needed, the simplified version of the semaphore called a mutex is used
	- A variable that can be in one of two states: unlocked (0) and locked (1)
- **Monitor**: high level synchronizing primitive 
	- A collection of procdures, variables, and data structures that are all grouped together in a special kind of module
	- One only process can be active in a monitor at any given instance 
	- When a process calls a procedure inside a monitor, check whether the process is active within a monitor or not, and if it is, the calling process will be suspended until the other process has left the monitor 
	- Uses **conditional variables**:
		- Two operations on a conditional variable (wait, signal)
		- When a monitor procedure discovers it cannot continue, it does wait on some condition variable, which causes the calling process to block; the other process can wake up its sleeping partner by doing a signal on the condition variable its partner is waiting on 
					![[Pasted image 20230402172729.png]]
- **Message Passing**: 
	- A method of interprocess communication by using two primitive system calls (send, receive)
	- Usually used between processes located in different systems 
	- Can used sequence numbers and ACK in order to avoid duplicates/lost messages
						![[Pasted image 20230402172959.png]]
## Lecture Twelve: Memory Managment 

### Memory Management 
- **Ideal Memory**: infinitely large, fast, non-volatile 
- **Memory Hierarchy**: 
	- Registers
	- Cache
	- RAM
	- Hard disks

#### Mono-process
- Only memory sharing is between a user program and the OS
- Only one program can be loaded into memory 
- Long term scheduler chooses job from pool of jobs to be loaded into memory

#### Multi-process
- **Fixed partition**: memory is divided into $n$ partitions of possibly unequal size
	- **Separate input queues**: when job arrives, can be put into smallest queue large enough for it 
		- Issue comes from large queues being empty while small jobs are waiting for smaller queues 
	- **Single input queue**: closest queue that fits in it is chosen and loaded into the empty partition
		- Search whole queue and find out what job is a best fit (discriminates against small jobs) --> solution is to create a partition for small jobs and keep a counter to make sure that small jobs aren't skipped too often

#### Modeling Multiprogramming 
- **Probablistic model**: let $p$ be the fraction of time a process is waiting for I/O. If there are $n$ processes, the probability that all processes are waiting for I/O is $p^n$. 
	- **CPU Utilization** = 1- $p^n$ 
- EXAMPLE: 
	A computer has 32MB of memory, the OS takes up 16MB and each program takes up 4MB. 
	So, with a $p$ = 80%, you have about 60% = $1-0.8^4$ of CPU utitlization. 

#### Ways of Memory Management 
- **Swapping**: bring each process in its entirety, running it for a while, then putting it back on disk
	- **Fixed Partition**: the number, location, and size of partitions is fixed. OS knows the address of each process, simple to manage the memory, and the internal fragmentation wastes space. 
	- **Variable Partition**: number, location, and size of partitions vary dynamically based on the size of the process; OS needs to keep track of partition information dynamically for allocation/deallocvation. Can create multiple holes --> solution can be to combine them into one big hole. 
	- How much memory should be allocated for a process when it is created or swapped? 
		- Fixed partition: simple, base off that size
		- Dynamic allocation: using the heap/recursion; you have to allocate extra memory for each process, use adjacent hole to manage growing size. Processes may be swapped out or killed. 


### Free Memory Space Management 
- **Bitmap**: memory is divided into allocation units (*words* that are $k$ bytes). 
	- Corresponding to each of the allocation units is a bit in the bitmap that show whether the unit is free (0) or occupied (1).
	- Size of allocation unit:
		- Smaller allocation = larger bitmap 
		- Large allocation = smaller bitmap, memory may be wasted in the last unit of the process if the allocation units are not a multiple of the process size 
	- **Advantages**: simple way to keep track of words in a fixed amount of memory since size of bitmap depends on the size of memory and the size of allocation units
	- **Disadvantages**: to allocate a process withh $k$ unit size, the memory manager has to search for $k$ consecutive 0 bits in the map
- **Free list**: OS maintains a linked list of allocated and free memory segments
	- Each entry has 4 bits: hole or process, starting address, length, and a pointer to the next entry
	- If the segments are sorted by address, then it is simple to update the list when a process terminates or is swapped out of memory
- EXAMPLE
128MB of memory is allocated in units of 2KB. For the linked list, the memory consists of alternating sequences of segements of processes and holes, each 64KB. Each node in the linked list contains a 32 bit address, 16 bit length, and a 16 bit next node. 

**Bitmap** 

*# of allocation units* = $128MB/2KB$ = $(128 * 2^{20})/(2*2^{10}) = 2^{27} * 2^{11} = 2^{16}$ 
*size of bitmap* = $2^{16}$ bits = $2^{13}$ bytes

**Free list**

*# number of nodes in linked list* = $128MB/64KB$ = $2^{27}/2^{16}$ = $2^{11}$ nodes
*size of each node* = 32+16+16 = 64 bits = 8 bytes = $2^3$ bytes
*total size of linked list* = **number of nodes * size of a node** = $2^{11} * 2^3$ = $2^{14}$ bytes

- Algorithms for memory space allocation with free-list:
	- **First fit**: scans list of segments from the beginning until it finds a whole that is big enough
	- **Next fit**: starts searching the list from where it left off last time
	- **Best fit**: searches entire list and takes the smallest hole
	- **Worst fit**: always takes largest free hole
- Can speed up the algorithms by maintaining two lists, one for the holes that is sorted by size and the other for processes
	- **Quick fit**: takes the separate list for holes which might have a table with $n$ entries, where each entry is a pointer to a head of a certain size ($4KB$ holes, etc.) which means it is quick to find the hole of a required size but it is expensive. 

## Lecture Thirteen: Virtual Memory

### Virtual Memory 

- **Memory sizes in bytes**
	- 1 byte = 8 bits
	- 1 KB = $2^{10}$ bytes
	- 1 MB = $2^{20}$ bytes
	- 1 GB = $2^{30}$ bytes
	- 1 TB = $2^{40}$ bytes
	- 1 PB = $2^{50}$ bytes
	- 1 EB = $2^{60}$ bytes
	- 1 ZB = $2^{70}$ bytes
	- 1 YB = $2^{80}$ bytes

- **Motivation**: the entire logical address space of a process must be in physical memory before the process can access, but since the size of a program grows and physical memory is limited, it is not always possible
- Virtual memory is a technique that allows the execution of processes that may not be entirely in memory
	- OS needs to keep track of the parts of the program currently located in main memory and the rest of the disk
	- Can be used in multiprogramming
- Approaches: 
	- Paging
	- Segmentation
	- Segmentation with paging

#### Paging 
- **Pages**: units of the virtual address space
	- Size is usually between $512$ bytes and $64KB$
- **Page frames**: corresponding units in physical memory 
- Page frame size and page size must be thhe same so you can calculate the physical address from the virtual address
- How to map virtual address to physical memory address: 
	1. PC saves the address of the next instruction (virtual address in virtual space)
	2. Virtual address goes into the memory management unit (MMU) that maps the virtual address onto the physical memory addresses based on the memory map
- **Finding number of pages:** possible virtual space/page size
- **Finding number of page frames:** size of memory/page size


The computer generates a 16-bit address, so the system supports a 64KB virtual space since: 0 ~ $2^{16}-1$ = 0 ~ $64*2^{10}$ . The page size is 4KB and the computer has 32KB memory. 

*Number of pages:* 16 = (64KB/4KB)
*Number of page frames:* 8 = (32KB/4KB)

- EXAMPLE

The computer generates a 32-bute address, so the system supports a 4GB virtual space since: 0 ~ $2^{32}-1$ = $4*2^{30}$ . The page size is 4KB and the computer has 2GB of RAM. 

*Number of pages:* 4GB/4KB = ($4*2^{30}$)/($4*2^{10}$) = $2^{20}$ pages 
*Number of page frames:* 2GB/4KB = ($2*2^{30}$)/($4*2^{10}$) = $2^{19}$ page frames \


MOV REG, 8900;; 

The virtual address 8900 is sent to the MMU. The MMU then calculates the address 8900 belongs to virtual page 2 (8192 ~ 12288). The MMU then checks the memory map such that virtual page 2 maps to physical frame 6 (24576 ~ 28672). 

The MMU then sends the address 24576 + (8900 - 8192) = 25287. This is the physical address. 

**Calculating physical address** = beginning range of physical frame + (location provided - beginning range of virtual frame)

##### Physical Address Mapping 
- How to map virtual address into physical address by the MMU
- EXAMPLE

The computer generates a 16-bit address, with a page size of 4KB. The computer has 32KB of RAM. There are 16 pages and 8 page frames. 

The incoming 16 bit address is split into 4-bit page numbers, since there are 16 pages and a 12-bite offset, since the page size is 4KB. 

The first 4-bits are used as an index into the page table (since $2^4$ = 16). With the 12-bit offset, you can address $2^{12}$ = 4KB within a page.

![[Pasted image 20230328223036.png]]

The first 4 incoming bits are indexed into the table. In the table, there is a value that then becomes the first 4 bits of the outgoing (physical) address. The 12 bits of the original address are then appended to the new first 4 bits. 

- EXAMPLE 

System generates a 32-bit virtual address with a memory size of 1GB and a page size of 4KB. 

Size of the virtual space = $2^{32}$
Number of pages = $2^{32}$/($4*2^{10}$) = $2^{20}$ so the page table has $2^{20}$ entries. 
Number of page frames = ($2^{30}$)/($4*2^{10}$) = $2^{18}$

For example, if the system generates a virtual address 10101111100010101110111111001111, the first 20 bits are used for the index of the page table, and the remaining 12 bits (page size  = 4KB = $2^{12}$) will be used for the offset. Because there are $2^{18}$ page frames, 18 bits must be reserved for saving page frame number in the page table entry. 


### Lecture Fourteen: Page Tables 

#### Page Table
- The purpose of the page table is to map virtual pages onto page frame
- Two major issues: 
	- Page table can be extremely large
	- The mapping must be fast
		- Building a single table consisting of an array of fast, hardware registers, with one entry for each virtual page, indexed by virtual page numbers can be very expensive. 
		- The page table can be entirely in main memory, all of the hardware needs is a single register that points to the start of the page table, but you need one or more memory references to read page table entries. 

#### Page Table with Hardware Support 
- OS maintains a page table per process; a pointer to the page table is stored in the process table.
- When the short term scheduler selects a process for execution, its page table must be loaded into memory. 
- **Hardware implementation:** 
	- Dedicated high-speed registers, which makes the page-address translation very efficient, but it increases context-switch time, since each register must be exchanged during a context-switch
	- Page-table based register, which is used to save a pointer to a page table that is kept in memory and changing page table requires only a chance in the content of the register

##### Translation Look-Aside Buffer
- Small, special, fast-lookup hardware cache with a key and a value. 
- Used with a page table by saving a few of the page table entries
- TLB contains only a few of the page-table entries
	- When a logical address is generated by the CPU, the MMU first checks if its page number is present in the TLB; if it is found, then the frame number is immediately available (hit) and the MMU can map the physical address which is used to access memory
	- If a TLB miss happens, then address translation occurs:
		1. Search page table and get a page frame number to map to the physical address
		2. The search result will be added to the TLB, so that they will be used quickly on the next reference
		3. If the TLB is already full of entries, an existing entry must be selected for replacement based on replacement policies (LRU, round-robin, random)
	- **Effective access time:** (hit ratio)$*$ memory access time + (1-hit ratio)$*$ 2$*$ memory access time


#### Page Table Continued 
- Advantage of paging is sharing common code
#### Structure of a Page Table
- **Multilevel page table:** avoiding keeping all the page tables in memory all of the time
- EXAMPLE: second level page tables

The system generates a 32-bit virtual address with a page size of 4KB. Since you have a 4KB page size, the offset is 12-bits, and the remaining 20 bits are partitioned into a 10-bit PT1 field and a 10-bit PT2 field.

The **first level** is a page table for page tables. Since there are $2^{10}$ entries, there are $2^{10}$ page tables. The present/absent bit is used to save whether the table is in memory or not. 

The **second level** is the page table for pages. Each second level page table has $2^{10}$ entries and since a page size is 4KB, each second level page table supports $2^{10} * 4KB = 4MB$ virtual space. ![[Screenshot 2023-03-29 at 10.13.28.png]]

- **Hashed page table:** one approach for handling address spaces larger than 32 bits; the hash is the virtual page number.
	- Reduces the number of entries in the page table
	- Each entry in the hash table contains a linked list of elements that hash to the same location
	- Each element has three fields (page number, page frame number, pointer to the next element in the linked list)
	- **Algorithm**
		1. Virtual page number in the virtual address is hashed into the hash table
		2. Virtual page number is compared with field 1 in the first element in the linked list
		3. If there is a match, the corresponding page frame is used to form the desired physical address
		4. If there is no match, subsequent entries in the linked list are searched for a matching virtual page number
- **Inverted page table:** contains one page table entry for every frame of the main memory
	- Number of page table entries in the inverted page table is the number of page frames in physical memory
	- A single page table is used to represent the paging information of all of the processes
	- Overhead of storing an individual page table for every process gets eliminated and only a fixed portion of memory is required to store all of the paging information
	- Each entry has a virtual page number, process ID, and control bits 
		- Given the process ID and virtual page number, you can search the inverted page table for a match
		- If the match is found at the $i^{th}$ entry, then this page is located in the $i^{th}$ frame and you can calculate the physical address to access memory.
	- Increases the amount of time needed to search since it's based on physical address but lookups occur based on virtual address. 
		- Can alleviate this problem by using a hash table, though it adds a memory reference (one virtual memory reference requires at least two real memory reads, one for the hash table entry and one for the page table)
	- Can have an issue with shared memory, since there is only one virtual page for every physical page, one physical page cannot have two or more shared virtual pages. 
		- A reference by another process sharing the memory will result in a page fault and will replace the mapping with a different virtual address

## Lecture Fifteen: Page Replacement Algorithms 

### Demand Paging and Page Fault
- **Demand paging:** type of swapping done in virtual memory; the data is not copied from the disk to main memory until they are needed or being demanded by some program. 
	- While a process is executing, some pages will be in memory and some will be in secondary storage
- **Page fault:** a process tries to access a page that was not brought into memory
- **Algorithm for accessing memory:** 
	1. Check the location of the referenced page in the page table
	2. If a page fault occurred, call on the OS to fix it
	3. Using the frame replacement algorithm, find the frame location
	4. Read the data from disk to memory 
	5. Update the page map table for the process
	6. The instruction that caused the page fault is restarted when the process resumes execution
- **Pure demand paging:** after a page is brought into memory, the process continues to execute, faulting as necessary until every page that it needs is in memory
	- It can then execute withh no more faults (never bringing a page into memory until it is required)
	- **Algorithm:**
		1. Trap the OS (control change from process to kernel)
		2. Check page fault or not by checking the page table for the process
		3. Check that the page reference was legal and determine the location of the page on the disk 
		4. Issue a read from the disk to a free frame
		5. Coorrect the page table
		6. Restore process status
		7. The process starts to run the instruction
	- Three major task components of the page-fault service time
		1. Service the page-fault interrupt (save the process status, find the free page frame or find victim page frame)
		2. Read in page
		3. Restart process
	- **Effective access time with page faults:** 
		- $p$: page fault rate
		- $m$: memory access rate
		- $s$: page switch time
		- formula: $(1-p)*m + s*p$
		- Directly proportional to the page fault rate. 
	- Usage of the swap space:  I/O to swap space tends to be faster than to the file system because it is allocated in much larger blocks, and file lookups and indirect allocation methods are not used.

#### Swapping with Paging
- Process instructions and data must be in memory to be executed, but a process or a portion of it may be swapped temporarily uot of a memory to a backing store and then brought back into memory for continued execution.
	- Swapping makes it possible for the total physical memory address space of all processes to exceed the real physical memory
	- Most systems use a version of swapping that takes out individual pages in a process rather than an entire process

#### Free-Frame List
- When a page fault occurs, the OS must bring the desired page from secondary storage into a free page frame in memory
	- OS must create a free frame if there is non
	- Most OS maintains a free-frame list, which is a pool of free frames

#### Copy-on-Write between parent and child
- When using the fork() command, allows parent and child processes to initially share the same pages. 
	- These pages are marked as copy-on-write pages, whichh means that if either process writes a shared page, a copy of the shahred page is created.
- When using vork(), it doesn't use copy-on-write, so if the child proocess modifies any pages of the parent's address space, the modified page will be visible to the parent once it resumes. 

#### Structure of a Page Table Entry

### Page Replacement Algorithms
- When a page fault occurs and there is no free frame: 
	1. Choose a victim page current allocated in a free frame (dependent on replacement algorithmh)
	2. If the page has been modified in memory, rewrite to the disk
	3. A page is allocated into the page frame which was used by the victim page
	4. Change page table
- If no frames are free, two page transfers (one for page out and one for page in) are required 
- A process's memory access can be characterized by a list of page numbers, which is called the **reference string**
- Three items to characterize the paging system: 
	1. The reference string of the executing process
	2. The page replacement algoritmh
	3. The number of page frames available in memory for a process

#### Optimal Algorithm 
- Replace the page that will not be used for the longest time
![[Screenshot 2023-03-29 at 11.44.07.png]]

#### Not Recently Used
- When a page fault occurs, the OS inspects all pages and classifies them into four groups based on the page table information (modified, reference)
	1. Class 0: not referenced, not modified
	2. Class 1: not referenced, modified
	3. Class 2: referenced, not modified
	4. Class 3: referenced, modified 
- Removes a page at randdom from the lowest numbered, non empty class

#### First In, First Out
- Replace the oldest page frame

#### Second Chance
- Simple modification to FIFO that avoids the problem of throwing out a heavily used frame by inspecting the R bit of the oldest page. 
	- If the oldest page's R = 0 and is not referenced, it is removed
	- If the oldest page's R = 1, it is old but referenced, so you set R = 0 and it moved from oldest to newest

#### Clock Replacement Algorithm
- Similar to second change, but you keep all of the page frames in a circular list
- When a page fault occurs, the page the hand is pointing to is inspected
	- If R = 0, evict the page
	- If R = 1, clear R and advance the hand

#### Least Recently Used
- Replace the page that has not bee used for the longest time
- Optimal page replacement looking backward in time
- Requires use of a counter (to save the reference time) and a stack (to keep a stack of page numbers)

### Lecture 16: Modeling Page Replacement Algorithms 

- Belady's Anomaly
	- Initutively, it might seem that the more page frames the memory has, the fewer page faults a program will get. 
	- Belady shows the case with the FIFO: the more page frames the memory has, the more page faults a program has
![[Screenshot 2023-04-20 at 09.42.02.png]]
- Stack Algorithm 
	- A paging system can be characterized by three items: 
		1. The reference string of the executing process
		2. The page replacement algorithm
		3. The number of page frames available in memory 
	- Model for stack algorithm
		- Maintains an internal array M that keeps track of the state of memory
		- M has as many virtual memory pages n
		- Top m entries contain all the pages currently in the memory (page frames)
			- n: # of pages, m: # of page frames
		- Bottom n-m entries contains all the pages that have been referenced but have been swapped out and are not currently in memory
		- Properties of the model:
			1. When a page is referenced, it is always moved to the top entry in M
			2. If the page referenced was already in M, all pages above it moves down one position
			3. A transition from within the box to outside of it corresponds to a page being evicted from the memory
			4. The pages that were below the referenced page are not moved
	- Property of Stack Algorithm 
		- If any page replacement algorithm has the following property, we call it a stack algorithm: 
			$M(m,r)$ subset $M(m+1, r)$
		- If we increase memory size by one page frame and re-execute the process, at every point during the execution, all the pages that were present in the first run are also present in the second run, along with one additional page
		- If a replacement algorithm is a stack algorithm, it does noot suffer from Belady's algorithm 
- Design Issues for Paging System
	- Load control: even though we use the best replacement algorithm with global allocation policies, if the combined working set of all processes exceed the capacity of memory (if the system supports a high degree of multiprogramming with a small main memory), thrashing can be expected
		- Thrashing: two of more processes accing a shared resource repeatedly such that serious system performance degredation occurs because the system is spending a disproportionate amount of time just accessing the shared resource
		- Possible concern is reducing the degree of multiprogramming - swapping out somee of the processes into the disk 
	- Page size: 
		- Smaller page size leads to smaller internal fragementation 
		- Larger size will cause more unused programs to be in memory than a small page size
		- Smaller pages mean that programs will need many pages (hence a large page table)
		- Many pages wastes time for seek and rotation
		- Some system need to load the epage table into the hardware registers to execute - many small pages, need more page tables for loading time
		- Overhead of paging system
			- Page table
			- Internal fragmentation loss 
	- Mathematical analysis: 
		- $S$: average size of a process (bytes)
		- $P$: the size of a page (bytes)
		- $E$: amount each page table entry needs (bytes)

		$S/P$: average number of pages per process
		$(S/P) * E$: average page table space
		$P/2$: the wasted memory in the last page of the process 

		**Total overhead by page table and internal fragementation loss is:** 
		1. Overhead(P): $(SE/P) + P/2$
		2. Overhead'(P): $-(SE/P^2)+1/2 = 0$
		3. Solve for P = $P = sqrt(2SE)$ 
- **Segmentation**
	- Segmentation is a logical entity, which the programmer knows and uses as a logical entity
	- Segmentation is a memory management scheme that supports the user's view of memory
	![[Screenshot 2023-04-20 at 18.39.23.png]]
	- A logical addreess space is a collection of segments; each segment has a name and a length
	- Each segment might have a different size
	- A segment might contain a procedure or an array or a stack or a collection of scalar variables, but usually it does not containa mixture of different types
	- Normally, when the user program is compiled, the compiler automatically constructs segments reflecting the input program
	- Segmentation table saves the size of the segment and base address (beginning of physical addreess) for each segment 
	- EX: any compiler might create separate segments for the following: 
		1. The global variablse
		2. The procedure calls stack to store parameters and return address
		3. The code portion of each procedure or function
		4. The local variablse of each procedure and function
	![[Screenshot 2023-04-20 at 18.48.55.png]]
	- Advantages with segmentation: 
		- Protection: because the segment represents a semantically defined portion of the program, it is likely that all entries in the segment will be used for the same way
			- It is relatively easy to protect a segement 
			- Examples:
				1. In modern architecture, instructions are non-self-modifying, so the instruction segement can be defined as read or execute only; the memory maping hardware check the protection  bit associated with each segment table entry to prevent illegal accesses to memory 
				2. Big array is placed in its own segment; the memory management hardware will automatidally check that array indexes are legal and do not stray outside the array boundaries
		- Sharing: a segment can be shared by several process without allocated segment space for each process
			- Examples:
				- Shared library: modern work stations that can run advanced window systems have extremely large graphical libariese compiled into nearly every program' graphical libraries can be put into a segement
- Segmentation with paging
	- Reasons: 
		- If the segments are large, to keep them in the physical memory might be wasting memory space
		- If a segment's virtual space is larger than its physical space, it is not even possible to keep them in physical memory
		- Solution: each segement is divided into pages
	- MULTICS
		- For each program, a virtual memory of up to $2^{18}$ segments are allowed
		- Each program has a segmentation table
		- Since there are potentially $2^{18}$ entries in the segementation table, a segment table is a segment within the page table
![[Screenshot 2023-04-21 at 14.02.16.png]]
![[Screenshot 2023-04-21 at 14.02.27.png]]
![[Screenshot 2023-04-21 at 14.03.15.png]]
![[Screenshot 2023-04-21 at 14.03.29.png]]
### FIles and Directories 

#### File System

- Three essential requirements for long term information storage: 
	- To store a very large of infomration 
	- To store information permanently
	- To share the information with multiple processes
- File Names: 
	- \<filename>.\<extension>
	- Length: 255
	- UNIX: case sensitive
	- DOS, Windows: not case sensitive  
- File Structure 
	- UNIX, Linux, Windows: file is an unstructured sequence of bytes, OS does not know what is in the file
- FIle Types:
	- Regular files: for user's information 
		- ASCII: line of text; line is terminated by carriage return or special character
		- Binary file: 
			- Executable file (OS can execute a file if it has the proper format)
				- Header: all information regarding execution (magic number -- identifying the file as an executable and size of file, address where execution starts)
				- Text
				- Data
			- Archive file (consists of a collection of library functions compiled but not linked)
				- Data file
	- Directories: system files for maintaining the stucture
- File Access: 
	- Sequential access: a program can read all of the bytes in a file in order, starting from the beginning
		- Read operation changes the poointer to the next location for read
	- Random access: a proocess can read all bytes in a file in any order
		- Seek operation can move the pointer point to the location (HDD, SSD)
- File Operations: create, delete, open, close, read, write, append, seek, get/set attributes, rename

#### Directories

- File systems use directories to keep track of files
- Types of directories: 
	- Single level directory system: one directory containing all files
	- Two level directory system: giving each user a proviate directory; users create a file under their directory; each file name must be unique 
	- Hierarchical directory system: user can create directories to group their files in a logicla way; same file name can be used under a different directory 
![[Screenshot 2023-04-10 at 13.49.21.png]]
- Directory operations: create, delete, open, close, rename, read, link

#### File System Layout

- When the computer is booted: 
	- BIOS reads in and executes MBR
	- The MBR (**master boot record**) locates the active partition
		- Read in the first block (boot block) in a partition and execute is
		- The program in the boot block loads the OS contained in that partiton (active partition)
- System layout of a disk partition: 
	- Super block: key parameters about the file system; magiv number to identify the file system type, the size/number of blocks
	- Free space management 
	- I-Node
	- Root directory
	- Directories and files

#### Implementing Files 

- Disk spaces are divided into blocks and one or more blocks are used to save a file
- Implementing a file: 
	- **Contiguous Allocation**: a file is saved in contiguous blocks
		- Advantages: 
			1. Simple implementatoon: system needs to keep two number for each file in the directory: disk address of the first block and the number of blocks used for the file
			2. Read performance is excellent: the is only one seek needed to read the entire file since the each block is allocated contiguously
		- Drawback: fragmentation; if a newly created file size is fixed, system just finds a big hole to fit the file - if it is not fixed, then there is a problem 
	![[Screenshot 2023-04-11 at 08.08.51.png]]
	- **Linked list allocation**: a block is used for saving data and the next block information (block number)
		- Drawbacks:
			1. Reading a file sequentially is straight forward, but random access is extremely slow 
			2. Since storage per block is not a power of 2, need effort for reading/writing in the block 
	![[Screenshot 2023-04-11 at 14.07.51.png]]
	- **Linked list allocation with File Allocation Table**: block information (which blocks are used for each file) is saved in the FAT
		- All link nformation for each file is written into the FAT which is located in main memory 
		- Primary disadvantage: 
			- Since entire table needs to be in memory all od the time, a lot of space needs to be used
			- EX: 20 GB harddrice with 1KB bloock size, need 20 million entries for FAT. If each entry is 3 bytes, we need 60 MB for the FAT
	![[Screenshot 2023-04-11 at 14.11.09.png]]
	- **Index-Node allocation**: block information for each file is saved in an I-node
		- Lists the attributes and disk addresses 
		- To open a file, a system only needs to load the corresponding i-noode into memory 
		- Issue: the i-node has room for a fixed number of addresses, so what happens when a file grows beyond this limit? 
			- Solution: reserve the last disk address for the address of a block containing more block addresses
	![[Screenshot 2023-04-11 at 14.15.13.png]]
- Different information needs to be saved in the directory for each file depending on how the file is implemented 
	- CA: first block number and size
	- LLA: first block number
	- LLA w/ FAT: just in FAT
	- INA: i-node number

#### Implementing DIrectories 

- Where should attributes for each file be saved? 
	- DOS/Windows: directory entry
	- UNIX: i-node entry
- How to handle different size of file names: 
	1. Set a limit on a file name stored in a directory entry or in an i-node entry (simple, wasting space)
	2. All directory enties start wth the length of the entry followed by a date with a fixed foormat (when a file is deleted/created, there is fragmentation)
	3. Make the directory entries a fixed length and keep the file names together in a heap at the end of the directory
- Searching directories: 
	- If a directory is designed in a linear way, for extremely long directories, linear searching can be slow
	- To speed up the search: 
		- Use hash table in each directory
		- Use cache
![[Screenshot 2023-04-11 at 15.36.52.png]]
### Shared File System and Disk Space Management 

- To share a file, it is convenient to appear simultaneouosly in different directories belonging to different users ![[Screenshot 2023-04-11 at 08.05.04.png]]
- It's convenient but it produces problems when a shared user modifies the shared file ![[Screenshot 2023-04-11 at 08.05.57.png]]
- Solutions (both have problems):
	1. Disk block information for a file is saved not in the directory but in the i-node
	2. A system can create a symbolic link to a shared file; the new file contains the path name of the shared file
- **Shared Files with i-node**
	- Assume that user C creates a file and it is shared with user B
	- i-node number must be saved in both directoories 
	- If the user C removes the shared file then the i-node used for the shared file can be used for the other new file 
	![[Screenshot 2023-04-11 at 08.08.51.png]]
	- Solution: 
		- System check the number of users for a file in the i-node
		- If the number of users is > 0, the file can be retained
		- Problem still: even though C removed a shared file, that space is counted as C's space since it is owned by C; used for counting the quota for C
			- Possible since system may allocate a specific amount of disk space per user
- **Shared Files with Symbolic Link**: 
	- The same problem with i-nodes does not arise since only the real owner has a pointer to the i-node
	- Extra overhead is required 
		- To read or write a file: get a path, parse the path from root 
		- Extra block for an i-node is required for each symbolic link to store only path, wasting disk space
- **Log Structured File Structure**: 
	- CPU speeds have increased dramatically while disk access times (seek times) have only improved slowly
	- Cause more and more applications to become disk-bound
		- Log Structured file system is designed to reduce the impact of this problem
	- Designed for HDD but also used for SSD
	- Based on the assumption that files are cached in main memory
		- DIsk traffic will be dominated by writes
		- Writes all new information to disk in a sequential structure called the log (contiguous blocks)
		- Increases write performance speed by eliminating almost all seeks 
	- The sequential nature of the log also permits much faster crash recovery:
		- UNIX: scans entire disk to restore consistency after a crash
		- LSF: examines thhe most recent portion of the log 
	- Some file systems use a log as a temporary storage to speed up write and crash recovery 
	- LSF system stores data permananently in the log: there is no other structure on disk 
	- It must ensure that there are always large amounts of free space available for writing new data
	- A **segment thread cleaner** continually regenerates empty segments:
		1. Read the summary of the first segment
		2. Check the i-node map to find out it is currently used segment 
		3. If not, it is set as an available segment for write operation 
	- **Basic idea**: structure the entire disk as a log
		- All writes are initially buffered in memory 
		- Periodically, all buffered writes are written to the disk in a single segment at the end of the log
		- A single segment contains i-nodes, directory blocks, and data blocks, all mixed together
	- In a UNIX system, each i-node is at a fixed location disk; in an LSF, each i-node is not at a fixed location, they are written to the log
		- The LSF uses a data structure called an i-node map to maintain the current location of the each i-node for each file
		- Opening a file consists of using the map to locate the i-node for the file 
	- Once an existing file is open for update, a segment of the file is loaded into memory (file cache)
		- It always writes back to the disk at the end of the log
		- Means that the previously updated file will not copy back to the previous location
- **Disk Space Management**: 
	- Disk is always divided into blocks of the same size
	- We need to decide the size of a block: larger block size == internal fragmentation; smaller block size == more seek and rotational delay 
	- **Larger block size**: 
		- Consider disk space efficiency:
			- With 4KB files with 1KB, 2KB, or 4KB per block - no wasting space 
			- With a 4KB file on an 8KB block - 50% wasting soace 
			- With a 5KB file with 1KB, 2KB, 4KB, or 8KB block - 
				- 1KB = 5 blocks = no wasting space 
				- 2KB = 3 blocks = 1KB wasting space 
				- 4KB = 2 blocks = 3KB wasting space 
				- 8KB = 1 block = 3KB wasting space
	- **Small Block Size**: 
		- Each file will consist of many blocks
		- Reading in each block requires more seek and rotation delay in HDD
		- EX: a disk with 1MB per track, a rotation time of 8.33 msec and an average seek time of 5 msec. The time in msec to read a block of k bytes oof the sum of the seek rotation and transfer time. 
			$5 + (2^{20}/k) * 8.33 + 4.165$
			seek time + number of blocks per track * rotation time + initial rotation time (half of rotation time)
- **Free Block Management**
	- System reserves spaces (blocks) to save free block information:
		- Linked list and bitmap 
	- **Linked List** 
		- Each block for free blocks list holds as many free disk block numbers as will fit 
		- EX:
			with a 1KB block size and a 32 bit disk block number
			a block can hold = $8 * 2^{10} / 32 = 256 - 1 = 255$ free blocks (1 slock for next free block pointer)
			16 GB disk has $2^{34}/2^{10} = 2^{24}$ blocks so you need $2^{24}/255 = 65794$ blocks to hold free block numbers 
		- When the free list method is used, only one block of pointers needs to be kept in main memory 
		- When a file is created, the needed blocks are taken from the block of pointers
		- When it runs out, a new block of pointers is read in from the disk
		- When a file is deleted, its blocks are freed and added to the block of pointers in main memory
	![[Screenshot 2023-04-11 at 08.39.54.png]]
	- **BItmap**: a disk with n blocks requires a bitmap with n bits. A free block is represented by a 1 in the map, allocated blocks are represented by 0 
		- EX: 
			16 GB disk, size of block = 1 KB
			there are $2^{34}/2^{10} = 2^{24}$ blocks 
			need $2^{24}$ bits for bitmap = $2^{24}/2^3$ bytes = $2^{11} * 2^{10}$ bytes = $2^{11}$ KB = 2048 blocks for the bit map 
		- EX: 
			**Linked List**
				4 KB block size and a 64 bit disk block number
				a block can hold $8*4*2^{10} / 64 = 512 - 1 = 511$ free block numbers 
				a 2 TB disk has $2*2^{40}/4*2^{10} =2^{41}/2^{12} = 2^{29}$ blocks 
				$2^{29}/511 = 1050628.007$ so you need to reserve 1050629 blocks to hold free block numbers 
			**Bitmap**
				2TB disk has $2^{29}$ blocks
				size of bitmap = $2^{29}$ bits = $2^{29}/2^3 = 2^{26}$ bytes = $2^{16}$ KB 
				$2^{16}KB / 4KB$ = $2^{14}$ = 16384 blocks 

#### DIsk Quota

- The system administrator assigns each user a maximum allotment of files and bloocks
- The OS makes sure that each user does not exceed their quotas
- When a user opens a file, the attributes and disk addresses are located and put into an open file table in main memoory 
- The **quota table** contains the quota record for every user with a currently opened file
- The record is extracted from a quota file on disk for the users whose files are currently open
- When all files are closed, the record is written back to the quota file
![[Screenshot 2023-04-12 at 09.15.51.png]]

#### File System Backup 

- File System Backup Design Issues:
	1. Should the entire file system be backed up or only part of it? 
	2. It is wasteful to backup files that have not changed since the last backuo
	3. Since a big amount of data is typically dumped, it may be desirable to compress the data before writing them to tape or disk 
	4. It is difficult to perform a backup on an active file system - making rapid snapshots of the file system
	5. Backup introduces many nontechnical problems into an organization - security problem 
- **Physical Dump**: 
	-  Start at block 0 of the disk, writes all of the disk blocks onto the output tape or disk in order, stop when it has copied the last one
		- How to handle skipping unused blocks
		- How to handle bad blocks
	- Advanrtage: simplicity, speed
	- Disadvantage: unable to skip selected directories, make incremental dumps, and restore individual files
- **Logical Dump**: starts at one or more specified directories and recursively dump all files and directories found there that have changed since some given base date
	- The dump algorithm maintains a bitmap indexed by the i-node number with several bits per i-node
	- The algorithm is in 4 phases
		1. Begins at the starting directory and examines all entries in it. For each modified file, its i-node is marked in the bitmap. Each directoory is also marked and recursively inspected
		2. Unmarking any directories that have no modified files or directories in them or under them 
		3. All marked directories are dumped
		4. All marked files are dumped 
	![[Screenshot 2023-04-12 at 09.48.01.png]]
	![[Screenshot 2023-04-12 at 09.48.11.png]]
	![[Screenshot 2023-04-12 at 09.48.22.png]]
	![[Screenshot 2023-04-12 at 09.48.30.png]]

### Deadlock
- **What is deadlock?**: several processes are competiting for a finite number of resources; as a process requests resources, if the resources are not available, the process enters a block state. Sometimes a blocked prcesses can never change statee again because the resources it has requested are held by other blocked processes. 
	- Deadlocks between processes occur because there is a limitation on the resources that must be shared 
	- Cannot avoid deadlock without proper management by the OS
- **Deadlocks**: a common problem in multiprocessing systems, parallel computing, and distributed systems, where software and hardware locks are used to handle shared resources and implement process synchronization 
	- EX: in a transactional database, a deadlock happens when two processes each within its own transcation updates two rows of information in the opposite order. 
	![[Screenshot 2023-04-13 at 08.06.21.png]]
- **Resource Allocation Graph**: 
	- This graph consists of a set of vertices V and a set of edges E
	- V is partitioned into two types: 
		- P = {$P_1, P_2, ... P_n$} = set a processes and 
		- R = {$R_1, R_2, ..., R_n$} = set of resources
	- Edge from a process to a resource
		- P --> R denotes a process P requesting resource R and is currently waiting 
	- Edge from a resource to a process 
		- R --> P denotes a resource R being currently held by a process P 
![[Screenshot 2023-04-13 at 08.09.38.png]]
- **Deadlocks (continued)**: there are two processes  ($P_1, P_2$) working on their job and two resources ($R_1, R_2$). Both  ($P_1, P_2$) need both ($R_1, R_2$) to finish their job.
	- $T_0$: $P_1$ requests $R_1$ and is granted
	-  $T_1$: $P_2$ requests $R_2$ and is granted
	- $T_2$: $P_1$ requests $R_2$ without releasing $R_1$; but, since $R_2$ is held by $P_2$, $P_1$ goes to the block state to waite for $R_2$ to release $R_2$ 
	- $T_3$: $P_2$ requests $R_1$ but it had not be released by $P_1$ yet. $P_2$ foes into the blocked state and both proceses will be blocked forever. 
	![[Screenshot 2023-04-13 at 08.15.41.png]]
	![[Screenshot 2023-04-13 at 08.15.52.png]]
- **Resources for a Process**: 
	- **Preemptive resources**: resources that can be taken away from a process without ill effect (ex: memory)
	- **Non-preemotive resources**: resoruces that cannot be taken away from a process that currently owns it until the process finishes with the resource and releases it (ex: printer, CD)
- **Sequence for Resource Use**: 
	- The sequence of events for using a resource: 
		1. Request the resource
		2. If availbale, hold and use the resource 
		3. Release the resource 
	- If a resource is not available when it is requested, the requesting process might be: 
		- Blocked and then awakendd when it is available 
		- Wait a little and try again 
	- **Implementation of resource request, use, and release**: 
		- High system dependent 
		- Usually the request/release of resources are system calls 
		- Request and release can be accomplished through the down and up operations on semaphoers 
		- If a resource is already allocated to a process, the process requesting the resource is added to a queue of waiting for this resource
		- EX: 
			- Let's say there are two processes $P_1, P_2$ and two resources $R_1, R_2$ 
			- Both processes need both resources to finish their job 
			- Each resource is associated with  a semaphore 
![[Screenshot 2023-04-13 at 08.22.36.png]]
![[Screenshot 2023-04-13 at 08.22.48.png]]
- **Deadlock Conditions**: a deadlock can arise if and only if the following four conditions hold simultaneously in a system: 
	1. Mutual exclusion 
	2. Hold and wait
	3. No preemptiion
	4. Circular wait
- **Deadlock Example**
![[Screenshot 2023-04-13 at 08.24.01.png]]
![[Screenshot 2023-04-13 at 08.24.24.png]]
- **Four Strategies of Dealing with Deadlocks** 
	1. Just ignore
	2. Detection and recover
	3. Dynamic avoidance by careful allocation
	4. Prevention by negating one of the four conditions necessary to cause deadlock 

### Deadlock Detection and Recovery 

- **Deadlock Detection and Recovery**: when a process requests a resource and if the resource the available, the system lets the process hold the resource 
	- For each resource allocation, the OS keeps track whether there is a deadlock or not (checks after each resource allocation)
	- When a deadlock is detected by the system, the system takes some action to recover from the deadlock 
	- **Deadlock detection with one resource of each type**: simple, done by keeping a resource allocation graph 
		- A simple graph algorithm which can detect. a cycle in a directed graph can detect a deadlock
			- Simply find a path from the node to the node itself is a cycle 
		 ![[Screenshot 2023-04-13 at 08.28.46.png]]
	- **Deadlock detection with multiple resources of each type**: the OS needs to keep matrices for deadlock detection algorithms 
		1. Existing resource matrix (one dimension): presents how many numbers of resource per each type
		2. Available resource matrix (one dimension): presents how many number of resources per each type are availabale at any moment, since some resources are assigned to processes that are not available
		3. Current allocation matrix (two dimension): $row_i$ in matrix C presents the resources currently held by process $P_1$ 
		4. Request matrix (two dimension): $row_1$ in matrix R presents how many more resources are needed for a process $P_1$ to finish its job 
	![[Screenshot 2023-04-13 at 08.32.44.png]]
		- The deadlock detection algorithm is based on comparing vectors.
			- Let's define the relationship $A \le B$ between two one-dimension vectors A and B, which means that each element of A is less than or equal to a corresponding element of B 
			- EX: A = (1, 2, 0, 2); B = (1, 3, 0, 2). Is $A \le B$ true? Yes
		- **Deadlock detection algorithm**: 
			1. All processes start unmarked 
			2. Look for an unmarked process $P_i$, for which the $i^{th}$ row of R (the resources needed by $P_1$) is less than or equal to A
			3. If such a process is found, add the $i^{th}$ row of C to A, mark the process and go back to step 1. 
			4. If no such process exists, the algorithm terminates
		![[Screenshot 2023-04-13 at 08.37.08.png]]
		- You add the columns of C to its corresponding A value to get E
		- Each row of C corresponds to a process ($P_3$ = third row of C)
- **Recovery from a deadlock** 
	- **Recovery through preemption** 
		- Take a resource from some other process, depends on the nature of the resource 
	- **Recovery through rollback**: 
		- Checkpoint a process periodically, use this save state, restart a process if it is found deadlocked 
	- **Recovery through killing processes**: 
		- Crudest but simplest way of breaking a deadlock
		- Kill one of the processes in the deadlock cycle
		- Other processes get its resources
		- Killed process needs to start from the beginning

### Deadlock Avoidance 

- When a process requests a resource, OS must be able to decide whether granting a resource is safe or not, and only make an allocation when its safe
- Is there any algorithm that can always avoid deadlock by making the right choice all of the time? 
	- Yes, when certain information is available in advance 
- **Safe and unsafe state**: a state is said to be the safe state if it is not deadlocked and there is some scheduling order in which every process can run to competion even if all of them suddenly request their maximum number of resources immediately 
- **Banker's Algorithm for a Single Resource**: 
	- Modeled by the way a small town banker might need to deal with a group of customers
	- What Banker's algorithm does is check to see whether granting the resource leads to an unsafe state or not. If granting the request leads to an unsafe state, the request is denied.
![[Screenshot 2023-04-18 at 08.46.55.png]]
![[Screenshot 2023-04-18 at 08.47.06.png]]

- **Banker's Algorithm for Multiple Resources** 
	- Using the three available matrices for checking a safe state: 
		1. Available resource matrix (A): the number of resources per each type that are available at any given memory, since some resources assigned to processes are not available
		2. Request matrix (R): how many resources are needed for processes to finish their jobs 
		3. Current allocation matrix (C): how many resources are currently held by processes 
	- Algorithm: 
		1. Look for a row of vector R whose unmet resouce needs are smaller or equal to A. IF not such row exists, the system will eventually deadlock since no process can run to completition 
		2. Assume the process of the row chosen requests all of the resources it needs and finishes. Mark that process as terminated and add all its resources to the A vector. 
		3. Repeat steps 1 and 2 until either all processes are marked terminated (safe state) or until a deadlock occurs (unsafe)
- **Deadlock Prevention**
	- Deadlock avoidance is essentially impossible to use for the OS in a complex system, since it requires information about future request which are usually not known. 
	- Deadlock prevention is a method to attack one of four deadlock conditions: 
		1. Mutual exclusion
		2. Hold and wait
		3. No preemption
		4. Circular wait 
	- **Attacking mutual exclusion**: the mutual exclusioon condition must hold for non-preemptive resources such as printer, CD ROM writer
		- Preemptive resources do not require mutual exlcusion such as a read only file 
	- **Attacking hold and wait**: to ensure the hold and wait condition never occurs in the system, we must guarantee that, whenever a threads requests a resource, it does not hold any furthere resources 
		- If a thread or process is currently holding any resources, it needs to release all resources and request all resources 
		- **Approach One:** 
			- Each process requests  all resources before starting execution. If everything is available, all resources (requested by the process) and located and finishes its job
			- If some resources are not available, no resources are allocated to the process
			- Problem with approach one: 
				- Many processes do not know how many resources they will need beefore execution
				- If poossible, Banker's algorithm can be applied 
				- Resources cannot be used optimally
		- **Approach two:** 
			- Allow a process to request resources only when the process has none. To get a new resource, first release all resources currently being held and request all at the same time.
			- Disadvantage: starvation is possible; a process that needed several popular resources may have to wait indefinitely since at least one of the resourcese that it needs is always allocoated to some other process
	- **Attacking No Preemption**: 
		- **Approach One**: 
			- If a process that is holding some resources requests another resource that cannot be immediately allocated, then all resourcees currently being held are preempted 
			- Preempted rersources are intentially released and enter the available resources list 
			- The process will be restarted only when it can regain its old resources as well as the new ones that it is requesting 
		- **Approach Two**
			- If a process requests some resources, first check whether they are available or not
				- If they are: allocate them to the process
				- If they aren't: check whether they are allocated to some other process that is waiting for additional resources
					- If so, preempt the desired resource from the waiting process and allocate them to the requesting process
					- If the resources are not either available or held by the waiting process, the requesting process must wait
		- **Attacking Circular Wait**: 
			- **Approach One**: 
				- A process can only hold one resource
				- If a process needs a second resource, the process needs to release the the first one
				- Sometimes this approach is not acceptable
					- EX. a process needs to copy a huge file from the disk so the process needs to hold a buffer and disk to read/write 
			- **Approach Two**: 
				- Global number is provided to each resource 
				- A proocess can request resources whenever they want, but all requests must be made in numerical order
	
	![[Screenshot 2023-04-19 at 15.51.03.png]]



### Lecture 23: Input/Output Management 

- **Input/Output**
	- OS controls all I/O devices by:
		1. Issue commands to devices (read/write)
		2. Catch interrupts from devices (when device is ready to read or write)
		3. Handle errors 
	- OS also provides interface betweeen the devices and the rest of the system 
- **Input/Output Devices** 
	- I/O devices can be divided into block devices, character devices, and others
	- **Block devices**: 
		- Stores information in fixed size blocks, each block has its own address
		- Common block size ranges from 512 bytes and 32 KB
		- The essential property of a block device is possible to read or write each block independently; needs a seek operation before read/write operation
		- EX: disk, CD ROM, SSD
	- **Character Devices** 
		- A character device delivers/accepts a stream of characters, without regard to any block structurre
		- It is not addressable and does not have any seek operation
		- EX: printers, network interfaces, mouse
	- **Others**: 
		- Clock: can only cause interrupt at a well-defined interval 
		- Memory mapped screen 
- **Device Controllers**: 
	- Most I/O units consist of a mechanical component, an electrical component, and a device driver 
		- Mechanical component: device itself
		- Electrical component: device controller (a chip on the parent board or a printing circuit card that can be inserted into a PCI expansion slot)
		- Device driver (software): interface between OS 
	- A **device controller** is a part of a computer system that plays an important role in the operation of that device; it functions as a bridge between the device and the OS
		- Any device connected to a computer is connected by a plug an d socket, and the socket is connected to a device controller
		- Each device controller has a local buffer and a command reegister; it communicates with the CPU through interrupts 
	- The device controller receives data from a connected device and stores it in a local buffer inside the controller before sending it through the bus. 
		- It then communicates the data with a **device driver**
		- For each **device controller** there is a **device driver** which is the standard intereface through which the device controller communicates with the OS through interrupts 
	- The interface between the controller and device is very low-level. 
		- EX: Disk controller (512 bytes per block)
			- The disk drive sends a serial bit stream starting with: 
				- Preamble (cylinder and sector number, sector size)
				- 4096 bit ($512*8$)
				- Check sum for error checking 
			- Disk controller's job is to: 
				- Convert the serial bit stream into a block of bytes
				- Error checking and error correction
				- Send to main memory 
	- How does the CPU communicate with controller? 
		- Each controller has a few registers used for communicating with the CPU
		- By writing into these registers, OS can command the device to deliver, accept data and to switch itself ono and off (ready to receive/send)
		- By reading from these registers, the OS an learn: what is the device state --> whether it is ready to accept a new command or not. 
		- Devices have a data buffer in the device controller for the OS to read/write from
			- EX: the common way for computers to display pixels on the screen is to have video RAM, which basically just a data buffer available for programs or OS to write into 
		- The issue is how the CPU communicates with control registers and device buffers: 
			- Non Memory Mapped I/O 
				- Each control register is assigned an I/O port number (8 bit or 16 bits)
				- The set of all I/O ports form the I/O port space, only the OS can access it,
				- By using special I/O instruction, the CPU can read in the control registeres and can write the content of a register
					- EX: IN REG, PORT; the CPU can read in control register PORT and store the result in CPU REG
					- OUT PORT, REG; the CPU writes the contents of REG to a control register
				- In non-mapped I/O scheme, the address space for memory and I/O are different
				- DIfferent instruction sets are used for communicate between CPU and meemory and between the CPU and I/O device controller registers
					- EX: 
						- IN R0, 4; reads the contents of I/O port 4 and puts it in R0
						- MOV R0, 4; reads the contents of memory word 4 and puts it in R0 
			- Memory Mapped I/O
				- Each controler register is assigned a unique memory address to which no memory is assigned (for buffer or register)
				- The assigned addresses are usually at the top of the address space 
			-  Hybrid Scheme
				- The memory-mapped I/O for data buffers and separate ports for the control registers is used in the Pentium 
				- When the CPU wants to read a word, either from memory or from the I/O port: 
					- The CPU puts the address on the bus's address line 
					- Asserts a read signal on the bus's control line 
					- The second signal line is used to tell whether the I/O or memory space is needed
					- Either I/O device or memory responds to the request based on the second signal line
			- Pure memory mapped I/O: 
				- Memory spaces are used for data buffer and registers of controller
				- Every memory module and every I/O device compares the address lines to the range of addresses that its services
				- If the addreses falls in its range, it responds to the request 
				- Advantage with memory mapped I/O: 
					- Since a non-memory mapped I/O system needs a special instruction set, the device driver cannot be written by a high level language such as C or C++
					- WIth memory-mapped I/O, the device driver can be written in a high level language 
					- No special protection mechanism is needed for user processes performning I/O - OS just checks wehtheer the user process tries to use the controller's space or not
					- Every instruction (that can reference memory) can also reference the control register
				- Disadvantage with memory-mapped I/O 
					- The trend in modern PCs is to have a dedicated, high speed memory bus between memory and the CPU
					- I/O devices have no way of seeing memory addresses as they go by on the memory bus, so they have no way of responding (need an extra mechanism to solve the problem)
- **Direct Memory Access**
	- Whether the CPU has memory-mapped I/O or not, it needs to addreess the device controllers to exchange data with them
	- The CPU can request data from an I/O controller one byte at a time
	- If the CPU needs to wait for completion of data transfer, it wastes the CPU time 
	- DMA controller can handle I/O independent from the CPU
	- The DMA is a capability provided by some computer bus architectures that allows data to be sent directly from an attached device (such as a disk drive) to the memory
	- The CPU is freed from invovlement with the data transfer, thus speeding up overall computer operation
	- EX: Disk read froom disk to memory without a DMA controller
		1. Disk controller reads in data bit by bit froom the disk until an entire block is in the controller's buffer
		2. It checks the checksum to verify any error
		3. If there is no error, controller causes an interrupt to get a service from the OS
		4. The service transfers data from the controller's buffer to main memory through the bus line
- **Interrupts Revisited**
	- When an I/O device finishes its job, it causes an interruot
	- The I/O device sends a signal for the interrupt service through the bus line
	- This signal is detected by the interrupt controller chip on the parent board
	- If no other interrupts are pending, the interrupt controller processes the interrupt immediately; if the interrupt controller is busy to handle other interrupt, the device is just ignored for the moment. And continues to assert an interrept signal on the bus until it is serviced by the CPU. 
	- The interrupt controller sends a number to the CPU which is used for the index of the interrupt vector
	- The signal from the interrupt controller causes the CPU to stop what it is doing and start handling the interrupt
	- The interrupt vector (table) has pointers where the interrupt service procedures is located
	- The program counter saves the location where the interrupt service procedure is located
	- Shortly after it starts running, the interrupt service procedure acknowledges the interrupt by writing a certain value to one of the interrupt controller's I/O ports which tells the controller that it is free to issue another interrupt. 

### Principle of I/O Software

- **Goal of I/O Software**: 
	1. Device independent: I/O software must be able to access any I/O devices
		- It is up to the OS to take care of the problems caused by different devices, which require different command sequences to read or write (non-memory I/O needs device dependent isntructure set for device driver)
	2. Uniform naming: name of a filee or device should be named with a string or an integer, not dependent on the type of device 
		- EX: in UNIX or LINUX, all disks can be integrated into the file system hierarchy 
			- A USB stick can be mounted on the top of the directory, so a file can copy from any disk to the USB stick with a path name. 
	3.  Error handling:
		- Error must be handled as close to the hardware as possible
		- I/O errors must be detected by the controller or the device itself
		- OS only takes care of upper level errors for I/O access
		- Only checks error by the RAM (through checksum)
	![[Screenshot 2023-05-04 at 08.12.25.png]]
	4. Synchronous vs. Asynchronous Transfer
		- **Synchronous**: blocks CPU until it finishes transfering data from a device to a different device 
		- **Asynchronous**: interrupt-driven transfer; once a transfer starts between devices, the CPU can take care of other processes until the interrupt arrives (ex. DMA)
	5. Buffering: 
		- For some devices, data cannot be stored directly in its final destination
		- It must be saved in a buffer and check errors or decoded proper form and then send to destination
			- Ex: OS cannot directly take care of data coming in off the network until it is stored in a buffer and examined
	6. Sharable vs. Dedicated Device I/O Software
		- Hard disk is able to share with multiple processes (multiple read/write heads for each disk)
		- Tape drive: have to be dedicated to a single user until that user is finished
- Fundamental ways I/O can be performed: 
	1. **Programmed I/O:** the CPU blocks for a job
		- Ties up the CPU full time until all of the I/O is done
		- Simple but has the disavntage of typing up the CPU full time
	![[Screenshot 2023-05-04 at 08.20.10.png]]
	![[Screenshot 2023-05-04 at 08.20.41.png]]
	2. **Interrupt-driven I/O:** CPU blocks for each step of a job
		- If a printer take 10ms to print one character, the CPU will sit in idle for 10ms wiaitng to be allowed to print the next character
		- Interrupt drive I/O lets the CPU do something else while waiting for the I/O device to become ready
		- Disdavantage is that an interrupt occurs on every character, wasting CPU time
	![[Screenshot 2023-05-04 at 08.23.10.png]]
	3. **I/O using DMA:** the CPU informs to the DMA, CPU works on another job. When the job is done, the DMA informs the CPU. The CPU continues the job. 
		- CPU initiates the I/O, the DMA takes over the I/O job, once the I/O finishes, the DMA issues an acknowledge for the interrupt, and the OS unblocks the process waiting for I/O. 
	![[Screenshot 2023-05-04 at 08.25.41.png]]
- Input/Output Software Layer 
	![[Screenshot 2023-05-04 at 08.26.19.png]]
	- Interrupt Handler: or known as an interrupt service routine, is a subroutine in an OS or device driver whpse execution is triggered by the reception of an interrupt. 
		- Have multitudes of functions, which vary based on the reason the interrupt sas generated and the speed at which the interrupt handler completes its task
		- Pressing a key on a computer keyboard, or moving the mouse, triggers interrupts that call interrupt handlers which read the key (aka mouse position) and copies the associated informatiion into the computer's memory 
		- Steps: 
			1. Save any registers for currently running processes
			2. Set up context switch for ISR
			3. Set up stack for the ISR
			4. Acknowledged interrupt controller
			5. Copy registers
			6. Run the ISR
			7. Select which process will run next
			8. Set up MMU context for the process to run next
			9. Load the new process's register
			10. Start running the new process
	- Device driver:
		- Since each I/O device has different roles, the number of registers and commands used in different I/O devices are different
		- A device driver is a device-specific code for controlling an I/O device 
		- A device driver is written by the device manufacturer
		- A device driver is the interface between OS and I/O controller 
		- In order to access the device's hardware, each of the device drivers has to be part of the OS kernel 
			- It is still possible to contruct a driver which runs in the user's space, with systme calls for reading and writing the device drivers (micro-kernel)
		- Modern OS's expect drivers to run in the kernel 
		- OS designer needs to consider an architecture that will allow driver installation on it
		![[Screenshot 2023-05-04 at 08.33.58.png]]
		- Standard interface: OS defines a standard interface for block and character devices 
			- A number of procedures that the rest of the OS can call to get the driver to do work on it 
			- An OS with a single binary program (UNIX) that contains all of the device driveers needs to be recompileded if a new driver is added 
			- In the PC, OS went over to a model where drivers where dynamically loaded into the system during execution
			- Different system uses different ways to load drivers. 
- **Hard Disk Drive (HDD)**
	- Modern disk drives are addressed as a large one-dimensional array of logical blocks
	- Size of the logical blocks is usually 512 bytes
	- At any given arm position, each of the heads can read (by rotation) an annular region called a track
	- All of the tracks for a given arm position form a cylinder
	- Each track is divded into some number of sectors
	- The one-dimensional array of logical blocks is mapped onto the sector of the disk sequentially 
	- You can convert a logical block number into a disk address that contains the cylinder number, a track number, and a sector number. 
	![[Screenshot 2023-05-04 at 08.39.10.png]]
	![[Screenshot 2023-05-04 at 08.39.24.png]]
	![[Screenshot 2023-05-04 at 08.39.35.png]]
	- Disk Scheduling 
		- One of the responsibilities of the OS is to use hardware efficiently
		- WIth disk drivers, the OS tries to minimize the disk access time
		- The major components of the disk access times: 
			- Seek time: time for the disk arm to move the heads to the cylinder containing the desired sector 
			- Rotation time: the time to rotate to the desired sector to the disk head
		- Disk scheduling algorithms are for scheduling the movement of the disk arm between the cylinders 
		- **First Come, First Serve**:
			- Ex. consider a disk queue with requests for I/O to blocks on cylinder 98, 183, 37, 122, 14, 124, 65, 67. If the disk head is initially at cylinder 53: 
				FCFS = 98, 183, 37, 122, 14, 124, 65, 67
				Total head movement: $(98-53) + (183-98) + (183-37) + (122-37)$
				 $+ (122-14) + (124-14) + (124-65) + (67-65)$
				 
			![[Screenshot 2023-05-04 at 08.45.44.png]]
		- **Shortest Seek Time First**: serves the request closest to the current location
			-  Ex. consider a disk queue with requests for I/O to blocks on cylinder 98, 183, 37, 122, 14, 124, 65, 67. If the disk head is initially at cylinder 53: 
				SSTF: 65, 67, 37, 14, 98, 122, 124, 183
				Total head movement: $(65-53) + (67-65) + (67-37) + (37-14)$
				 $+ (98-14) + (122-98) + (124-122) + (183-124)$
				 
			![[Screenshot 2023-05-04 at 09.20.45.png]]
		- SCAN: elevator scheduling; the disk arm starts at one end of the disk, and moves towards the other end 
			- Ex. consider a disk queue with requests for I/O to blocks on cylinder 98, 183, 37, 122, 14, 124, 65, 67. If the disk head is initially at cylinder 53: 
			Elevator: 37, 14, 0, 65, 67, 98, 122, 124, 183
			![[Screenshot 2023-05-04 at 09.23.44.png]]
		- C-SCAN: similar to elevator algorithm, move the head from one end of the disk to the other, when the head reaches the other end, it immediately retirns to the beginning of the disk without serving any of the requests on the the return trip. 
			- Ex. consider a disk queue with requests for I/O to blocks on cylinder 98, 183, 37, 122, 14, 124, 65, 67. If the disk head is initially at cylinder 53: 
			C-SCAN: 65, 67, 98, 122, 124, 183, 199, 0, 14, 37
			![[Screenshot 2023-05-04 at 09.26.36.png]]
		- LOOK: both elevator and C-SCAN move the disk arm across the full width of the disk. In practice, neither algorithm is implemented in this way. More commonly, the arm goes only as far as the final request in each direction, then it reverses direction immediately  without going all the way to the end of the disk 
			- This version of SCAN and C-SCAN algorithm are called LOOK and C-LOOK 
		![[Screenshot 2023-05-04 at 09.36.24.png]]
		![[Screenshot 2023-05-04 at 09.36.34.png]]
- **DIsk Management**
	- Disk formatting: the format consists of a series of concentric tracks, each track containing sectors, with short gaps between sectors 
		- Preamble: start with bit pattern, the cylinder number, sector number, and other information 
		- Data: size of data portion is determined by formatting program (512 bytes)
		- ECC: error correcting code; information used for error correction 
	- Bad Block Management: bad blocks should be managed by the OS 
		- MS-DOS (windows): *format* command does a logical format, scans the disk to find a bad block, and if one is found, a special value is written into the corresponding file allocation table entry 
		- SCSI (Small Computer System Interface) disk: used in workstations and servers
			- The disk controller maintains a list of bad blocks on the disk
			- The list is initialized during the low-level format at the factory
			- It is updated over the life of of the disk 
	- Swap Space Management: 
		- The swap space is used in various ways by different OSs. Based on the memory management algorithm. 
			- Swapping: need to swap entire process space
			- VIrtual memory: paging, segementation, segementation with paging
		- Swap space location: the location can be sarved out of the normal file system
			- Can be in separate partition 


### Solid State Driver 

- **Structure of an SSD**
	- Solid state drives are a flash-memory based data storage device. Bits are stored into cells, which are made of floating-gate transistors.
	- SSds are made of entirely electronic componnets, there are no mechnical components like in hard disk drivese
	- Voltages are applied to the floating-gate transistors, which is how bits are read, written, and erased; two solutions exist for writing transistors: 
		- NOR flash memory
			- Larger cell size
			- Faster to read
			- Slower to erase and write
			- Much more expensive 
			- Need more power consumption when power is on
			- Used in mobile phones, scientific instructments and medical devices 
		- NAND flash memory 
			- Their cells are wearing off, have limited lifespan
			- Smaller cell size
			- Slow to read
			- Faster to erase and write
			- Less expensive
			- Higher memory capacity 
			- Has lifespan 
			- Used in devices to which large files are frequently uploaded and replaced 
	- Each cell has a maximum number of program/erase cycles
	- NAND flash memory wears off and has a limited life span; the livespan of SSDs can be tremendously increaed 
	- Types of cells: 
		- Single level cell: transistors can only store 1 bit but have a longer lifespan 
		- Multiple level cell: transistors can store 2 bits, at the higher cost of higher latency and reduced lifespan compared to SLC
		- Triple level cell: transistors can store 3 bits, but at an even highere latency and reduced life span.
	- Having more bits for the same amount of transistors reduces the manufacturing costs. SLC based SSDs are known to be more reliable and have a longer life expectancy than MLC-based SSDs, but at a higher manufacturing cost. 
	- **NAND - Flash Pages and Blocks** 
		- Cells are grouped into a grid, called a block, and blocks are grouped into planes
		- The smallest unit through which a block can be read or written is a page
		- Pages cannot be erased individually, only whole places can be erased
		- The size of a NAND-flash page size can vary, most have sizes of 2KB, 4KB, 8KB, or 16KB.
		- Most SSDs have blocks of 128 or 256 pages, which means that the size of a block can vary between 256KB and 4MB. 
	![[Screenshot 2023-05-08 at 15.56.14.png]]
- **Architecture of an SSD**: 
	- Commands (read/write) come from the user through the host interface
	- The processor in the SSD controller takes the commands and passes them to the flash controller. 
	- SSDs have embedded RAM memory, generally for caching purposes and to store mapping information (mapping information)
	- The packages of NAND flash memory organized in gangs, over multiple channels 
- **Basic Operations in SSD (read, write, erase)**: 
	- Due to the organization of NAND-flash cells, it is not possible to read or write single cells individually
	- Memory is grouped and is accessed with very specific properties 
	- Reads are aligned on page size:
		- It is not possible to read less than one page at once
		- One can request just one byte from the OS, but a full page will bee retrived in the SSD, forcing a lot more data to be reead than necessary (read amplification)
	- Writes are aligned on page size: 
		- When writing to an SSD, writes happeen by increments of the page size. So even if a write operation affects only one byte, a whole page will be written anyway
		- Writing more data than necessary is write amplification
	- Pages cannot be overwritten
		- A NAND-flash page can be written to only if it is in the "free" state
		- When data is changed, the content of the page is copied into an internal register, the data is updated, and the new version is stored in a "free" page, an operation called "read-modify-write"
	- Erases are aligned on block size 
		- Pages cannot be overwitten, once they become stale, the only way to make them free again is to erase them
		- It is not possible to erase individual pages, and it is only possible to erase whole blocks at once - one page modification in a block needs the entire block moving to a free block
		- The erase command is triggered automatically by the garbage collection process in the SSD controller when it needs to reclaim stale pages to make free space 
		![[Screenshot 2023-05-08 at 16.54.12.png]]![[Screenshot 2023-05-08 at 16.54.48.png]]
	- **Write Amplification**: 
		- Since writes are aligned on page size, any write operation that is not both aligned on the page size and a multiple of the page size will require more data to be written than necessary, a concept called write amplification
		- Writing one byte will end up writing a page, which can amount up to 16KB from some models of SSD and be extremely inefficient 
		- Writing data in an unaligned way causes the pages to be read into cache before being modified and written back to the device, which is slower than directly writing pages to the disk.
		- This operation is known as read-modify-write
		- Never write less than a page
			- Avoid writing chunks of data that are below the size of a NAND-flash to minimize write amplification and prevrent read-modify-write operations
			- To maximize throughput, whenever possible, keep small writes into a buffer in RAM and when the buffer is full, perform a single large write to batch all of the small writes. 
	- **Wear leveling**:
		- NAND-flash cells have a limited lifespan due to their limited number of process/execute cycles
		- What if data was always read and written from the exact samt block; this block would quickly exceed its P/E cycle limit, wear off, and the SSD controller would mark it as unuseable 
		- One of the main goals of an SSD controller is to implement wear leveling by careful block selection for writing, which distributes P/E cycle as evenly possible among the locks by selecting blocks carefully when writing
		- Ideally, all blocks would reach their P/E cycle limits and wear off at the same time
- **Flask Translation Layer**
	- The main factor that made adoption of SSDs so easy is that they use the same host interface as HDDs
	- Although presenting an array of Logical Block Addresses (LBA) makes sense for HDDs as their sectors can be overwritteen, it is not fully suiteed to the way flash memory works 
	- For this reeason, an additional component is required to hihde the inner characteristics of NAND flash memory and expose only an array of LBAs to the host
	- This component is called the Flash Translation Layer and resides in the SSD controller; the FLT is critical and has two main purposes: logical block mapping and garbage collection
	- **Logical block mapping**:
		- Translates the logical block addresses from the host space into physical block addresses (PBA) in the physical NAND-flash memory space
		- This mapping takes the form of a table, which for any LBA gives the corresponding PBA
		- This mapping table is stored in the RAM of the SSD for speed of access, and is persisted in flash memory in case of power failuree. When the SSD powers up, the table is read from the persisted version and is reconstructured into the RAM of the SSD.
		![[Screenshot 2023-05-08 at 17.34.21.png]]
		- The naive approach is to use page-level mapping to map any logical page from the host to the physical page
			- Allows for a lot of flexibility; mapping table could be extremely large, which increases the manufacturing costs (needs larger RAM)
			- A solution to that would be to map blocks instead of pages, using block-level mapping 
			- An SSD drivehas 256 pages per block withh block-level mapping requires 256 times less memory than page-level mapping, which is a huge improvement for space utilization
			- Mapping still needs to be persisted on disk in case of power failure, and in case of workloads with a lot of small updates, full blocks of flash memory and will be written whereas pages would have been enough; increases write amplification and makes block-level mapping widely inefficient 
		- **Log Block Mapping**: trade off between page-level mapping and block-level mapping is one of performance versus space 
			- The log block mapping uses a hybrid approach between page-level and block-level mapping which uses an approach similar to log-structured file system 
			- Incoming write operations are written sequentially to log blocks; when a log block is full, it is merged with the data block associated too the same logical block number into a free block
			- Only a few log blocks need to be maintained, whiich allows to maintain them with page granularity. 
	- Garbage Collection
		- Since pages cannot be overwritten, if the data in a page has to be updated, the new version is written too a free page, and the page containing the previous version is marked safe
		- When blocks contain stale pages, they need to be erased before they can be written to 
		- Because the high latency required by the erase command compared to the write command, this extra erase step incurs a delay which makes the write slower
		- Some controllers implement a background garbage collection process, which takes advantage of idle time and runs regularly in the background to reclaim stale pages and ensure that future foreground. 